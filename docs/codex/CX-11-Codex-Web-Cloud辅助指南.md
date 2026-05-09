# CX-11 Codex Web / Cloud 辅助指南：什么时候离开 App

> 本篇是 App 主线的云端辅助篇。
>
> 主要来源：OpenAI Codex Web / Cloud、GitHub integration、Environments、Internet Access、Secrets 官方文档。

## 本课目标

你会学会：

- Web / Cloud 与本地 App 的边界。
- 什么任务适合交给云端。
- Cloud environment、setup script、secrets 怎么理解。
- 如何避免把 Cloud 当成本地 App 的替代品。

## 1. Web / Cloud 是什么

Codex Web / Cloud 是远程执行路径。它通常围绕远程仓库、PR 和云端环境工作。

与 App 的区别：

| 维度 | App | Web / Cloud |
|---|---|---|
| 执行位置 | 本机 / 本地项目 | 云端环境 |
| 主要对象 | 本地线程、worktree、Review | 远程仓库、任务、PR |
| 环境依赖 | 本机工具和文件 | Cloud environment |
| 适合 | 本地开发、桌面多线程 | 长任务、远程 PR、云端 CI 风格任务 |

## 2. 什么时候用 Cloud

适合：

- 远程仓库上直接修 issue。
- PR 评论修复。
- CI 失败后云端排查。
- 长时间后台任务。
- 不依赖本机私有文件的改动。

不适合：

- 需要本机数据库或本地 GUI。
- 需要访问未同步的本机文件。
- 需要你频繁看 UI 的任务。
- 密钥和生产数据边界不清的任务。

## 3. Environment 是什么

Cloud environment 定义云端任务怎么跑：

- 选择仓库。
- 安装依赖。
- 设置 setup script。
- 配置 secrets。
- 控制网络访问。
- 设定运行验证方式。

好的 environment 应该能让 Codex 在云端稳定复现项目。

## 4. 创建 Cloud 任务前先准备什么

从 App 接力到 Cloud 前，先准备：

| 项 | 要写清 |
|---|---|
| 仓库 | Cloud 要操作哪个 GitHub repo / branch |
| 任务范围 | 改哪些目录，不改哪些目录 |
| 依赖安装 | 用 `npm ci`、`pnpm install --frozen-lockfile` 还是其他命令 |
| 验证命令 | build、test、lint 具体怎么跑 |
| Secrets | 需要哪些，是否只读，是否会进日志 |
| 网络 | 是否需要 Internet access |
| 回流方式 | 生成 PR、推分支，还是只输出报告 |

Cloud 不是“更强的本地 App”。它缺少你的本机文件、GUI 状态和本地服务，必须靠远程仓库和 environment 复现。

## 5. Setup Script

Setup script 用来准备云端环境，例如：

```bash
npm ci
npm run build
```

原则：

- 短而稳定。
- 不写交互式命令。
- 不输出密钥。
- 失败时错误清晰。
- 与项目 README / AGENTS.md 保持一致。

## 6. Internet Access 与 Secrets

Cloud 任务可能需要联网或 secret。规则：

- 默认最小权限。
- 只给任务需要的 secret。
- 不把 secret 打印到日志。
- 不把生产写权限给调试任务。
- 定期轮换密钥。

## 7. App 到 Cloud 的接力

常见流程：

```text
App 中发现问题
  -> 本地确认范围
  -> 创建 GitHub issue / PR
  -> Cloud 接手长时间修复或 CI 修复
  -> 回到 App 拉取分支
  -> App Review 最终检查
```

Cloud 是辅助执行，不是绕过 Review。

## 8. Cloud 回到 App 怎么验收

Cloud 完成后，回到 App 做：

1. 拉取远端分支或打开对应 PR。
2. 在 App Review 里看 diff。
3. 本地运行关键验证命令。
4. 检查 Cloud 是否改了范围外文件。
5. 检查 PR 描述和测试记录。
6. 决定是否继续让 App 修，还是回 Cloud 处理远程 CI。

不要因为 Cloud 任务“通过”就跳过本地 Review。

## 9. Web / Cloud 与 Automations

| 需求 | 优先 |
|---|---|
| 本地项目周期检查 | App Automation |
| 远程仓库长任务 | Cloud |
| PR / CI 触发 | GitHub / CI / Cloud |
| 内部系统调度 | SDK / 外部队列 |

不要用桌面 App 伪装 webhook server。

## 常见问题

### Q1：Cloud 能访问互联网吗？

取决于当前 environment 和官方能力。需要联网时显式配置并审查风险。

### Q2：Cloud 能用我的本机文件吗？

不能默认假设。Cloud 主要使用远程仓库和配置的环境。

### Q3：Cloud 任务完成后还要本地 Review 吗？

要。最终合并前仍要看 diff、测试和 PR。

## 下一步

下一篇：[CX-12 CLI 辅助指南](./CX-12-Codex-CLI辅助完整指南.md)。
