# CX-08 Subagents 完整指南：App 中的多 Agent 协作

> 主要来源：OpenAI Codex Subagents、App Features、CLI Slash Commands 官方文档。

## 本课目标

你会学会：

- 在 App 主线中判断什么时候需要 Subagents。
- 区分主线程、subagent、worktree、automation。
- 设计安全的多 Agent 分工。
- 避免为了“看起来高级”滥用 subagents。

## 1. Subagents 解决什么问题

Subagents 适合把一个任务拆成多个相对独立的子任务：

- 并行读不同模块。
- 一个实现、一个审查。
- 一个查官方文档、一个查代码。
- 前端、后端、测试分开推进。

它不是扩大单个上下文窗口，也不是让任务自动变聪明。它的价值是并行和分工。

## 2. App 中什么时候用

适合用：

```text
这个重构涉及前端、API 和测试。请先制定分工计划，再并行处理互不重叠的部分。
```

不适合用：

```text
修一个拼写错误。
```

判断标准：

| 条件 | 是否适合 |
|---|---|
| 任务可拆成独立子任务 | 适合 |
| 多个文件/模块互不重叠 | 适合 |
| 需要独立审查 | 适合 |
| 下一步必须等一个结果 | 先主线程做 |
| 单文件小改 | 不适合 |

## 3. App 中启动前要写清的内容

让 App 调 subagents 前，提示词至少要包含：

| 内容 | 示例 |
|---|---|
| 总目标 | “把认证模块迁移到新 SDK” |
| 子任务边界 | “前端只读 `src/pages/login.tsx`，后端只读 `src/api/auth/*`” |
| 文件所有权 | “Agent A 只改 docs，Agent B 只改 tests” |
| 是否允许写 | “先只读分析，不改文件” |
| 汇总方式 | “每个 agent 输出结论、证据、风险和建议动作” |
| 停止条件 | “遇到权限/测试/依赖问题先汇报，不自行扩大范围” |

不写这些，subagent 很容易变成多个不受控的普通对话。

## 4. 推荐分工模板

```text
先给出 subagent 分工计划，不要立刻改文件。

目标：重构认证模块。
范围：src/auth、tests/auth。
约束：不要改数据库迁移，不要引入新依赖。
分工：
- Agent A：阅读现有 auth 代码，列出风险。
- Agent B：核对官方 SDK 文档。
- Agent C：审查测试覆盖缺口。
汇总后再决定是否执行。
```

## 5. 文件所有权

并行写代码时必须明确文件所有权：

| Agent | 负责范围 |
|---|---|
| Frontend agent | `src/pages/login.tsx`、`src/components/auth/*` |
| Backend agent | `src/api/auth/*` |
| Test agent | `tests/auth/*` |
| Review agent | 只读 diff，不改文件 |

没有文件边界的并行开发很容易互相覆盖。

## 6. Subagents 与 worktree

| 能力 | 作用 |
|---|---|
| Subagent | 分工和并行思考 / 执行 |
| Worktree | 隔离 Git 工作区 |
| Review | 合并前看 diff |

大任务建议组合：

```text
Worktree thread + Subagents + Review
```

这样既能分工，又能降低污染主工作区的风险。

## 7. Subagents 与 Skills

Subagent 可以执行一个明确角色，Skill 提供工作流方法。

例子：

```text
让一个只读 subagent 使用 $tutorial-verifier skill 核对这篇教程的官方来源。
```

注意：不要假设所有 subagent 都自动拥有所有工具或插件。以当前 App 暴露能力为准。

## 8. Review 与合并规则

Subagents 返回后，主线程不要直接相信总结。按这个顺序合并：

1. 看每个 subagent 的输出是否有证据。
2. 看是否有互相冲突的结论。
3. 看文件所有权是否被遵守。
4. 由主线程整合方案。
5. 执行或合并后用 App Review 看 diff。
6. 运行验证命令。

如果两个 agent 给出相反建议，不要让其中一个继续硬改；先让主线程整理冲突点，再决定下一步。

## 9. 常见失败模式

| 失败模式 | 原因 | 修正 |
|---|---|---|
| 多个 agent 改同一文件 | 所有权不清 | 明确文件范围 |
| 主线程不知道进展 | 没有汇总点 | 要求阶段性汇报 |
| 子任务太大 | 拆分不够 | 缩小到模块级 |
| agent 自己发散 | 缺少目标和验收 | 写清停止条件 |
| 没有 Review | 并行改动难合并 | 合并前统一 review |

## 常见问题

### Q1：Subagents 会自动更正确吗？

不会。它们只是并行执行单元。正确性来自清晰分工、约束和 Review。

### Q2：什么时候不用 Subagents？

单文件小改、一步任务、目标不清楚的任务，都不应该先拉 subagents。

### Q3：Subagents 能和 Automation 一起用吗？

可以，但自动化任务更要限制权限和范围。

## 下一步

下一篇：[CX-09 Automations：App 里的后台任务和周期检查](./CX-09-Codex-Automations后台任务完整指南.md)。
