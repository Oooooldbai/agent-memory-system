# OpenClaw Methodologies

> OpenClaw 方法论模板 - 供女娲和其他人学习使用

---

## 📚 方法论文档

### 1. 图书馆记忆法 v2.0 ✨
**文件**: `LIBRARY-MEMORY-TEMPLATE.md`

核心思想：建立索引 → 按需加载 → 单一配置源

- **v2.0 核心变更**：单一真相源（config.md + config.local.md），彻底杜绝配置遗漏
- 轻量索引文件（index.md）
- 关键词映射表
- 日志生命周期管理
- 敏感信息分离（gitignore）

**与 v1.1 的区别**：
| 维度 | v1.1 | v2.0 |
|------|------|------|
| 配置存储 | 四层同步 | 单一来源 |
| 遗漏风险 | 高（依赖AI记性） | 零（无同步问题） |
| Git可追溯 | 否 | 是 |

### 2. 消防队模式
**文件**: `FIREFIGHTER-MODE-TEMPLATE.md`

核心思想：任务拆解为<5分钟子任务 → 检查点驱动 → 可恢复

- 检查点文件（JSON格式）
- 任务追踪文件（Markdown格式）
- 支持中断恢复

### 3. 任务执行闭环管理 ✨
**文件**: `TASK-COMPLETION-WORKFLOW.md`

核心思想：确认 → 固化 → 验证三步闭环

整合图书馆记忆法 + 消防队模式

**触发条件**：
- 用户确认格式/规则/配置
- 涉及定时任务（cron job）变更
- 涉及自动化流程调整

**执行步骤**：
1. **确认**：明确变更内容，创建检查点
2. **固化**：更新执行配置 + 记忆文档
3. **验证**：立即测试一次，确认输出符合预期
4. **归档**：合并检查点，更新任务追踪

**关联文档**：
- `DAILY-REPORT-FORMAT.md` - 日报9字段格式规范示例

---

## 🚀 快速开始

### 复制到你的工作区

```bash
git clone https://github.com/{username}/openclaw-methodologies.git
cd openclaw-methodologies
```

### 定制方法论

1. 复制模板文件到你的工作区
2. 根据你的需求修改
3. 按照模板执行

---

## 📝 使用示例

### 示例1：配置变更固化

当确认一个配置变更时：

```bash
# 1. 确认阶段
cat > checkpoints/confirmation/{task-id}-impact.json << 'EOF'
{
  "phase": "confirmation",
  "content": "确认配置变更内容"
}
EOF

# 2. 固化阶段
# 更新执行配置（cron job / 脚本 / 环境变量）
# 更新记忆文档（MEMORY.md / topics/*.md）

# 3. 验证阶段
# 立即测试一次
cron run <job-id>

# 4. 归档阶段
# 合并检查点，更新任务追踪
```

### 示例2：日报格式标准化

详细示例见 `DAILY-REPORT-FORMAT.md`

---

## 🤝 贡献

欢迎提交PR改进方法论！

---

## 📄 许可证

MIT License

---

*OpenClaw Methodologies | 最后更新: 2026-04-14*
