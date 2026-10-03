# 消防队模式详细设计

## 问题诊断

### 盘古反馈的问题
1. **子任务状态不明确** - "仍在运行中" / "已经获取了所有8个批次"
2. **运行缓慢** - 子任务执行效率低
3. **反馈循环** - 陷入检查→等待→再检查的循环

### 根本原因
```
旧模式（有问题）：
盘古（主）→ 生成子任务 → 等待子任务完成 → 获取结果 → 继续
              ↑___________________________________________|
                          （阻塞等待，轮询检查）
```

**问题：**
- 主agent阻塞等待子agent完成
- 子agent生命周期长，容易出错
- 没有清晰的检查点机制
- 批处理粒度太大

---

## 消防队模式设计

### 核心原则

1. **子Agent = 消防员**
   - 拿到任务
   - 执行（<5分钟）
   - 写结果到文件
   - 标记检查点
   - 立即退出

2. **主Agent = 调度中心**
   - 接收任务
   - 拆解子任务
   - 并行触发子agent
   - 不等待，立即返回
   - 异步验收结果

3. **检查点 = 任务看板**
   - 文件系统存储
   - 所有人可见
   - 可恢复、可追踪

---

## 执行流程详解

### 场景：采集飞书文档数据

**Step 1: 女娲下达任务**
```
女娲 → 盘古："采集Batch 4-8，每批100条"
```

**Step 2: 盘古拆解任务**
```
盘古分析：
- Batch 4: 400条 → 4个子任务（每批100条）
- Batch 5: 400条 → 4个子任务
- ...

生成子任务列表：
- task_1: batch4_part1 (记录0-99)
- task_2: batch4_part2 (记录100-199)
- task_3: batch4_part3 (记录200-299)
- task_4: batch4_part4 (记录300-399)
- ...
```

**Step 3: 盘古触发子Agent（不等待）**
```
盘古操作：
1. 为每个子任务生成agent配置
2. 并行触发所有子agent
3. 立即返回女娲："已触发16个子任务"
```

**Step 4: 子Agent执行（并行）**
```
子Agent 1: 
  → 读取 batch4_part1 配置
  → 调用飞书API获取记录0-99
  → 提取结构化数据
  → 写入 batch4_part1.jsonl
  → 写入 checkpoint/batch4_part1.json
  → 立即退出

子Agent 2:
  → 同上，处理记录100-199
  → ...
  → 立即退出
```

**Step 5: 女娲异步验收**
```
女娲操作：
1. 等待30秒（让子agent先跑）
2. 读取 checkpoints/ 目录
3. 统计已完成：12/16
4. 检查未完成：task_5, task_9, task_13, task_16
5. 决定是否重试
```

---

## 关键代码模式

### 子Agent代码模板

```python
# 子Agent： firefighters/batch_processor.py

def main():
    # 1. 读取任务参数
    task_config = load_task_config()
    batch_id = task_config['batch_id']
    doc_token = task_config['doc_token']
    start = task_config['start']
    end = task_config['end']
    
    # 2. 执行采集
    records = []
    for i in range(start, end):
        record = fetch_feishu_record(doc_token, i)
        records.append(record)
    
    # 3. 写入文件
    output_file = f"output/{batch_id}.jsonl"
    write_jsonl(output_file, records)
    
    # 4. 标记检查点
    checkpoint = {
        'batch_id': batch_id,
        'status': 'completed',
        'count': len(records),
        'file': output_file,
        'timestamp': now()
    }
    write_checkpoint(f"checkpoints/{batch_id}.json", checkpoint)
    
    # 5. 立即退出
    print(f"✅ {batch_id} completed: {len(records)} records")
    exit(0)
```

### 盘古Dispatcher代码模板

```python
# 盘古：dispatcher.py

def dispatch_tasks(batch_range, doc_token):
    # 1. 拆解任务
    tasks = []
    for batch_id in batch_range:
        total_records = get_batch_size(batch_id)
        num_parts = (total_records + 99) // 100  # 每批100条
        
        for part in range(num_parts):
            task = {
                'batch_id': f"{batch_id}_part{part}",
                'doc_token': doc_token,
                'start': part * 100,
                'end': min((part + 1) * 100, total_records)
            }
            tasks.append(task)
    
    # 2. 触发子Agent（不等待）
    for task in tasks:
        spawn_subagent(
            agent_id='batch_processor',
            task=task,
            wait=False  # 关键：不等待
        )
    
    # 3. 立即返回
    return {
        'status': 'dispatched',
        'total_tasks': len(tasks),
        'checkpoints_dir': 'checkpoints/'
    }
```

---

## 故障恢复

### 场景：子Agent崩溃

```
女娲检查检查点：
- 已完成：12/16
- 未完成：task_5, task_9, task_13, task_16

操作：
1. 读取未完成任务的配置
2. 重新触发这4个子agent
3. 再次检查
```

### 检查点文件示例

```json
// checkpoints/batch4_part1.json
{
  "batch_id": "batch4_part1",
  "status": "completed",
  "count": 100,
  "file": "output/batch4_part1.jsonl",
  "timestamp": "2026-04-11T14:30:00+08:00",
  "checksum": "md5:xxx"
}

// checkpoints/batch4_part2.json
{
  "batch_id": "batch4_part2",
  "status": "failed",
  "error": "API rate limit",
  "timestamp": "2026-04-11T14:31:00+08:00"
}
```

---

## 最佳实践

1. **批大小：100条**
   - 执行时间 < 5分钟
   - API限制友好
   - 失败重成本低

2. **检查点粒度：每个子任务一个文件**
   - 便于追踪
   - 便于重试
   - 避免文件锁

3. **子Agent生命周期：短**
   - 不做汇总
   - 不汇报进度
   - 立即退出

4. **主Agent策略：异步**
   - 不阻塞
   - 批量触发
   - 定期检查点

---

## 总结

消防队模式 = 
- 子Agent短时执行（<5分钟）
- 主Agent异步调度（不等待）
- 检查点文件驱动（可恢复）

解决：状态不明、运行缓慢、反馈循环
