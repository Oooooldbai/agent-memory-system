# 图书馆记忆法（Library Memory System）

> 节省token，按需加载详细内容 + 配置管理防失忆

## 核心原则

模板本身必须轻量。业务规则、项目隐私不进模板，独立存放。

## 四层配置体系

```
config.md          ← 唯一配置源（非敏感，进Git）
config.local.md    ← 敏感Token（不进Git，gitignore）
MEMORY.md          ← 不存配置内容，只存引用
TOOLS.md           ← 使用说明，引用config.md
memory/index.md    ← 关键词映射，指向config.md
    ↓
memory/topics/*.md ← 按需加载的知识专题
```

**核心规则：所有配置只存 config.md 和 config.local.md，禁止其他文件重复存储。**

## 每日日志生命周期

| 阶段 | 时机 | 动作 |
|------|------|------|
| 写入 | 当天随时 | 重要事件、决策、配置变更 |
| 读取 | 每次启动 | 最近2天，获取上下文 |
| 蒸馏 | Day 7+ | 有价值内容 → topics/ 或 MEMORY.md |
| 归档 | Day 7+ | 移到 memory/archive/ |
| 清理 | Day 30+ | 删除无价值日志 |

**强制执行：每周定期检查，不依赖"记得"。**

## 蒸馏判断标准

| 内容类型 | 保留？ | 存放 |
|----------|--------|------|
| 新决策/新机制 | ✅ | MEMORY.md + topics/ |
| 项目进展 | ✅ | 项目目录 |
| 配置变更 | ✅ | config.md / config.local.md |
| 日常琐事 | ❌ | 归档后删除 |
| 关键教训 | ✅ | MEMORY.md |

## 对话中按需加载

检测到关键词 → 自动读取对应topics文件。关键词映射存在 `memory/index.md`。

## 配置变更流程

| 操作 | 执行 |
|------|------|
| 非敏感配置（仓库、路径、链接） | 改 config.md |
| 敏感Token | 改 config.local.md |

不需要同步其他文件（配置只存在这两个地方）。

## 同步触发条件

| 触发源 | 触发词 |
|--------|--------|
| 用户反馈 | "太好了"/"解决了"/明确认同 |
| Agent自身 | "解决了"/"已完成"/"这个很重要" |
| 内容类型 | 新概念/名单/分类体系 |

触发后：更新 MEMORY.md + 更新 index.md

## 启动检查清单

```
SOUL.md → USER.md → memory/index.md → MEMORY.md → config.md → config.local.md
```

遗漏则立即补录。

## 会话归档机制

```
memory/sessions/daily/YYYY-MM-DD.md
```

| 天数 | 动作 |
|------|------|
| Day 0 | 有价值信息自动写入 |
| Day 1-7 | 保留，巡检扫描 |
| Day 8+ | 删除 |

vs 每日日志：归档是客观提取（不依赖自觉），日志是主动记录。

---

*图书馆记忆法 v2.0 — 按需加载 + 配置单一来源 + 日志生命周期 + 会话归档兜底*
