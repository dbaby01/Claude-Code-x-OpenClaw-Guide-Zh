# Codex 安装与认证完整指南：从零开始到成功运行

> **课程信息**
>
> - **作者**：老金
> - **GitHub**：https://github.com/KimYx0207
> - **公众号**：老金带你玩AI
> - **X（Twitter）**：老金带你玩AI
> - **个人博客**：https://aiking.dev
> - **预计学时**：2-3小时
> - **难度等级**：⭐ 零基础入门
> - **更新日期**：2026年5月
> - **适用版本**：Codex CLI v0.129.0（核对于 2026-05-08；以 `codex --version` 和 GitHub Releases 为准）/ Codex App 当前版本
> - **信息来源**：[Codex GitHub](https://github.com/openai/codex) | [Codex App 文档](https://developers.openai.com/codex/app/) | [Codex Models 文档](https://developers.openai.com/codex/models)

---

## 重要校准：本系列以 Codex App 为主线

本系列的主线是 Codex App。其他入口保留为补充，是因为 App 用户仍可能遇到 CLI 配置、Cloud handoff、MCP、SDK 或 GitHub Action 等边界问题，但它们不是本教程的学习主题。

Codex 现在至少有这些使用入口：

| 入口 | 面向谁 | 安装/访问方式 |
|------|--------|---------------|
| Codex App | 桌面端用户，本地项目、Review、Worktree、Automations | macOS / Windows 桌面安装入口 |
| Codex CLI | 终端用户、脚本、CI、MCP、插件管理 | `npm install -g @openai/codex` 或官方支持的包管理方式 |
| Codex IDE | VS Code / Cursor 等编辑器用户 | 编辑器扩展 |
| Codex Web / Cloud | 云端任务、远程仓库、PR 工作流 | ChatGPT / Codex 网页入口 |
| Codex SDK / App Server | 开发者自动化、嵌入式 agent 工作流 | 官方 SDK / CLI server 能力 |

这五类能力有关联，但不是“所有功能完全相同”。App 的 Review / Worktree / Automations 是本系列核心；CLI、Web、SDK 只在 App 用户需要排查或扩展时阅读。

---

## 📚 本课学习目标

完成本课后，你将能够：

1. 在 macOS / Windows 上安装或启动 Codex App
2. 用 ChatGPT 登录完成 App 认证
3. 理解 API Key、CLI、Web、SDK 这些补充入口的边界
4. 知道 Linux 用户为什么优先走 CLI / Web，而不是 App
5. 理解模型、订阅、API Key 和版本号的边界
6. 排查常见安装和认证问题

---

## 第1部分：Codex 生态全貌

### 1.1 Codex 与 ChatGPT / API 的关系

| 项目 | 说明 |
|------|------|
| ChatGPT 登录 | 适合个人和团队用户，使用 ChatGPT 账号授权 Codex |
| API Key | 适合脚本、CI、服务器或按 API 计费场景 |
| 模型可用性 | 不同认证方式、订阅计划、组织策略下可用模型可能不同 |
| 计费 | ChatGPT 订阅额度和 OpenAI API 计费不是一回事 |

截至 2026-05-07，官方 Codex Models 文档给出的主线是：多数 Codex 任务优先从 `gpt-5.5` 开始；账号还没看到时继续用 `gpt-5.4`；轻量任务或 subagents 可用 `gpt-5.4-mini`；`gpt-5.3-codex` 仍是专门的复杂软件工程模型；`gpt-5.3-codex-spark` 是 ChatGPT Pro 的实时迭代预览模型。`gpt-5.5` 当前需要 ChatGPT 登录，不支持 API-key authentication。价格和额度不要写死，具体看官方 Models / Pricing 页面。

### 1.2 选择入口

```text
只想本地用桌面界面 → Codex App
喜欢终端和脚本 → Codex CLI
想在编辑器里协作 → Codex IDE 扩展
想让云端跑仓库任务 → Codex Web / Cloud
想把 Codex 嵌入自己的系统 → Codex SDK / App Server
```

---

## 第2部分：系统要求

### 2.1 平台支持

| 平台 | App | CLI | Web | IDE | SDK |
|------|-----|-----|-----|-----|-----|
| macOS | 支持 | 支持 | 支持 | 支持 | 支持 |
| Windows | 支持 | 支持 | 支持 | 支持 | 支持 |
| Linux | 无桌面 App 主路径 | 支持 | 支持 | 支持 | 支持 |

App 的最低系统版本和安装入口以官方下载页为准。Linux 用户优先使用 CLI 或 Web。

### 2.2 CLI 依赖

标准 CLI 安装需要 Node.js 22+：

```bash
node -v
npm install -g @openai/codex
codex --version
```

如果你用 Homebrew Cask 安装 App，可能同时得到桌面 App 和 CLI，具体以当前官方包行为为准。

---

## 第3部分：认证方式

### 3.1 ChatGPT 账户登录

适合大多数个人用户：

```bash
codex login
```

或直接运行：

```bash
codex
```

首次启动会引导登录。登录后 App、CLI、IDE 能否共享认证，取决于当前安装方式和账号状态。

### 3.2 API Key 认证

适合 CI、脚本、服务器、自动化：

```bash
# macOS / Linux
export OPENAI_API_KEY="sk-proj-..."

# Windows PowerShell
$env:OPENAI_API_KEY = "sk-proj-..."
```

持久化到 shell 配置或 CI secrets 时，注意不要把 key 写进仓库。

### 3.3 认证选择建议

| 场景 | 建议 |
|------|------|
| 个人桌面 App | ChatGPT 登录 |
| 本地 CLI 日常使用 | ChatGPT 登录或 API Key |
| GitHub Actions / CI | API Key / 组织级 secret |
| 企业统一管理 | 组织策略 + 最小权限凭据 |

---

## 第4部分：安装 Codex App

### 4.1 macOS

常见路径：

```bash
brew install --cask codex
```

也可以从官方页面下载安装。安装后可从系统应用启动，或尝试：

```bash
codex app
```

### 4.2 Windows

Windows 桌面 App 以官方 Microsoft Store / winget 等入口为准。常见形式：

```powershell
winget install Codex -s msstore
```

如果 `winget` 找不到包，先检查 Microsoft Store、地区可用性和官方 Codex App 下载页。

### 4.3 Linux

Linux 当前优先使用 CLI 或 Web。不要按 macOS App 的路径套到 Linux。

---

## 第5部分：安装 Codex CLI

### 5.1 npm 安装

```bash
npm install -g @openai/codex
codex --version
codex --help
```

### 5.2 常用启动

```bash
# 交互模式
codex

# 直接带提示词
codex "检查这个项目的测试命令，并告诉我如何运行最小测试"

# 指定工作目录
codex --cd /path/to/project "总结这个项目结构"
```

### 5.3 非交互模式

```bash
codex exec "运行类型检查并总结失败原因"
codex review --uncommitted
codex review --base main
```

具体参数以：

```bash
codex exec --help
codex review --help
```

为准。

---

## 第6部分：IDE、Web 与 SDK

### 6.1 IDE 扩展

在 VS Code / Cursor 等编辑器扩展市场搜索 OpenAI Codex 或按官方 IDE 文档安装。安装后通常需要登录 ChatGPT 或配置 API Key。

### 6.2 Codex Web / Cloud

使用浏览器访问官方 Codex / ChatGPT 入口。云端任务运行在远程环境中，不等于本地 App 的 Local / Worktree。

### 6.3 SDK

Codex SDK 是开发者能力，不是普通用户安装 Codex 的必经路径。官方 TypeScript SDK 包名是 `@openai/codex-sdk`；Python SDK 目前按官方 SDK 文档说明处理，不要使用旧稿里的 `pip install openai-codex` 或 `new Codex().generate()` 示例。

---

## 第7部分：版本管理

### 7.1 版本号怎么写

截至 2026-05-08，`openai/codex` GitHub Releases 已更新到 v0.129.0（2026-05-08）。Codex CLI 迭代很快，教程应同时写“当前核对版本”和“本机验证命令”，不要把某个 release 永久写成最新：

```bash
codex --version
```

再对照 GitHub Releases 和官方文档。

### 7.2 团队版本约定

团队可以在 `AGENTS.md` 写清：

```markdown
## Codex
- 推荐使用当前团队验证过的 Codex CLI 版本
- 运行 `codex --version` 后确认与团队基线一致
- 配置变更见 `.codex/config.toml` 和 CX-05
```

DotSlash 可以作为高级团队版本锁定方案，但不是 Codex 官方主线安装方式。除非团队已经维护可验证的 `.slash` 文件和 release URL，不要把它写成“团队推荐默认路径”。

---

## 第8部分：故障排查

### 8.1 `codex` 命令找不到

```bash
npm root -g
npm bin -g
```

把 npm 全局 bin 目录加入 PATH，或重开终端。

### 8.2 Node.js 版本过低

```bash
node -v
```

升级到 Node.js 22+ 后重新安装：

```bash
npm install -g @openai/codex
```

### 8.3 登录打不开浏览器

尝试：

```bash
codex login
```

复制终端中的登录链接到浏览器。如果公司网络拦截 OAuth 回调，需要检查代理、防火墙或换网络。

### 8.4 API Key 不生效

```bash
echo $OPENAI_API_KEY
codex --help
```

Windows PowerShell：

```powershell
echo $env:OPENAI_API_KEY
```

确认 key 没有写错、没有多余空格、CI secret 名称正确。

---

## FAQ

### Q1：必须安装 App 才能用 CLI 吗？

A：不必须。CLI 可以单独安装和使用。

### Q2：API Key 和 ChatGPT 登录哪个更好？

A：个人交互优先 ChatGPT 登录；CI、脚本、服务器优先 API Key。

### Q3：Codex App 支持 Linux 吗？

A：当前桌面 App 主路径是 macOS / Windows。Linux 用户优先使用 CLI 或 Web。

### Q4：当前 Codex 推荐用哪个模型？

A：官方当前建议多数任务优先用 `gpt-5.5`，不可用时用 `gpt-5.4`，轻量任务或 subagents 用 `gpt-5.4-mini`。但模型可用性受认证方式、订阅、组织策略和 rollout 影响，最终以模型选择器、`/model` 和官方 Models 文档为准。

### Q5：SDK 应该怎么装？

A：TypeScript 看 `@openai/codex-sdk` 官方文档；Python 按官方实验性说明安装。不要使用旧稿中的 `@openai/codex` SDK 示例。

---

## 术语表

| 术语 | 通俗解释 |
|------|----------|
| Codex App | 桌面端工作台 |
| Codex CLI | 终端里的 Codex |
| Codex Web / Cloud | 云端运行的 Codex |
| IDE 扩展 | 编辑器里的 Codex 入口 |
| SDK | 给开发者嵌入 Codex 能力的接口 |
| ChatGPT 登录 | 用 ChatGPT 账号授权 |
| API Key | OpenAI API 凭据 |

---

## 下一步

- **CX-02**：Codex App 桌面指挥中心完整指南
- **CX-03**：Codex CLI 终端 Agent 完整指南
- **CX-05**：Codex 配置系统完整指南
