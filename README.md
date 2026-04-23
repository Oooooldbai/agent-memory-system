# 锚点方法论（Anchor Methodology）

> 用检查点和自动化取代记忆和自觉。不依赖"记得"，依赖锚存在那里。

**新Agent先读：QUICKSTART.md（77行精华版）**

---

## 三足鼎立

```
        锚定机制
       （触发层）
      什么时候该固化
       /        \
图书馆记忆法    消防队模式
 （存储层）     （执行层）
 存什么、怎么存  怎么拆、怎么并行
```

| 机制 | 解决什么 | 文件 |
|------|---------|------|
| **锚定机制** | 什么时候该固化、怎么固化 | ANCHORING-TEMPLATE.md |
| **图书馆记忆法** | 存什么、怎么存、怎么读 | LIBRARY-MEMORY-TEMPLATE.md |
| **消防队模式** | 任务怎么拆、怎么并行、检查点怎么管 | FIREFIGHTER-MODE-TEMPLATE.md |

三者独立但互补，共同构成方法论三足鼎立。

## 辅助文件

| 文件 | 用途 |
|------|------|
| TASK-COMPLETION-WORKFLOW.md | 确认→固化→验证闭环 |
| AUTO-CHECKS.md | HEARTBEAT自动检查+熔断机制 |
| QUICKSTART.md | 快速启动卡（77行精华版） |

## 核心原则

1. **检查点是硬证据** — 不创建检查点 = 任务未完成
2. **锚定是触发器** — 不识别重要性 = 固化无从谈起
3. **配置单一来源** — 只存 config.md + config.local.md
4. **教训写进系统** — 不写进SOUL/FIRE/AGENTS = 没写
5. **按需加载** — 模板轻量，业务独立
6. **日志有生命周期** — 7天蒸馏，30天清理
7. **自动扫描** — 不依赖"记得"，HEARTBEAT强制执行

## 快速开始

```bash
git clone https://github.com/{username}/openclaw-methodologies.git
```

复制模板到你的工作区，根据需求定制。

## 贡献

欢迎提交PR改进方法论！

---

*MIT License | 最后更新: 2026-04-23*
