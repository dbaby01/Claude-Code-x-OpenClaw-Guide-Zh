# Codex Automations 与集成完整指南：从后台周期任务到 CI/CD 自动化

> **课程信息**
>
> - **作者**：老金
> - **GitHub**：https://github.com/KimYx0207
> - **公众号**：老金带你玩AI
> - **X（Twitter）**：老金带你玩AI
> - **个人博客**：https://aiking.dev
> - **预计学时**：3-4小时
> - **难度等级**：⭐⭐ 基础进阶
> - **更新日期**：2026年5月
> - **适用版本**：Codex CLI v0.128.0（验证于 2026-04-30）/ Codex App 当前版本
> - **信息来源**：[GitHub](https://github.com/openai/codex) | [Codex 官方文档](https://openai.com/codex)
> - **前置课程**：CX-02 App / CX-03 CLI / CX-05 配置系统

---

> **定位说明**：本篇仍以 Codex App Automations 为主。GitHub Action、CLI/CI、SDK 只作为 App 自动化之外的补充选项，不是本篇主线。

## 重要校准：不要把三类自动化混在一起

旧稿把 App Automations、GitHub Actions、Slack/Linear 集成、SDK 和 Webhook 写成了一个统一产品面，里面有不少无法证实或错误的内容：

- 官方 App Automations 不是 `.codex/automations.toml` 配置文件工作流
- 官方文档强调 Automations 结果进入 Triage / Inbox，没发现时可自动归档
- Git push / PR 事件触发不是当前 App Automations 的主线描述；GitHub PR 场景应看 Codex in GitHub / CLI / 云端工作流
- `@openai/codex` 作为“Node.js SDK”并不等于官方 Codex App SDK；旧稿里的 `generate()`、`generateStream()`、`result.diff` 示例不要照抄
- Slack、Linear、Gmail、Drive 等应通过 Codex Plugins / Apps / MCP 连接，不应写成 App 设置中固定存在的所有集成

本篇把自动化分成三层：

| 层级 | 使用什么 | 适合什么 |
|------|----------|----------|
| App Automations | Codex App 后台周期任务 | 定期检查、摘要、提醒、Triage |
| CLI / CI 自动化 | `codex exec`、`codex review`、GitHub Actions | 无头执行、PR 审查、CI 报告 |
| Plugins / Apps / MCP | Gmail、Drive、Slack、Linear、GitHub 等插件或 MCP | 连接外部系统、读取业务上下文 |

---

## 📚 本课学习目标

完成本课后，你将能够：

1. 创建和管理 Codex App Automations
2. 理解 Triage / Inbox、thread automation、test automation、skills 联动
3. 选择合适的沙盒和权限策略，避免无人值守任务失控
4. 用 CLI 在 CI 中运行一次性 Codex 任务
5. 正确区分官方插件、Apps、MCP、GitHub 工作流和 SDK
6. 设计团队可审计的自动化规范

---

## 第1部分：App Automations 概览

### 1.1 Automations 是什么

官方定义：Automations 用来在后台自动执行重复任务。Codex 有发现时把结果加入 Triage / Inbox；没有发现时可以自动归档。你可以把 Automations 与 Skills 组合，让周期任务具备更具体的工作流。

典型任务：

- 每天总结某个目录过去 24 小时的提交
- 每周检查你最近提交中可能引入的 bug
- 定期检查文档是否落后于代码
- 等待一段时间后继续某个线程

### 1.2 Automations 不是什么

它不是：

- 传统 cron 的完整替代品
- GitHub Actions 的替代品
- 任意 Webhook 触发器
- 可以绕过沙盒的后台超级权限
- 关机也能运行的云端守护进程

如果你需要 7x24、事件驱动、云端 runner，优先考虑 GitHub Actions、Codex Cloud 或你自己的调度系统。

### 1.3 Triage / Inbox

Automations pane 中的 Triage 是自动化结果收件箱：

- 有发现的 run 会出现在 Triage
- 可以筛选全部或未读
- 没有发现的任务可以自动归档
- 你再决定是否继续让 Codex 修复、生成 PR 或关闭结果

---

## 第2部分：创建和管理 Automations

### 2.1 用自然语言创建

官方推荐路径是直接让 Codex 创建或更新 automation，而不是手写一个固定配置文件。

示例：

```text
每天工作日上午 9 点，检查这个仓库过去 24 小时的提交。
只关注 src/ 和 tests/。
如果发现可能的 bug、缺失测试或明显文档漂移，把结果放到 Triage；
如果没有发现，自动归档。
```

再比如：

```text
每周一上午检查 package.json 和 lockfile 的依赖变化，
总结高风险升级和需要人工验证的点。不要自动修改文件。
```

### 2.2 管理入口

在 Codex App 的 Automations pane 中管理：

| 项目 | 用途 |
|------|------|
| Automations 列表 | 查看已创建的自动化任务 |
| Runs | 查看每次执行记录 |
| Triage | 查看有发现的结果 |
| Enable / Disable | 暂停或恢复 |
| Edit | 修改任务描述、计划或行为 |
| Archive | 归档无须继续处理的结果 |

按钮文案可能随 App 版本变化；团队文档应写“打开 Automations pane 管理”，不要写死某个菜单路径。

### 2.3 Thread Automations

Thread automation 绑定当前线程，适合“稍后继续这件事”：

```text
30 分钟后回来检查测试是否跑完。
如果失败，汇总失败测试和最可能原因；如果通过，归档这个线程。
```

适合：

- 等待 CI、测试、构建
- 稍后复查日志
- 每天/每周延续同一个工作线程

### 2.4 Test Automations

测试自动化不要一开始就全自动改代码。推荐三步走：

1. 只读运行测试并报告失败
2. 对明确失败生成修复建议
3. 人工 Review 后再允许写入或修复

示例：

```text
每天下午 6 点运行最小回归测试集合：
npm test -- --runInBand src/profile
如果失败，只生成 Triage 报告，不要修改文件。
```

---

## 第3部分：Automations + Skills

Automation 解决“什么时候跑”，Skill 解决“怎么跑”。

### 3.1 创建近期 bug 修复 skill

可以创建一个个人 skill，例如 `$recent-code-bugfix`，让它专门做：

- 找当前作者最近一周提交
- 只看这些提交触碰的文件
- 找直接关联的测试失败或可复现 bug
- 给出最小修复和验证结果

然后创建 automation：

```text
每天检查我过去 24 小时的提交，并运行 $recent-code-bugfix。
没有明确 bug 就归档；有明确 bug 就进入 Triage。
```

### 3.2 自动生成团队摘要

```text
每天上午 10 点查看 origin/main 过去 24 小时的提交。
按 workstream 汇总，不列 commit hash。
如果能查到 PR 标题和描述，就把 PR 链接内嵌到摘要里。
```

### 3.3 自动化文档漂移检查

```text
每周五检查 src/api 和 docs/api.md 是否一致。
如果发现代码已变但文档没变，生成 Triage 报告。
不要直接修改文档。
```

---

## 第4部分：权限与安全模型

### 4.1 Automations 使用默认沙盒设置

官方文档明确说明：Automations 是无人值守运行，会使用默认 sandbox settings。

含义：

- Read-only 模式下，写文件、访问网络、操作本机 app 的工具调用会失败
- Full access / 高权限模式下，后台自动化风险更高
- 可以通过 Settings 调整 sandbox，并用 rules 选择性 allowlist 命令

### 4.2 推荐权限表

| 自动化场景 | 推荐权限 | 说明 |
|------------|----------|------|
| 生成提交摘要 | Read-only | 只读 Git 历史即可 |
| 检查测试失败 | Read-only 或受限写入 | 需要写缓存时再放开 |
| 生成报告文件 | Workspace-write + Review | 报告可以写入，但要审查 |
| 自动修复代码 | 谨慎使用 | 必须进 Review，不要直接推主分支 |
| 操作浏览器或桌面 app | 不建议无人值守 | 容易触碰敏感页面或状态 |

### 4.3 Rules 和命令 allowlist

如果某个命令需要经常运行，例如 `npm test`，不要直接放开所有 shell 权限。更好的做法是只允许明确命令：

```text
允许 automation 运行：
- git log
- git diff
- npm test -- --runInBand

不允许：
- git push
- rm -rf
- 修改 .env / secret 文件
```

具体规则配置以 CX-05 和当前 Codex 设置为准。

---

## 第5部分：GitHub Action 与 CLI / CI 自动化

App Automations 适合桌面后台任务；CI 更适合可重复、可审计、随 PR 触发的自动化。

### 5.1 官方 GitHub Action

OpenAI 维护 `openai/codex-action`，适合在 GitHub Actions 里触发 Codex。它和 Codex App Automations 不是同一套系统：

| 能力 | 运行位置 | 适合 |
|------|----------|------|
| App Automations | Codex App 后台任务 | 个人/团队的周期检查、摘要、提醒、Triage |
| GitHub Action | GitHub-hosted 或 self-hosted runner | PR review、CI 失败分析、仓库内自动修复 |
| CLI `codex exec` | 任意 shell / CI runner | 自定义脚本、内部 CI、一次性自动化 |
| SDK | 你的应用服务器 | 自研工作流、队列、审批、审计 |

典型 GitHub Action 使用方式：

```yaml
name: Codex

on:
  pull_request:
    types: [opened, synchronize]

jobs:
  codex:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      pull-requests: write

    steps:
      - uses: actions/checkout@v5
      - uses: openai/codex-action@v1
        with:
          openai-api-key: ${{ secrets.OPENAI_API_KEY }}
          prompt: |
            Review this pull request for correctness, security issues,
            regressions, and missing tests. Leave concise findings.
```

`openai/codex-action` 会安装 Codex CLI，并通过 Responses API proxy 运行。常见输入包括 `openai-api-key`、`prompt` / `prompt-file`、`output-file`、`working-directory`、`sandbox`、`codex-version`、`codex-args`、`model`、`effort`、`safety-strategy` 等。实际参数、认证和输出方式以官方 README 为准。

### 5.2 GitHub Action 安全边界

在 PR 场景中尤其要注意：

- `permissions` 最小化，能只读就只读。
- fork PR 不要直接暴露高权限 secrets。
- 不要让 Action 自动 merge 或 push 到 protected branch。
- 对自动修复任务使用单独分支和 PR。
- 给 job 设置 `timeout-minutes`。
- 让 Codex 输出证据和验证命令，不要只输出“已完成”。
- Linux/macOS runner 可用 `drop-sudo`、`unprivileged-user`、`read-only` 等 safety strategy；Windows hosted runner 当前需要按 action 文档使用 `unsafe`。

### 5.3 先看本机 CLI 帮助

Codex CLI 的参数变化较快，先用帮助确认：

```bash
codex --help
codex exec --help
codex review --help
```

当前 CLI 主命令中常见能力包括：

- `codex exec`：非交互执行
- `codex review`：非交互代码审查
- `codex mcp`：管理 MCP server
- `codex plugin`：管理插件
- `codex app`：启动桌面 App

### 5.4 手写 CLI CI 例子

以下是“思路示例”，不是官方 GitHub Action 固定模板。务必在你的 CI 中锁定 CLI 版本、限制 token 权限、设置超时。

```yaml
name: Codex Review

on:
  pull_request:
    types: [opened, synchronize]

jobs:
  review:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      pull-requests: write

    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - name: Install Codex CLI
        run: npm install -g @openai/codex

      - name: Prepare diff
        run: git diff origin/${{ github.base_ref }}...HEAD > /tmp/pr.diff

      - name: Run Codex review
        env:
          OPENAI_API_KEY: ${{ secrets.OPENAI_API_KEY }}
        run: |
          codex exec --ask-for-approval never \
            "Review /tmp/pr.diff for correctness, security issues, and missing tests.
             Output concise Markdown findings with file references." \
            > /tmp/codex-review.md
```

如果要把结果发回 PR，需要再用 `gh pr comment` 或 GitHub API。不要让 Codex 在 CI 中自动 push 到受保护分支。

### 5.5 CI 安全底线

- 使用最小权限 `GITHUB_TOKEN`
- 设置 `timeout-minutes`
- 不把生产密钥暴露给 Codex
- 不默认 `dangerously-bypass-approvals-and-sandbox`
- 审查输出再决定是否评论或提交
- 对自动修复使用 PR 分支，不直接推 main

### 5.6 什么时候改用 SDK

如果你需要下面这些能力，SDK 比 CLI 更合适：

- 自己管理任务队列和重试
- 自己实现审批 UI
- 需要结构化事件流
- 需要和内部权限系统绑定
- 需要把结果写入工单、看板、聊天系统
- 需要多租户隔离和审计日志

CLI 适合短脚本；GitHub Action 适合仓库事件；SDK 适合产品化集成。

---

## 第6部分：Plugins / Apps / MCP 集成

### 6.1 插件是官方推荐的外部集成入口

Codex Plugins 可以包含：

- Skills：工作流
- Apps：外部服务连接，例如 Gmail、Google Drive、Slack、Linear 等
- MCP servers：工具和资源接口

安装插件后，你可以直接描述任务：

```text
总结今天未读的 Gmail 线程，并标出需要我回复的邮件。
```

也可以用 `@` 指定插件：

```text
@slack 总结 #release 频道过去 24 小时的阻塞问题。
```

### 6.2 外部 app 的权限

注意：

- 安装 plugin 不等于自动授权外部 app
- 外部 app 需要单独登录或授权
- 数据会受外部 app 的条款和隐私政策约束
- 组织账号可能限制哪些 connector 可用

### 6.3 MCP 适合什么

MCP 更适合连接工程工具：

- GitHub / GitLab
- 数据库
- 文件系统
- 文档检索
- 内部 API

团队使用 MCP 时，建议在 `AGENTS.md` 或安全文档中写清：

| 字段 | 示例 |
|------|------|
| server 名称 | github |
| 用途 | 读取 issue / PR |
| 权限 | 只读或最小写权限 |
| 凭据来源 | 环境变量 / Secret manager |
| 禁止事项 | 不允许删除 repo / 改组织设置 |

---

## 第7部分：Webhook 与自定义集成

如果你要接飞书、钉钉、企业微信或内部系统，推荐把 Codex 当执行器，而不是把桌面 App 伪装成 webhook server。

可靠架构：

```text
外部系统 Webhook
  → 你的服务校验签名和权限
  → 写入队列 / 创建工单
  → CI runner 或后台 worker 调用 Codex CLI / OpenAI API
  → 结果回写外部系统
```

这样做的好处：

- 可以审计
- 可以重试
- 可以限流
- 可以隔离密钥
- 不依赖某台桌面电脑常开

---

## 第8部分：自动化设计模板

### 8.1 Read-only 摘要任务

```text
名称：每日工程摘要
频率：工作日 09:30
范围：当前 Git 仓库，过去 24 小时
权限：Read-only
输出：Triage Markdown 摘要
无发现：自动归档
禁止：修改文件、发外部消息、push
```

### 8.2 测试守护任务

```text
名称：关键路径测试检查
频率：每 4 小时
命令：npm test -- --runInBand tests/auth
权限：Read-only
输出：失败测试、错误日志、可能根因
禁止：自动修复、安装依赖、删除缓存
```

### 8.3 文档漂移任务

```text
名称：API 文档漂移检查
频率：每周五 17:00
范围：src/api、docs/api.md
权限：Read-only
输出：不一致项和建议改动
后续：人工确认后再开 Local/Worktree 线程修文档
```

---

## FAQ

### Q1：Automations 会不会在电脑关机时运行？

A：桌面 App 的后台自动化依赖本机 App 环境。需要持续运行请用云端 runner、GitHub Actions 或 Codex Cloud 类工作流。

### Q2：可以用配置文件批量管理 Automations 吗？

A：当前官方 App 文档主线是通过 App pane 和自然语言创建/更新 Automations，而不是 `.codex/automations.toml`。不要把旧稿里的 TOML 当官方接口。

### Q3：Automations 可以自动修复 bug 吗？

A：可以设计成“发现问题后进入 Triage，再由你触发修复”。无人值守自动写代码风险较高，必须配合 Review、沙盒和最小权限。

### Q4：Slack / Linear 是不是内置在 Automations 里？

A：不要这样理解。Slack、Linear 等应通过 Plugins / Apps / MCP 连接，是否可用取决于你是否安装、授权以及组织策略。

### Q5：有没有 `@openai/codex` SDK？

A：不要按旧稿的 `new Codex().generate()` 示例写生产代码。Codex CLI 是可脚本化的；如果要构建自己的 agent 应用，优先看 OpenAI Agents SDK / Responses API / 官方 Codex 文档，而不是假设存在一个稳定的 Codex App SDK。

---

## 术语表

| 术语 | 通俗解释 |
|------|----------|
| Automation | Codex App 后台周期任务 |
| Triage / Inbox | 自动化有发现时进入的收件箱 |
| Thread automation | 绑定某个线程的后续自动化 |
| Skill | 可复用工作流，可被自动化调用 |
| Plugin | 可包含 skills、apps、MCP server 的扩展包 |
| MCP | 让 Codex 连接外部工具的协议 |
| CI runner | GitHub Actions 等云端执行环境 |
| Unattended run | 没有人盯着的后台运行，权限风险更高 |

---

## 下一步

- **CX-02**：Codex App 桌面指挥中心完整指南
- **CX-05**：Codex 配置系统完整指南
- **CX-09**：Codex 沙箱安全与企业指南
