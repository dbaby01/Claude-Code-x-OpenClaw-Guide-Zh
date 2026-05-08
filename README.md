# Claude Code & OpenClaw & Codex 中文教程

<div align="center">

![GitHub stars](https://img.shields.io/github/stars/KimYx0207/AI-Coding-Guide-Zh?style=social)
![GitHub forks](https://img.shields.io/github/forks/KimYx0207/AI-Coding-Guide-Zh?style=social)
![License](https://img.shields.io/badge/license-MIT-blue.svg)
![Claude Code](https://img.shields.io/badge/Claude_Code-2.1.133-green.svg)
![OpenClaw](https://img.shields.io/badge/OpenClaw-v2026.3.28-blue.svg)
![Codex](https://img.shields.io/badge/Codex-v0.128.0-orange.svg)

**AI 三顶流 · 中文教程扛把子**

</div>

> 🎯 **Claude Code（官方编程神器）+ OpenClaw（开源 AI 助手框架）+ Codex（OpenAI 编程 Agent）= 最强 AI 工具三教程**
>
> Codex 主线以 **Codex App** 为核心；CLI / Web / SDK / GitHub Action 等只作为 App 生态补充。

> 📚 36个完整教程 | 180,000+字 | 90+代码示例 | 230+FAQ

> ⭐ 小白友好 | 从零到企业实战 | 三线学习路径

> 🔗 **GitHub仓库**：[https://github.com/KimYx0207/AI-Coding-Guide-Zh](https://github.com/KimYx0207/AI-Coding-Guide-Zh)

> 老金的开源知识库，实时更新群二维码：https://my.feishu.cn/wiki/OhQ8wqntFihcI1kWVDlcNdpznFf

---

## 📞 联系方式

<div align="center">
  <img src="images/二维码基础款.png" alt="联系方式" width="600"/>
  <p><strong>获取更多AI资讯和技术支持</strong></p>
  <p>
    👤 <strong>作者：老金</strong> | 🔗 <a href="https://github.com/KimYx0207">GitHub</a> | 🌐 <a href="https://aiking.dev/">aiking.dev</a> | 𝕏 <a href="https://x.com/KimYx0207">老金带你玩AI</a> | 📱 微信公众号：<strong>老金带你玩AI</strong>
  </p>
</div>

### ☕ 请我喝杯咖啡

<div align="center">
  <p><strong>如果这个教程对你有帮助，欢迎打赏支持！</strong></p>
  <table align="center">
    <tr>
      <td align="center">
        <img src="images/微信.jpg" alt="微信收款码" width="300"/>
        <br/>
        <strong>微信支付</strong>
      </td>
      <td align="center">
        <img src="images/支付宝.jpg" alt="支付宝收款码" width="300"/>
        <br/>
        <strong>支付宝</strong>
      </td>
    </tr>
  </table>
</div>

---

## 📖 项目简介

这是一套**系统化、小白友好、三顶流合璧**的 AI 工具中文教程，涵盖三大王者级产品：

| | Claude Code | OpenClaw | Codex |
|--|-------------|----------|-------|
| **是什么** | Anthropic 官方 AI 编程 CLI 工具 | 开源 AI 私人助手框架 | OpenAI 编程 Agent 平台 |
| **干什么** | 终端里写代码、调Bug、做架构 | 消息平台上管邮件、排日程、自动化一切 | App / CLI / Web / IDE / SDK 分层协作的编程 Agent |
| **谁出的** | Anthropic 官方 | Peter Steinberger（原名 Clawdbot，因 Claude 商标被迫改名） | OpenAI 官方（CLI 开源 Apache-2.0） |
| **教程数** | 13 篇 + 1 速查卡 | 12 篇完整教程 | 11 篇完整教程 |

### 🧬 为什么放在一起？

**因为它们是 AI Agent 时代的一体多面：**

1. **血脉相连** — OpenClaw 原名 **Clawdbot**（Claude + Bot），DNA 里流着 Claude 的血，被 Anthropic 强制改名后依然是 Claude API 最大的社区消费者
2. **互补工具链** — Claude Code 管**编程工作流**，OpenClaw 管**日常工作流**，Codex 管**快速 Agent 任务**，合在一起就是程序员的 AI 全家桶
3. **双强协作** — Codex 和 Claude Code 通过 MCP Server 共享工具链，双工具协作是 2026 年专业开发者的正确姿势
4. **中国用户刚需** — 三大顶流 AI 工具的中文教程，一站式搞定，不用东找西找

### ✨ 核心特色

- **🎓 三线学习路径**：Claude Code 编程线 + OpenClaw 助手线 + Codex Agent 线，各取所需
- **🚀 快速上手**：Claude Code 3 小时上手 / OpenClaw 5 分钟跑起来 / Codex 20 分钟上手
- **👶 小白友好**：90+术语解释、50+生活类比、230+FAQ
- **💻 实战导向**：90+个可运行代码示例，边学边练
- **📊 质量保证**：关键版本号与 CLI 行为优先对照 **官方 Release / 文档** 修订；细节仍可能随上游快速变化，请以你本机版本为准
- **🔄 持续更新**：适配最新版本（Claude Code v2.1.133 / OpenClaw 稳定版 v2026.3.28 / Codex CLI v0.128.0，实际以 GitHub Releases 和本机 `codex --version` 为准）

---

## 📚 教程目录

### 🤖 Part 1：Claude Code — Anthropic 官方编程神器

| 序号 | 教程名称 | 学时 | 难度 | 必学度 | 说明 |
|------|---------|------|------|--------|------|
| 01 | [Claude Code完整安装指南](docs/claude-code/01-Claude-Code完整安装指南.md) | 2-3h | ⭐ | ⭐⭐⭐ | 环境搭建、API配置、IDE集成 |
| 02 | [基础使用完整指南](docs/claude-code/02-基础使用完整指南.md) | 4-6h | ⭐ | ⭐⭐⭐ | 三种使用模式、30+命令详解 |
| 03 | [Commands系统完整指南](docs/claude-code/03-Commands系统完整指南.md) | 4-6h | ⭐⭐ | ⭐⭐ | Slash 命令、Skills 工作流与兼容层 |
| 04 | [MCP集成完整指南](docs/claude-code/04-MCP集成完整指南.md) | 4-6h | ⭐⭐ | ⭐⭐⭐ | 10+核心服务器、自定义开发 |
| 05 | [Hooks系统完整指南](docs/claude-code/05-Hooks系统完整指南.md) | 4-6h | ⭐⭐ | ⭐⭐⭐ | 多事件 Hook、4 类处理器、自动化工作流 |
| 06 | [Subagent子代理完整指南](docs/claude-code/06-Subagent子代理完整指南.md) | 1-2h | ⭐⭐ | ⭐⭐ | 官方 Subagents、Task 委派、Agent Teams（实验性） |
| 07 | [Skills定制完整指南](docs/claude-code/07-Skills定制完整指南.md) | 6-8h | ⭐⭐ | ⭐⭐ | 创建可复用功能包 |
| 08 | [Plugins生态完整指南](docs/claude-code/08-Plugins生态完整指南.md) | 4-6h | ⭐⭐ | ⭐ | `/plugin`、市场、作用域与本地开发 |
| 09 | [Agent-SDK完整指南](docs/claude-code/09-Agent-SDK完整指南.md) | 6-8h | ⭐⭐⭐ | ⭐⭐ | 编程开发AI Agent |
| 10 | [综合实战完整指南](docs/claude-code/10-综合实战完整指南.md) | 2-3h | ⭐⭐⭐ | ⭐⭐ | 团队协作、CI/CD集成 |
| 11 | [企业实战完整指南](docs/claude-code/11-企业实战完整指南.md) | 4-6h | ⭐⭐⭐ | ⭐ | 企业级最佳实践 |
| 12 | [Remote Control完整指南](docs/claude-code/12-Remote-Control完整指南.md) | 1-2h | ⭐⭐ | ⭐⭐ | 跨设备继续本地会话、`/remote-control`、`claude remote-control` |
| 13 | [Channels与计划任务完整指南](docs/claude-code/13-Channels与计划任务完整指南.md) | 2-3h | ⭐⭐⭐ | ⭐ | `--channels`、`/schedule`、`/loop`、`CronCreate` |

**速查**：[Claude Code 快速导航卡](docs/claude-code/快速导航卡.md)

### 🦞 Part 2：OpenClaw — 开源 AI 助手

| 序号 | 教程名称 | 难度 | 说明 |
|------|---------|------|------|
| OC-00 | [阅读指南](docs/openclaw/00-阅读指南.md) | 🟢 | 术语表、文档地图、4条阅读路线 |
| OC-01 | [项目介绍](docs/openclaw/01-OpenClaw项目介绍.md) | 🟢 | OpenClaw 是什么、发展历史、核心架构 |
| OC-02 | [安装部署](docs/openclaw/02-安装部署指南.md) | 🟢 | macOS / Linux / Windows 全平台安装 |
| OC-03 | [快速开始](docs/openclaw/03-快速开始指南.md) | 🟢 | 5 分钟跑起来第一个对话 |
| OC-04 | [AI 模型配置](docs/openclaw/04-模型配置指南.md) | 🟡 | 接入 OpenAI / Claude / Ollama 等模型 |
| OC-05 | [消息平台接入](docs/openclaw/05-消息平台接入指南.md) | 🟡 | 连接 WhatsApp / Telegram / Discord / 飞书等平台 |
| OC-06 | [技能系统](docs/openclaw/06-技能系统指南.md) | 🟡 | 技能生态与自定义技能开发 |
| OC-07 | [记忆系统](docs/openclaw/07-记忆系统指南.md) | 🟡 | AI 如何记住你的偏好和上下文 |
| OC-08 | [多 Agent 协作](docs/openclaw/08-多Agent协作指南.md) | 🔴 | 一个网关跑多个独立 AI 助手 |
| OC-09 | [Docker 部署](docs/openclaw/09-Docker部署指南.md) | 🔴 | 容器化部署与 VPS 远程访问 |
| OC-10 | [安全配置](docs/openclaw/10-安全配置指南.md) | 🔴 | 安全配置、CVE 防护、权限管理 |
| OC-11 | [常见问题](docs/openclaw/11-常见问题FAQ.md) | 🟢 | 踩坑指南与解决方案 |

### 🤖 Part 3：Codex — OpenAI 编程 Agent 平台

Codex 学习主线：先看 CX-01 安装认证，再重点看 CX-02 Codex App；其余 CX-03 到 CX-09 作为 App 生态补充和团队落地参考；CX-10 综合实战串联全系列；CX-11 为可选对比附录。

| 序号 | 教程名称 | 学时 | 难度 | 说明 |
|------|---------|------|------|------|
| CX-01 | [Codex安装与认证完整指南](docs/codex/CX-01-Codex安装与认证完整指南.md) | 2-3h | ⭐ | App 优先安装、ChatGPT 登录、Windows/macOS、CLI/API Key 仅作补充 |
| CX-02 | [Codex App桌面指挥中心完整指南](docs/codex/CX-02-Codex-App桌面指挥中心完整指南.md) | 4-6h | ⭐⭐ | App 主教程：Local/Worktree、Review、Settings、Plugins、Automations、Browser、Computer Use、Commands、Windows 排障 |
| CX-03 | [Codex CLI终端Agent完整指南](docs/codex/CX-03-Codex-CLI终端Agent完整指南.md) | 3-4h | ⭐⭐ | App 补充：排查配置、MCP、非交互任务时再看 |
| CX-04 | [Codex Web云端Agent完整指南](docs/codex/CX-04-Codex-Web云端Agent完整指南.md) | 2-3h | ⭐⭐ | App 补充：需要云端后台任务或 PR 工作流时再看 |
| CX-05 | [Codex配置系统完整指南](docs/codex/CX-05-Codex配置系统完整指南.md) | 4-6h | ⭐⭐ | App 补充：AGENTS.md、config.toml、Rules、Hooks、Plugins、MCP |
| CX-06 | [Codex Skills技能系统完整指南](docs/codex/CX-06-Codex-Skills技能系统完整指南.md) | 3-5h | ⭐⭐⭐ | App 补充：Skills 工作流、自定义 skill、团队共享 |
| CX-07 | [Codex MCP与Subagents完整指南](docs/codex/CX-07-Codex-MCP与Subagents完整指南.md) | 3-4h | ⭐⭐⭐ | App 补充：接外部工具、多 Agent、MCP 配置边界 |
| CX-08 | [Codex Automations与集成完整指南](docs/codex/CX-08-Codex-Automations与集成完整指南.md) | 3-4h | ⭐⭐ | App 自动化：Triage、thread automation、test automation、插件/MCP 集成 |
| CX-09 | [Codex沙箱安全与企业指南](docs/codex/CX-09-Codex沙箱安全与企业指南.md) | 3-4h | ⭐⭐⭐ | App 安全：沙盒、审批、rules、hooks、插件/MCP 权限、Memories |
| CX-10 | [Codex综合实战完整指南](docs/codex/CX-10-Codex综合实战完整指南.md) | 2-3h | ⭐⭐⭐ | 全系列串联实战：从零搭建项目、配置、Skills、MCP、Automations、安全全流程 |
| CX-11 | [Codex与Claude Code对比实战指南](docs/codex/CX-11-Codex与Claude-Code对比实战指南.md) | 2-3h | ⭐⭐⭐ | 可选附录：双工具协作、MCP 共享、团队决策 |

---

## 📋 环境要求

### Claude Code

- **操作系统**：Windows 10+、macOS 10.15+、Linux
- **安装方式**：支持标准安装（`npm install -g @anthropic-ai/claude-code`，需 Node.js 18+）和原生二进制安装（beta / 改进安装路径）
- **认证方式**：可用 Claude 订阅登录，也可用 Anthropic Console / 第三方兼容提供商配置
- **IDE**：VS Code、Cursor、Windsurf 或其他支持的编辑器

> ⚠️ **2026年更新**：Claude Code 已提供原生二进制安装，但 **Node.js 18+ 的标准 npm 安装路径仍然受支持**。本仓库安装指南现同时覆盖两条路径，并明确各自适用场景。

### OpenClaw

- **Node.js**：24.x 推荐，22.14+ 兼容
- **AI 模型 API Key**：OpenAI / Anthropic / Google 等（或使用 Ollama 本地模型免 Key）
- **操作系统**：macOS / Linux / Windows（推荐 WSL2）

### Codex

- **Codex App**：macOS / Windows 桌面端（macOS 可用 Homebrew Cask；Windows 以 Microsoft Store / winget 等官方安装入口为准；Linux 使用 CLI 或 Web）
- **Codex CLI**：Node.js 22+（`npm install -g @openai/codex`）；也可按官方 CLI 文档选择 Homebrew Cask 等路径
- **Codex Web / Cloud**：现代浏览器 + 支持 Codex 的 ChatGPT 账号或组织工作区
- **认证方式**：ChatGPT 账户登录 或 OpenAI API Key

---

## 🚀 快速开始

### 克隆仓库

```bash
git clone https://github.com/KimYx0207/AI-Coding-Guide-Zh.git
cd AI-Coding-Guide-Zh
```

### Claude Code 路线（3小时上手）

```
Step 1（60分钟）：01-安装指南 → 路径A快速上手
Step 2（30分钟）：04-MCP集成 → 第2部分快速开始
Step 3（30分钟）：05-Hooks系统 → 第2部分快速开始
完成 ✅ 能用Claude Code + 能用MCP + 能用Hook
```

### OpenClaw 路线（1小时上手）

```
Step 1（15分钟）：OC-01 项目介绍 → 了解全局
Step 2（20分钟）：OC-02 环境安装 → 装好环境
Step 3（10分钟）：OC-03 快速开始 → 跑起第一个对话
Step 4（15分钟）：OC-04 模型配置 → 接入 AI 模型
完成 ✅ 能用OpenClaw + 能和AI对话
```

### Codex 路线（20分钟上手）

```
Step 1（10分钟）：CX-01 安装与认证 → 装好 Codex + 配好认证
Step 2（10分钟）：CX-02 App 或 CX-03 CLI → 跑起第一个任务
完成 ✅ 能用Codex + 能让AI写代码
```

### 完整掌握路径

```
Week 1-2：Claude Code 安装 + 基础使用 + MCP
Week 3-4：Claude Code Hooks + Skills + Plugins
Week 5  ：Claude Code 模型配置 + Remote Control + Channels/计划任务
Week 6  ：Claude Code Agent-SDK + 综合实战
Week 7  ：OpenClaw 安装 + 快速开始 + 模型配置
Week 8  ：OpenClaw 消息平台 + 技能系统 + 记忆系统
Week 9  ：OpenClaw 多Agent + Docker部署 + 安全
Week 10 ：Codex 安装 + App + CLI 基础
Week 11 ：Codex 配置系统 + Skills + MCP
Week 12 ：Codex Automations + 安全 + 与 Claude Code 协作
```

---

## 📊 项目统计

| 指标 | 数值 |
|------|------|
| **教程总数** | 36 篇完整教程（Claude Code 13 / OpenClaw 12 / Codex 11）+ 1 速查卡 |
| **总字数** | ~180,000 字 |
| **代码示例** | 90+个（全部可运行） |
| **FAQ数量** | 230+ 个 |
| **覆盖AI模型** | 29 个提供商（OpenClaw） |
| **覆盖消息平台** | 15+ 个（OpenClaw） |
| **Claude Code版本** | 2.1.133（2026-05-08 验证） |
| **OpenClaw版本** | 稳定版 **v2026.3.28**（2026-04-05 对照 [Releases](https://github.com/openclaw/openclaw/releases)）；列表含预发布时请自行取舍 |
| **Codex版本** | CLI v0.128.0（2026-04-30 GitHub Release）；本仓库按 2026-05 官方文档修订，实际以 `codex --version` 与 [GitHub](https://github.com/openai/codex) 为准 |

---

## 🔖 版本说明

> **版本校验方法**：本仓库教程中的版本号和 CLI 行为，优先对照 **官方 Release / 官方文档** 修订。上游产品迭代很快，部分细节可能在你阅读时已发生变化。
>
> **遇到版本不一致时**：以你本机 `claude --version`、`codex --version` 或 `npm list -g` 的输出为准，教程示例按官方最新文档调整。

| 产品 | 当前验证版本 | 验证日期 | 官方来源 |
|------|-------------|---------|---------|
| Claude Code | v2.1.133 | 2026-05-08 | [GitHub Releases](https://github.com/anthropics/claude-code/releases) |
| OpenClaw | 稳定版 v2026.3.28 | 2026-04-05 | [GitHub Releases](https://github.com/openclaw/openclaw/releases) |
| Codex CLI | v0.128.0 | 2026-04-30 | [GitHub Releases](https://github.com/openai/codex) |

> ⚠️ **版本号会随上游更新而失效**。教程中标注的版本号是编写/验证时的快照，不代表实时最新。遇到命令或参数不一致时，优先查官方 Release Notes 和 CLI 帮助（`--help`）。

---

## 🔌 第三方模型配置说明

三大工具均支持多种模型接入方式，具体配置见各教程：

| 产品 | 支持方式 | 配置入口 |
|------|---------|---------|
| **Claude Code** | Anthropic Console / Claude 订阅 / 第三方兼容提供商（`ANTHROPIC_BASE_URL`） | [01-安装指南：API中转站配置](docs/claude-code/01-Claude-Code完整安装指南.md) |
| **OpenClaw** | 29 个提供商（OpenAI / Claude / Gemini / Ollama / 本地模型等） | [04-模型配置指南](docs/openclaw/04-模型配置指南.md) |
| **Codex** | ChatGPT 账户登录 / OpenAI API Key | [CX-01 安装与认证](docs/codex/CX-01-Codex安装与认证完整指南.md) |

> ⚠️ **第三方模型注意事项**：
> - 第三方兼容提供商的 API 行为可能不完全等同于官方（速率限制、模型列表、功能支持可能有差异）
> - 本地模型（Ollama 等）能力取决于模型本身，复杂任务可能不如旗舰模型
> - 各提供商计费方式不同，使用前请确认价格策略

---

## 🎯 适用人群

- ✅ **零基础小白**：从未接触过 AI 编程工具，想系统学习
- ✅ **开发者**：想用 Claude Code 提升编程效率 + 用 OpenClaw 自动化日常工作
- ✅ **团队负责人**：为团队制定 AI 工具使用规范
- ✅ **企业用户**：企业级部署和最佳实践
- ✅ **AI 爱好者**：想搭建自己的 AI 私人助手

---

## 💡 学习建议

### 初学者（零基础）

**想学编程 AI** → 从 Claude Code Part 1 开始（01 → 04 → 05）

**想玩 AI 助手** → 从 OpenClaw Part 2 开始（OC-01 → OC-02 → OC-03）

**想试 Codex** → 从 Codex Part 3 开始（CX-01 → CX-02 或 CX-03）

**都想学** → 先 Claude Code 3 小时上手，再 Codex 20 分钟上手，最后 OpenClaw 1 小时上手

### 进阶者（有基础）

- Claude Code 重点：04-MCP、05-Hooks、06-Subagent、07-Skills
- OpenClaw 重点：06-技能系统、08-多Agent路由
- Codex 重点：CX-05 配置系统、CX-06 Skills、CX-07 MCP

### 高级者（深度定制）

- Claude Code：09-Agent-SDK、10-综合实战
- OpenClaw：08-多Agent、09-Docker部署、10-安全
- Codex：CX-09 沙箱安全、CX-10 综合实战、CX-11 双工具协作
- **双工具协作**：Codex + Claude Code 通过 MCP Server 共享工具链（详见 CX-11）
- 长期 Skill 治理：可参考 [SkillClaw](https://github.com/AMAP-ML/SkillClaw)（skill 演化、去重、合并、共享；论文 [arXiv:2604.08377](https://arxiv.org/abs/2604.08377)）

---

## 🤝 贡献指南

欢迎提交 Issue 和 Pull Request！

- 发现错误或过时信息，请提交 Issue
- 有改进建议，欢迎提交 PR
- 想分享使用经验，欢迎在 Discussions 讨论

---

## 📄 许可证

本项目采用 [MIT License](LICENSE) 开源协议。

---

## 🙏 致谢

感谢所有为 Claude Code 和 OpenClaw 生态做出贡献的开发者和社区成员！

---

## 📋 更新日志

### v4.1 (2026-05-08) — 全库审计与内容增强

**Phase 3 审计修复（已验证）**
- CX-03 / CX-05：`--full-auto` 标注为 deprecated（2026年4月底 CLI 发布已废弃），补充推荐替代写法 `--sandbox workspace-write`
- 01-安装指南：权限配置路径从 `.claude/config.json` 修正为 `.claude/settings.json`（`permissions.allow` 字段），附官方权限文档链接
- 排除2条误报：`actions/checkout@v5`（v5.0.0 已发布）和 `@openai/codex` 包名（官方正确包名）

**Phase 5 内容更新（v2.1.93→v2.1.133 新功能）**
- 01-安装指南：effort 五级（xhigh/max）、Opus 4.7 模型名
- 02-基础使用：Monitor tool、Session recap、自定义主题、Native binaries、Opus 4.7 默认、/skills、/proactive
- 03-Commands：/proactive、/skills 搜索、/color random、/mcp 工具计数、skillOverrides
- 04-MCP：`alwaysLoad` 跳过 ToolSearch 懒加载
- 05-Hooks：effort.level + `$CLAUDE_EFFORT`、PostToolUse 替换输出、duration_ms
- 06-Subagent：`worktree.baseRef`、`CLAUDE_CODE_FORK_SUBAGENT`、Skill 发现修复
- 07-Skills：`skillOverrides`（off/user-invocable-only/name-only）、`/skills` 搜索过滤
- 08-Plugins：自定义主题、`plugin prune`、`--plugin-url`、`.zip` 支持
- 09-SDK：`CLAUDE_CODE_SESSION_ID`、`localSettings`、`mcp_authenticate redirectUri`
- 11-企业实战：`parentSettingsBehavior`、`claude project purge`、Bedrock service tier
- 12-Remote-Control：v2.1.121+ 和 v2.1.133 增强条目
- 13-Channels：`--channels` 支持 Console auth（v2.1.128）

**Phase 4 内容增强**
- README 新增"版本说明"区块：版本校验方法、验证版本快照表、版本不一致处理指引
- README 新增"第三方模型配置说明"入口：三大工具支持的模型接入方式和配置教程链接
- README 版本号对齐：徽章更新至 Claude Code v2.1.133、统计表修正为 36 篇教程（Codex 11 篇）

### v4.0 (2026-05-07) — 三顶流合璧：新增 Codex 系列

**重大更新**
- 项目升级为 **Claude Code & OpenClaw & Codex 三教程**
- 新增 Codex (OpenAI 编程 Agent 平台) 11 篇完整中文教程
- 目录结构扩展：`docs/codex/`
- README 新增 Part 3 Codex 教程目录、环境要求、快速开始路线

**Codex 教程内容**
- CX-01 安装与认证：CLI/App/IDE/Web/SDK 分层安装、ChatGPT 登录、API Key、Windows/macOS/Linux 路径
- CX-02 App 桌面指挥中心：Local/Worktree、Review、Settings、Plugins、Automations、Computer Use、内置浏览器
- CX-03 CLI 终端 Agent：`approval_policy`、沙盒、命令行选项、非交互模式、review
- CX-04 Web 云端 Agent：chatgpt.com/codex、环境配置、Internet Access、PR 工作流
- CX-05 配置系统：AGENTS.md、config.toml、approval_policy、sandbox_mode、Rules、Hooks、Plugins
- CX-06 Skills 技能系统：Skills 定位、自定义开发、团队共享、与 Plugins/MCP/Hooks 边界
- CX-07 MCP 与 Subagents：MCP 配置、`codex app-server` 边界、Subagents、自定义 Agents
- CX-08 Automations 与集成：App Automations、Triage、thread automation、CI/CLI、Plugins/MCP 集成
- CX-09 沙箱安全与企业：审批、沙盒、rules、hooks、插件/MCP 权限、企业 requirements
- CX-10 Codex 综合实战：全系列串联项目、配置、Skills、MCP、Automations
- CX-11 Codex vs Claude Code：全面对比、双工具协作实战、社区生态

**验证版本**
- Codex CLI: v0.128.0（2026-04-30 GitHub Release）；以本机 `codex --version` 和 [GitHub Releases](https://github.com/openai/codex) 为准
- Codex App: 以桌面 App 内更新与 OpenAI 官方 Codex App 文档为准

### v3.1 (2026-03-18) | Claude Code v2.1.69-v2.1.78 新功能补充

### v3.2 (2026-04-05) | Claude Code v2.1.92 + OpenClaw 差量同步

**本次重点修正**
- Claude Code：同步安装、基础使用、MCP、Agent SDK、Commands、Hooks、Subagents、Plugins、企业实战等章节的版本口径
- Claude Code：新增 2 篇缺失专题教程（Remote Control、Channels/计划任务），模型配置整合到安装指南
- Claude Code：修正 README 与安装教程中“原生安装 = 完全不需要 Node.js”这一过满表述，改为“原生安装与 npm 标准安装并存”
- Claude Code：补齐 `/plan`、`/plugin`、`/release-notes`、`/rewind`、`/insights`、`/schedule`、`/statusline` 等较新内置命令的上下文说明
- Claude Code：补齐 `/btw`、`/config`、`/desktop`、`/diff`、`/install-github-app`、`/mobile`、`/reload-plugins`、`/remote-control`、`/tasks`、`/theme`、`/ultraplan` 等当前命令面
- Claude Code：修正 Hooks 章节中“固定 20 种 Hook”这一过时说法，改为按事件族和处理器类型理解
- Claude Code：修正 Subagent 章节中过度依赖第三方代理包的问题，明确官方 Subagents 与实验性 Agent Teams 的边界
- Claude Code：修正 Plugins 章节中过时的本地加载叙述，补上当前 `/plugin`、market、scope、manifest、LSP、`/reload-plugins`、持久化数据目录等结构
- Claude Code：补充“自定义 slash 工作流已并入 Skills 体系，`.claude/commands` 主要作为兼容层”的说明
- OpenClaw：同步阅读指南、项目介绍、安装部署、快速开始、模型配置、技能系统、FAQ 等章节的当前主路径
- OpenClaw：统一安装基线为“Node 24 推荐，22.14+ 兼容”，补充官方 `install.sh` 快速安装路径
- OpenClaw：统一 Windows / WSL2 表述为“原生可用，WSL2 更稳定”
- OpenClaw：清理 README 和项目介绍中容易过时的 Stars / 历史增长数字表述

### v3.3 (2026-04-05) | OpenClaw 稳定版基线与全文口径

- OpenClaw：README 徽章与统计表改为 **稳定版 v2026.3.28**（与 [GitHub Releases](https://github.com/openclaw/openclaw/releases) 2026-04-05 页面对照）；说明 **预发布**（如 v2026.4.1-beta.1）可能与稳定版并存
- OpenClaw：OC-00 / OC-02 及 OC-01/03/04/05/06/07/08/09/10/11 文首补充 **教程版本基线** 交叉引用，减少各篇版本表述漂移
- OpenClaw：目录与贡献路线用语中「源码」改为「仓库 / 本地构建」等中性表述（安装篇章节标题同步）

### v3.0 (2026-02-25) — 双顶流合璧

**重大更新**
- 项目升级为 **Claude Code & OpenClaw 双教程**
- 仓库更名为 `AI-Coding-Guide-Zh`
- 新增 OpenClaw 11 篇完整中文教程（基于 v2026.2.23）+ 1 篇阅读指南
- 目录结构重组：`docs/claude-code/` + `docs/openclaw/`
- OpenClaw 全部文档经官方文档与发行版 CLI 对照校验，修正技术错误（配置格式、端口、CLI 命令、平台支持等）
- OpenClaw 全部文档加入小白友好元素（难度标记、术语解释、阅读路线、可跳过标记）

**OpenClaw 教程内容**
- OC-00 阅读指南：术语表、文档地图、4条阅读路线（小白速通/日常使用/深度定制/生产部署）
- OC-01 项目介绍：发展历史、核心架构、15+消息平台、29个AI模型提供商
- OC-02 环境安装：macOS / Linux / Windows / Docker 全平台安装
- OC-03 快速开始：5 分钟跑起第一个对话
- OC-04 AI 模型配置：OpenAI / Claude / Gemini / Ollama 等模型接入
- OC-05 消息平台集成：WhatsApp / Telegram / Discord / 飞书等连接
- OC-06 技能系统：50+ 内置技能详解与自定义技能开发
- OC-07 记忆系统：Markdown 记忆文件、自动刷新、多语言记忆
- OC-08 多 Agent 路由：多 Agent 配置、消息路由、人格设定
- OC-09 Docker 部署：容器化部署、VPS 部署、Fly.io / Render
- OC-10 安全最佳实践：Gateway Token、TLS 1.3、沙箱隔离
- OC-11 常见问题：安装/连接/模型/记忆/性能/中国用户专区

**Claude Code 教程补充**
- 08-Agent-SDK 新增 3.5 节：子代理编排（历史稿使用 Task 工具表述；当前版本已校准为 Agent 工具）
- 02-基础使用 /permissions 节新增权限模式切换（plan/acceptEdits/bypassPermissions）和 allowedTools 配置
- 04-MCP集成新增 MCP Apps 交互式界面、工具懒加载 ToolSearch（上下文减少 95%）、远程 MCP 服务器
- README 联系方式新增 X(Twitter) 和微信公众号信息

### v2.0 (2026-02-25) — 适配 Claude Code 2.1.52

**版本同步更新**
- 全部 10 篇教程版本号从 v2.0+ 更新至 v2.1+，验证日期更新至 2026-02-25
- README 版本徽章从 2.1.19 更新至 2.1.52
- 移除 Node.js 环境依赖要求（Claude Code 已切换原生安装）

**新增内容**
- 05-Hooks教程新增 3.7 节：WorktreeCreate / WorktreeRemove 工作树管理 Hook 详解
- 05-Hooks教程新增 `--worktree`（`-w`）内置 Git Worktree 并行任务隔离功能详解（v2.1.49+）
- 05-Hooks教程 Hook 类型从 6 种扩展至 8 种，术语表和速查表同步更新
- 02-基础使用新增 `--worktree`（`-w`）启动参数说明
- 02-基础使用新增 /fast 快速模式命令说明
- 01-安装指南新增 4.3.1 节：API中转站配置（ANTHROPIC_BASE_URL 环境变量）

**修复与优化**
- 修复快速导航卡中"安装Node.js"的过时描述，改为"原生安装Claude Code"
- 修复 02-基础使用教程中版本号示例（v2.0.71 → v2.1.52）
- 清理 06/09/10 教程中 23 处 "2.10+/2.1.0+" 旧版本标记

**GitHub Issues**
- 回复 Issue #5：连接失败排查步骤（HTTPS 代理配置）
- 回复 Issue #2：SQLite MCP 包名验证与替代方案

**已完成（原待办项）**
- ✅ 08-Agent-SDK 新增 3.5 节：子代理编排（当前版本使用 Agent 工具表述）
- ✅ 02-基础使用 /permissions 节新增权限模式切换、allowedTools 配置说明
- ✅ 04-MCP集成 新增 MCP Apps、工具懒加载（v2.1.52+）、远程 MCP 服务器说明

### v1.0 (2026-02-11) — 初始发布

- 10 篇完整 Claude Code 教程，约 104,000 字
- 基于 Claude Code 2.1.19 版本编写
- 70+ 代码示例，120 个 FAQ

---

## ⚠️ 免责声明

- Claude Code 教程基于 **v2.1.133** 编写（[Release](https://github.com/anthropics/claude-code/releases/tag/v2.1.133)）；OpenClaw 教程以 **稳定版 v2026.3.28** 为修订参考；Codex 教程按 **2026-05 OpenAI 官方 Codex 文档** 修订，CLI/App 版本以本机和官方 Releases 为准
- **预发布与 `latest` 以各项目 [Releases](https://github.com/openclaw/openclaw/releases) 与本机版本为准**（持续更新中）
- 部分功能可能随版本更新而变化，请以官方文档为准
- 使用本教程产生的任何问题，作者不承担责任

---

<div align="center">
  <p>⭐ 如果这个教程对你有帮助，欢迎 Star 支持！</p>
  <p>📢 分享给更多需要的人，让 AI 工具学习更简单！</p>
</div>
