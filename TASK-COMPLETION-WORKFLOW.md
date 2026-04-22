# 任务执行闭环管理 v1.0

> 整合：图书馆记忆法 + 消防队模式 + 确认→固化→验证闭环

---

## 🎯 核心理念

**记住 ≠ 执行，确认 → 必须 → 固化 → 验证**

当我们确认一个规则/格式/配置时，需要同时更新：
1. **执行配置**（cron job、脚本参数、环境变量）
2. **短期记忆**（检查点文件）
3. **长期记忆**（MEMORY.md、SKILLS-INDEX.md）
4. **验证测试**（立即执行一次）

---

## 📚 图书馆记忆法应用

### 索引文件结构
```
workspace/
├── memory/
│   ├── index.md                    # 轻量索引（关键词→文件映射）
│   └── topics/
│       ├── task-completion-workflow.md   # 本文档（按需加载）
│       ├── daily-report-format.md        # 日报格式规范
│       └── config-standards.md           # 配置标准汇总
├── SKILLS-INDEX.md                 # 技能索引
└── MEMORY.md                       # 长期记忆（关键配置区域）
```

### 按需加载规则
| 触发关键词 | 加载文件 |
|-----------|---------|
| 任务完成 / 检查清单 / 收尾 | `topics/task-completion-workflow.md` |
| 日报格式 / 格式规范 | `topics/daily-report-format.md` |
| 配置更新 / cron / 定时任务 | `topics/config-standards.md` |
| 闭环 / 固化 / 验证 | `topics/task-completion-workflow.md` |

---

## 🚑 消防队模式应用

### 任务拆解：<5分钟子任务

任何"确认→固化→验证"流程拆解为：

| 阶段 | 子任务 | 预估时间 | 检查点文件 |
|------|--------|---------|-----------|
| 确认 | 识别配置影响范围 | 2min | `checkpoints/confirmation/{task-id}-impact.json` |
| 固化 | 更新执行配置 | 3min | `checkpoints/confirmation/{task-id}-config.json` |
| 同步 | 更新记忆文档 | 2min | `checkpoints/confirmation/{task-id}-memory.json` |
| 验证 | 执行测试并验证 | 3min | `checkpoints/confirmation/{task-id}-verify.json` |

### 检查点文件格式
```json
// checkpoints/confirmation/daily-report-format-20260414.json
{
  "task_id": "daily-report-format-update",
  "date": "2026-04-14",
  "status": "completed",
  "phases": {
    "confirmation": {
      "completed": true,
      "timestamp": "2026-04-14T09:05:00+08:00",
      "summary": "确认日报9字段格式"
    },
    "solidification": {
      "completed": true,
      "timestamp": "2026-04-14T09:10:00+08:00",
      "actions": [
        "更新 cron job payload",
        "创建 topics/daily-report-format.md"
      ]
    },
    "verification": {
      "completed": true,
      "timestamp": "2026-04-14T09:15:00+08:00",
      "test_result": "pass",
      "notes": "手动验证格式正确"
    }
  },
  "files_updated": [
    "cron job a23bbe0d-089f-477a-a372-9b42fcad4006",
    "topics/daily-report-format.md"
  ]
}
```

---

## 🔄 确认→固化→验证 标准流程

### 触发条件（任一满足即触发）
- 用户说："确认"/"就这么定了"/"以后按这个来"
- 讨论同一话题超过 3 轮
- 涉及：配置变更 / 格式标准 / 筛选规则 / 自动化任务

### 执行流程

#### Phase 1: 确认（Confirmation）
```bash
# 1. 明确确认内容
echo "确认内容: [具体描述]"
echo "影响范围: [哪些系统/任务受影响]"

# 2. 创建检查点
cat > checkpoints/confirmation/{task-id}-impact.json << 'EOF'
{
  "phase": "confirmation",
  "content": "...",
  "impact_systems": ["cron", "daily-report", "..."]
}
EOF
```

#### Phase 2: 固化（Solidification）
```bash
# 3. 更新执行配置
# 如果是 cron job:
cron update <job_id> patch.payload.message="新配置..."

# 如果是脚本参数:
# 更新 scripts/config.json

# 4. 更新记忆文档
# 4.1 更新 MEMORY.md（关键配置区域）
# 4.2 更新 memory/index.md（关键词映射）
# 4.3 创建/更新 topics/{topic}.md（详细规范）

# 5. 记录检查点
cat > checkpoints/confirmation/{task-id}-config.json << 'EOF'
{
  "phase": "solidification",
  "config_updated": ["cron job", "MEMORY.md", "topics/xxx.md"]
}
EOF
```

