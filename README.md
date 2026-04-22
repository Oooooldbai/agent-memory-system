# 锚点方法论（Anchor Methodology）

> 用检查点和自动化取代记忆和自觉。不依赖"记得"，依赖锚存在那里。

---

## 三大支柱

| 支柱 | 文件 | 解决什么 |
|------|------|---------|
| **图书馆记忆法** | LIBRARY-MEMORY-TEMPLATE.md | 存什么、怎么存、怎么找、日志怎么保鲜 |
| **消防队模式** | FIREFIGHTER-MODE-TEMPLATE.md | 任务怎么拆、怎么并行、检查点怎么管 |
| **任务闭环** | TASK-COMPLETION-WORKFLOW.md | 确认→固化→验证，变更怎么不丢 |
| **自动检查** | AUTO-CHECKS.md | HEARTBEAT触发，不依赖"记得" |

## 核心原则

1. **检查点是硬证据** — 不创建检查点 = 任务未完成
2. **配置单一来源** — 只存 config.md + config.local.md
3. **按需加载** — 模板轻量，业务独立
4. **日志有生命周期** — 7天蒸馏，30天清理
5. **自动扫描** — 不依赖"记得"，HEARTBEAT强制执行

## 快速开始

```bash
git clone https://github.com/{username}/openclaw-methodologies.git
```

复制模板到你的工作区，根据需求定制。

## 贡献

欢迎提交PR改进方法论！

---

*MIT License | 最后更新: 2026-04-22*
