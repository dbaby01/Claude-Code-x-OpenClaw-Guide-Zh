# CX-10 Review / GitHub / PR 工作流：从 App 改动到可合并结果

> 本篇聚焦 App 主线里的 Review、GitHub 和 PR。
>
> 主要来源：OpenAI Codex App Review、GitHub integration、Codex Cloud / Web、CLI review 官方文档。

## 本课目标

你会学会：

- 在 App 中看懂 Codex 的改动。
- 用 Review 面板做行内反馈和最终验收。
- 把 App 本地改动推到 GitHub。
- 什么时候交给 Codex Cloud / Web 处理 PR。

## 1. App Review 是合并前的主关口

Codex 写完代码后，不要只看它的总结。你应该进入 Review：

1. 看改了哪些文件。
2. 看每个文件 diff。
3. 确认是否超出任务范围。
4. 确认测试是否运行。
5. 必要时行内评论让 Codex 修。
6. 人工决定是否 stage / commit / push。

## 2. 一个完整 App 改动流程

```text
目标：修复登录页移动端按钮换行问题。
范围：只改 src/pages/login.tsx 和 src/styles/login.css。
验证：运行 npm test -- login 和 npm run lint。
交付：展示 diff 摘要，不要自动提交。
```

完成后检查：

| 检查 | 标准 |
|---|---|
| 范围 | 只改目标文件 |
| 行为 | 修复目标问题，没有顺手重构 |
| 测试 | 运行了相关测试 |
| 样式 | 和现有代码风格一致 |
| 风险 | 没有引入密钥、网络、权限变化 |

## 3. App Review 面板里具体看什么

Review 不是只看“Codex 说完成了”。逐项看：

| 区域 | 要看什么 |
|---|---|
| 文件列表 | 是否出现范围外文件、生成物、锁文件、配置文件 |
| 单文件 diff | 是否有无关重构、删除、格式化噪音 |
| 测试结果 | 是否真的运行过命令，失败是否解释清楚 |
| 行内评论 | 能否针对具体行要求 Codex 修复 |
| Stage / Commit | 是否由你确认提交范围和 message |
| Push / PR | 是否推到正确分支和远端 |

如果 Review 中发现问题，优先用行内评论或明确提示让 Codex 修，不要重新开一个含糊的大任务。

## 4. `/review` 的用法

在 App 或 CLI 中，`/review` 可以审查当前改动：

```text
/review
```

更好的写法：

```text
/review 重点检查移动端布局、CSS specificity、是否影响桌面端，以及测试缺口。
```

`/review` 适合发现问题，但最终是否接受仍要看 Review 面板和测试结果。

## 5. GitHub Connector 的角色

GitHub Connector / integration 适合：

- 读取 issue。
- 读取 PR 上下文。
- 查 CI 状态。
- 评论或更新 PR（取决于权限）。
- 从 App / Cloud 之间接力任务。

授权前要确认：

- 访问哪些仓库。
- 是否有写权限。
- 是否能评论 PR。
- 是否能创建分支或提交。

## 6. 从 App 到 GitHub PR

典型流程：

```text
App thread
  -> Codex 修改本地文件
  -> Review 面板检查
  -> 运行测试
  -> git status / diff
  -> stage
  -> commit
  -> push branch
  -> create PR
```

提交信息建议让 Codex 先草拟，但由你确认：

```text
根据当前 diff 写一个简短 commit message。不要执行 git commit。
```

## 7. PR 前验收清单

创建 PR 前确认：

| 项 | 标准 |
|---|---|
| 分支 | 不是直接在 `main` 上提交 |
| Diff | 没有范围外改动 |
| 测试 | 至少运行相关测试或说明不能运行原因 |
| 描述 | Summary / Verification / Risk 写清楚 |
| Secret | 没有 `.env`、token、账号信息 |
| 连接器权限 | GitHub 写权限只用于目标 repo / PR |

## 8. 什么时候用 Cloud / Web 处理 PR

适合 Cloud / Web：

- 远程仓库任务，不需要本机环境。
- 长时间跑的修复。
- PR 评论和 CI 反复修。
- 你不想占用本机 App 线程。

不适合：

- 依赖本机私有服务。
- 需要本地 GUI / 本机文件。
- 需要你频繁交互判断的小任务。

## 9. CLI review 是辅助

CLI 可以在 CI 或终端中做补充：

```bash
codex review
codex review --help
```

适合：

- CI 中自动审查 PR。
- 终端里快速检查当前 diff。
- 和 App Review 结果交叉验证。

不要用 CLI review 取代 App Review 面板。

## 10. PR 描述模板

```markdown
## Summary
- 修复登录页移动端按钮换行
- 限制按钮最小宽度，避免文本溢出

## Verification
- npm test -- login
- npm run lint

## Risk
- 仅影响登录页样式
- 已检查桌面断点
```

## 11. 常见风险

| 风险 | 防法 |
|---|---|
| Codex 顺手重构 | 任务里写清范围 |
| 未运行测试 | 明确验证命令 |
| 改了主分支 | 使用 worktree / feature branch |
| PR 太大 | 拆任务 |
| CI 失败自动乱修 | 限定失败项和文件范围 |

## 常见问题

### Q1：Codex 可以自动提交吗？

技术上可以，但学习阶段不建议。先让它展示 diff 和 commit message，再由你确认。

### Q2：App Review 和 GitHub PR Review 哪个重要？

都重要。App Review 是本地合并前检查；GitHub PR Review 是团队协作关口。

### Q3：Cloud 修 PR 后怎么回到 App？

看当前 App / Cloud 的 handoff 能力和 Git 分支状态。可靠做法是拉取远端分支，在 App 中继续 Review。

## 下一步

下一篇：[CX-11 Web / Cloud：App 的云端辅助路径](./CX-11-Codex-Web-Cloud辅助指南.md)。