#### Phase 3: 验证（Verification）
```bash
# 6. 立即测试
# 如果是 cron job:
cron run <job_id>

# 如果是脚本:
# bash scripts/test.sh

# 7. 验证结果
# 检查输出是否符合预期
# 如果有问题，回滚并修复

# 8. 记录检查点
cat > checkpoints/confirmation/{task-id}-verify.json << 'EOF'
{
  "phase": "verification",
  "test_result": "pass/fail",
  "notes": "..."
}
EOF
```

#### Phase 4: 归档（Archive）
```bash
# 9. 合并检查点
cat > checkpoints/confirmation/{task-id}-complete.json << 'EOF'
{合并所有 phase 的检查点}

# 10. 更新任务追踪
echo "$(date) - [task-id] 已完成固化与验证" >> checkpoints/task-tracking.md

# 11. 在 MEMORY.md 标记 ⭐
# 在相关章节添加："✅ 2026-04-14 已固化并验证"
```

---

## 📋 实战案例：日报格式确认

### 场景
昨天确认了日报格式，但今天自动任务用了旧格式。

### 正确执行流程

```bash
# === Phase 1: 确认 ===
# 用户说："以后日报按这个9字段格式"

# 创建检查点
cat > checkpoints/confirmation/daily-report-format-20260414-impact.json << 'EOF'
{
  "phase": "confirmation",
  "content": "日报采用9字段标准格式",
  "fields": ["📦基本信息", "🔍一句话功能", "👤面向场景", 
            "🏭主要覆盖行业", "🏢代表企业", "🌍地区", 
            "💡核心亮点", "📈价值判断"],
  "impact_systems": ["cron job: 金融产品日报"]
}
EOF

# === Phase 2: 固化 ===
# 2.1 更新 cron job
cron update a23bbe0d-089f-477a-a372-9b42fcad4006 \
  patch.payload.message="请生成今日金融产品日报...（新格式详细说明）..."

# 2.2 创建格式规范文档
cat > topics/daily-report-format.md << 'EOF'
# 日报格式规范 v2.0
## 9字段标准
...
EOF

# 2.3 更新 MEMORY.md（关键配置区域）
# 添加：
# ## 日报配置
# - 格式版本: v2.0（9字段）
# - 最后更新: 2026-04-14
# - 状态: ✅ 已固化

# 创建检查点
cat > checkpoints/confirmation/daily-report-format-20260414-config.json << 'EOF'
{
  "phase": "solidification",
  "config_updated": [
    "cron job a23bbe0d-089f-477a-a372-9b42fcad4006",
    "topics/daily-report-format.md",
    "MEMORY.md 关键配置区域"
  ]
}
EOF

# === Phase 3: 验证 ===
# 3.1 立即测试
cron run a23bbe0d-089f-477a-a372-9b42fcad4006

# 3.2 检查结果
# 确认输出格式是否符合9字段标准

# 创建检查点
cat > checkpoints/confirmation/daily-report-format-20260414-verify.json << 'EOF'
{
  "phase": "verification",
  "test_result": "pass",
  "notes": "手动触发验证，格式正确"
}
EOF

# === Phase 4: 归档 ===
# 合并检查点、更新追踪、标记 MEMORY.md
```

---

## 🔍 快速检查清单

每次确认配置/规则/格式时，问自己：

- [ ] **执行配置**更新了吗？（cron payload / 脚本参数 / 环境变量）
- [ ] **检查点**创建了吗？（checkpoints/confirmation/{task-id}-*.json）
- [ ] **长期记忆**更新了吗？（MEMORY.md 关键配置区域）
- [ ] **技能索引**更新了吗？（SKILLS-INDEX.md / topics/*.md）
- [ ] **验证测试**执行了吗？（立即触发一次，确认输出）
- [ ] **任务追踪**记录了吗？（checkpoints/task-tracking.md）

**如果任一没做，= 没有真正固化**

---

## 📝 与现有方法论的整合

### 与图书馆记忆法的整合
1. 检查清单本身索引化（关键词："任务完成"/"确认固化"/"验证"）
2. 按需加载：不需要每次启动都读，遇到配置变更时加载
3. 关键配置区域：MEMORY.md 中单独开辟"已固化的配置"区块

### 与消防队模式的整合
1. "确认→固化→验证"拆解为 <5分钟子任务
2. 每个子任务有独立检查点文件
3. 支持中断恢复：任何时候可以查看检查点，知道执行到哪一步
4. 任务追踪文件记录所有已固化的配置变更

---

## 🎯 总结

**确认是起点，固化是动作，验证是闭环。**

没有固化的确认 = 口头承诺
没有验证的固化 = 自我安慰
没有记忆的验证 = 下次再犯

**三者缺一不可。**

---

*文档版本: 1.0*
*创建时间: 2026-04-14*
*关联文档: MEMORY.md（关键配置区域）, SKILLS-INDEX.md, checkpoints/task-tracking.md*