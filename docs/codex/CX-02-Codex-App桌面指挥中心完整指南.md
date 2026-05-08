# Codex App 桌面指挥中心完整指南：多线程本地开发的核心界面

> **课程信息**
>
> - **作者**：老金
> - **GitHub**：https://github.com/KimYx0207
> - **公众号**：老金带你玩AI
> - **X（Twitter）**：老金带你玩AI
> - **个人博客**：https://aiking.dev
> - **预计学时**：4-6小时
> - **难度等级**：⭐⭐ 基础进阶
> - **更新日期**：2026年5月
> - **适用版本**：Codex App 当前版本 / Codex CLI v0.129.0（核对于 2026-05-08；以 `codex --version` 和 GitHub Releases 为准）
> - **信息来源**：[Codex App 文档](https://developers.openai.com/codex/app/) | [App Features](https://developers.openai.com/codex/app/features) | [Codex GitHub](https://github.com/openai/codex)
> - **前置课程**：CX-01 Codex 安装与认证完整指南
> - **平台支持**：macOS 与 Windows 桌面端；具体系统要求以官方 App 下载页为准

---

## 重要校准：这篇按官方 App 文档重写

旧稿把很多“推测型 UI”和其他产品形态的功能写成了 App 确定事实，例如固定三栏布局、固定快捷键、非官方忽略文件、App 内 Git 事件 Automation、虚构 SDK、固定并发 Agent 数和未证实的 App Server 设置等。当前官方 Codex App 文档的核心结构是：

| 官方主题 | 你需要掌握什么 |
|----------|----------------|
| Features | Local / Worktree 模式、Git 工具、终端、浏览器、Computer Use、Web search、MCP、图片生成、语音听写、浮窗、artifact 预览、记忆、通知 |
| Settings | General、Notifications、Agent configuration、Git、Integrations & MCP、Browser use、Computer Use、Personalization、Memories、Archived threads |
| Review | 审查 Codex 修改、行内评论、stage / unstage / revert |
| Worktrees | Codex 托管 worktree、Handoff、永久 worktree、清理策略 |
| Automations | 后台周期任务、Triage 收件箱、thread automations、skills 联动、安全模型 |
| Browser | 页面预览、评论页面、浏览器任务边界 |
| Commands | App 内常用命令、模型/审批/状态/MCP 检查 |
| Windows | Windows 版 App 的安装、权限、终端和路径注意事项 |
| Troubleshooting | 启动、登录、终端、浏览器、自动化、worktree 常见问题 |

本篇只写官方能支撑的能力；不同 App 版本的按钮文案可能略有变化，遇到差异时以你 App 里的实际界面为准。

---

## 📚 本课学习目标

完成本课后，你应该能：

1. 区分 Codex App 的 Local、Worktree 和 Cloud/后台任务场景
2. 用 Review 面板审查、评论、暂存或回退 Codex 的修改
3. 正确使用 Worktree、Handoff 和永久 worktree，避免 Git 分支冲突
4. 在 Settings 中配置模型、沙盒、通知、Git、MCP、插件、浏览器、Computer Use、个性化和记忆
5. 创建和管理后台 Automations，并理解 unattended run 的安全风险
6. 使用内置浏览器、浏览器插件、Computer Use、Web search 和图片生成等 App 能力
7. 掌握 App 内常用命令、Windows 注意事项和常见故障排查
8. 避免把 CLI、App、Web、插件和 SDK 的能力混在一起

---

## 🗺️ 学习路径导航

### 路径A：30分钟上手

1. 第1部分：App 是什么
2. 第2部分：创建 Local 线程
3. 第4部分：Review 面板
4. 第6部分：Settings 必配项

### 路径B：App 重度用户

1. 第1-4部分：基本任务、模式、Review
2. 第5部分：Worktrees 与 Handoff
3. 第6-8部分：设置、插件、浏览器/Computer Use
4. 第9部分：Automations
5. 第10部分：安全边界和排错

---

## 第1部分：Codex App 是什么

Codex App 是 Codex 的桌面端工作台。它不是单纯的聊天窗口，也不是传统 IDE 插件，而是把本地项目、线程、Git 变更、终端、浏览器、插件、自动化和设置集中到一个桌面界面里。

### 1.1 App 与 CLI / Web / IDE 的边界

| 形态 | 适合做什么 | 关键边界 |
|------|------------|----------|
| Codex App | 本地项目、多线程、Review、Worktree、Automations、浏览器/Computer Use | 依赖桌面 App 和本机环境 |
| Codex CLI | 终端交互、脚本、CI、MCP 管理、插件命令 | 以命令行为主，没有 App Review 面板 |
| Codex Web / Cloud | 云端任务、远程仓库任务、PR 工作流 | 执行环境在云端，不等于本地 App |
| IDE 扩展 | 在编辑器内发起和同步 Codex 工作 | 主要服务当前 IDE 工作流 |

### 1.2 App 的核心对象

| 对象 | 含义 |
|------|------|
| Project | 一个本地项目目录或仓库 |
| Thread | 一段独立的 Codex 会话，可以运行在 Local 或 Worktree |
| Local | 在你的当前本地 checkout 中工作 |
| Worktree | Codex 创建或使用的 Git worktree，适合后台或隔离任务 |
| Review | 审查 Codex 产生的文件变更和代码审查结果 |
| Automation | 后台周期任务，结果进入 Triage / Inbox 或自动归档 |
| Plugin | 安装后为 Codex 增加 skills、apps 或 MCP 能力的扩展包 |

---

## 第2部分：启动第一个 App 任务

### 2.1 打开项目

常见方式：

```bash
# 如果已经安装 CLI，可从终端启动桌面 App
codex app
```

也可以直接从系统应用列表打开 Codex App，然后选择一个项目目录。

建议第一次选择一个 Git 仓库项目。这样你能完整体验 Review、Worktree、Handoff 和 Git 相关能力。

### 2.2 创建 Local 线程

适合快速修改当前工作区：

```text
请检查 src/api/users.ts，找出错误处理不一致的地方。
如果需要修改，先说明方案，再改最小范围，并运行相关测试。
```

Local 模式会直接作用在当前 checkout。它适合小修、小查、你愿意马上审 diff 的任务。

### 2.3 创建 Worktree 线程

适合并行任务、实验性改动、长任务：

```text
在 Worktree 模式下实现登录表单的可访问性改进。
只改 src/features/auth 和对应测试，完成后给我 Review 摘要。
```

Worktree 模式会让 Codex 在独立 worktree 中工作，避免干扰你当前本地 checkout。

### 2.4 任务描述建议

好的 App 任务通常包含：

```text
目标：修复用户资料页加载失败的问题
范围：只改 src/profile 和 tests/profile
约束：不要改公共 API；保持现有样式
验证：运行 npm test -- profile
交付：说明根因、改动文件、测试结果
```

不要只写“帮我修一下”。Codex 能探索，但你给的边界越清楚，Review 成本越低。

---

## 第3部分：模式：Local、Worktree 与后台任务

### 3.1 Local 模式

Local 线程在当前本地目录工作。优点是快，便于和你已有终端、开发服务器、IDE 状态共享；风险是会直接影响当前工作区。

适合：

- 小范围 bug 修复
- 解释代码
- 运行当前项目测试
- 你要立即 Review 的任务

### 3.2 Worktree 模式

Worktree 线程使用 Git worktree。它适合把 Codex 的工作放到后台，尤其是多个任务并行时。

需要注意：

- Worktree 只适用于 Git 仓库
- Codex 默认可创建托管 worktree
- 托管 worktree 常在 detached HEAD 状态工作，避免污染分支
- 可以用 Handoff 在 Local 和 Worktree 之间移动线程

### 3.3 后台 Automations

Automations 是无人值守的后台周期任务。对 Git 仓库，官方文档说明它会使用专用后台 worktree；非 Git 项目则会直接在项目目录运行。

这意味着：

- Git 项目更适合跑 Automation
- 非 Git 项目要更谨慎，因为后台任务可能直接触碰项目目录
- 自动化任务默认受你的沙盒和审批设置约束

---

## 第4部分：Review 面板

Review 面板用于理解 Codex 改了什么、给定点反馈、决定保留还是回退。

### 4.1 Review 能看到什么

通常包括：

- 修改过的文件列表
- 每个文件的 diff
- Codex 的说明或代码审查结果
- staged / unstaged 状态
- 可回退的文件或变更块

### 4.2 行内评论

如果某一行改得不对，不要重新写一大段泛泛提示。直接在对应位置留下具体反馈：

```text
这里不要吞掉异常。请保留原始 error message，并让调用方能区分 404 和 500。
```

好的行内反馈应该包含：

- 这行哪里不对
- 期望行为是什么
- 是否有约束，例如兼容旧 API、不能改测试快照

### 4.3 stage、unstage、revert

Review 阶段可以按文件或变更范围处理：

| 操作 | 适合场景 |
|------|----------|
| Stage | 确认这部分可以提交 |
| Unstage | 暂时不纳入提交 |
| Revert | 丢弃某个文件或某段不需要的修改 |

不要把 Review 当“全部接受”按钮。App 的价值就在于让你低成本保留正确改动、拒绝错误改动。

---

## 第5部分：Worktrees 与 Handoff

### 5.1 为什么用 Worktree

Git worktree 允许同一个仓库有多个工作目录。Codex 用它实现隔离开发：

```text
Local checkout：你正在调试主任务
Worktree A：Codex 修复登录 bug
Worktree B：Codex 补测试
Automation worktree：后台生成每日代码摘要
```

### 5.2 创建 Worktree 线程

基本流程：

1. 在 App 中选择项目
2. 创建新线程时选择 Worktree
3. 选择起始分支，例如 `main`、`master`、当前分支或 feature 分支
4. 提交任务
5. Codex 创建 worktree 并在其中工作

### 5.3 Handoff：在线程和代码之间切换工作地点

Handoff 用来把线程和代码从 Worktree 移到 Local，或从 Local 移回 Worktree。

适合场景：

- Codex 在后台完成一版，你想在本地 IDE 中继续调试
- 你正在 Local 工作，但想释放当前 checkout，让 Codex 在后台继续
- 你想避免同一个分支同时被两个 worktree checkout

注意：`.gitignore` 中的文件不会随 Handoff 移动。不要依赖 Handoff 迁移本地未跟踪的密钥、缓存或生成物。

### 5.4 托管 worktree 与永久 worktree

| 类型 | 说明 | 适合 |
|------|------|------|
| Codex 托管 worktree | 轻量、通常绑定单个线程、可自动清理 | 临时任务、后台任务 |
| 永久 worktree | 作为独立项目长期保留，不自动删除 | 长期分支、大型功能线 |

官方文档说明，Codex 会把托管 worktree 放在 `$CODEX_HOME/worktrees`，并尝试控制数量；默认保留最近若干个托管 worktree，删除前会保存快照，旧线程可尝试恢复。

---

## 第6部分：Settings 设置全指南

Codex App 的设置不是“装完就不用管”。建议第一次使用就检查以下项目。

### 6.1 General

常见内容包括启动行为、默认项目体验、更新和基础偏好。不同版本选项会变化，不要把某个按钮文案写进团队规范，除非你锁定了 App 版本。

### 6.2 Notifications

建议打开：

- 线程完成通知
- Automation 有发现时通知
- 需要审批或 Review 时通知

如果你跑很多后台任务，通知要配合 Triage 过滤，否则容易淹没真正需要处理的结果。

### 6.3 Agent configuration

重点看：

- 默认模型
- 默认沙盒/审批策略
- 是否允许网络、文件写入、命令执行
- 对危险命令的确认策略

安全原则：先用较保守设置，确认项目边界后再逐步放开。后台自动化尤其不要默认给全权限。

### 6.4 Appearance

Appearance 管理主题、字体显示、界面密度等视觉偏好。官方 App 还提供 Codex pets 这类轻量陪伴/状态元素；它们不影响模型能力，也不应被写成团队生产流程的一部分。

建议：

- 个人可按偏好开启/关闭视觉元素
- 团队教程不要依赖某个主题或宠物状态判断任务进度
- 截图教程应注明 App 版本和主题，避免读者因为界面差异误解

### 6.5 Git

建议确认：

- 默认起始分支
- Worktree 清理策略
- 是否与 GitHub / PR 工作流联动
- stage / commit / branch 相关行为

如果团队使用 protected branch，不要让 Codex 直接在主分支上生成不可审查的提交。

### 6.6 Integrations & MCP

这里管理外部工具连接。MCP 配置通常落在 `config.toml`，所以同一配置可能同时影响 CLI、IDE 扩展和 App。

建议做法：

1. 只连接当前任务确实需要的 MCP server
2. 对需要凭据的服务单独检查权限
3. 在团队文档中写明每个 MCP server 的用途和数据边界

### 6.7 Browser use

Browser use 用来安装或启用 bundled Browser plugin，并管理允许/阻止的网站。Codex 访问未允许的网站前会询问；从 blocked list 移除后，Codex 会在下次使用前重新询问。

建议：

- 文档、测试站点、localhost 可以按需允许
- 银行、支付、后台管理、生产用户数据页面默认阻止
- 不要在浏览器任务中粘贴密钥

### 6.8 Computer Use

Computer Use 是让 Codex 操作桌面应用的能力。官方当前主描述偏 macOS app 操作：启用后需要关注 Screen Recording、Accessibility 等系统级权限；要撤销访问，需要到 macOS Privacy & Security 中处理。该功能在 EEA、英国、瑞士首发不可用。Windows 可用性和权限提示以当前 App 设置与官方平台说明为准，不要把 macOS 权限流程直接套到 Windows。

安全建议：

- 只给必要的系统权限
- 先让 Codex 描述操作计划，再执行
- 不让 Codex 操作支付、密码、生产后台
- 全程保留 Review 和日志

不要写成“Computer Use 必须 Full Auto”。真实限制以 App 设置和系统权限为准。

### 6.9 Personalization

官方设置中有默认 personality：

| 选项 | 用途 |
|------|------|
| Friendly | 更自然、解释多一点 |
| Pragmatic | 更直接、工程任务优先 |
| None | 不加 personality 指令 |

还可以编辑 custom instructions；官方文档说明这会更新个人 `AGENTS.md` 指令。

### 6.10 Context-aware suggestions、Memories、Archived threads

| 设置 | 说明 |
|------|------|
| Context-aware suggestions | 回到 Codex 时提示可继续的任务或后续动作 |
| Memories | 在可用地区/版本中保存跨线程有用上下文 |
| Archived threads | 查看和恢复已归档线程 |

涉及隐私或企业合规时，应优先看组织策略和官方设置说明。

---

## 第7部分：Plugins 插件

Codex 插件不是单个“工具开关”，而是扩展包。一个 plugin 可以包含：

- Skills：可复用工作流或领域能力
- Apps：连接 Gmail、Google Drive、Slack、Linear 等外部服务
- MCP servers：给 Codex 暴露外部工具和资源

### 7.1 App 中安装插件

官方入口是 App 里的 Plugins 页面。打开后可以浏览和安装 curated plugins。

安装后可以：

```text
总结今天未读的 Gmail 线程，并按紧急程度排序。
```

也可以用 `@` 显式指定插件或插件内 skill：

```text
@gmail 总结今天来自客户的未读邮件
```

### 7.2 查看插件命令与配置状态

App 是插件的主入口；如果需要排查某个插件是否被 CLI 侧识别，可以再看本机帮助：

```bash
codex plugin --help
```

这只是辅助排查，不是 App 用户安装插件的主流程。App 用户优先在 Plugins 页面管理安装、授权和停用。

### 7.3 权限和数据共享

安装插件只是让能力可用，不代表绕过审批或沙盒：

- Codex 的审批/沙盒设置仍然生效
- 外部 app 仍受对应服务的登录、权限、隐私政策约束
- 插件包含 MCP server 时，可能还需要额外认证
- 通过 app 发送数据时，数据会进入对应第三方服务的数据处理流程

### 7.4 关闭或卸载插件

官方文档给出的两条路径：

- 在插件浏览器中重新打开该插件，选择卸载
- 若想保留但禁用，可在 `~/.codex/config.toml` 中设置：

```toml
[plugins."gmail@openai-curated"]
enabled = false
```

然后重启 Codex。

---

## 第8部分：内置浏览器、Web search 与 Computer Use

### 8.1 In-app browser

内置浏览器适合：

- 预览本地页面
- 让 Codex 看网页上下文
- 在页面上给具体反馈
- 做范围清晰的浏览器任务
- 在 review 或 UI 验证时把页面状态和代码改动放在同一个线程讨论

示例：

```text
打开 http://localhost:3000/settings，检查设置页是否存在移动端溢出。
只评论页面问题，不要修改代码。
```

### 8.2 Browser use

Browser use 是浏览器插件能力，和普通“看网页”不同，它涉及站点 allow / block 管理。第一次访问站点时，Codex 可能会要求你确认。

建议把站点分成三类：

| 类型 | 建议 |
|------|------|
| `localhost`、预览环境、公开文档 | 按任务允许 |
| staging 后台、内部系统 | 明确说明可访问范围 |
| 支付、生产用户数据、密码管理器 | 默认阻止 |

### 8.3 Web search

Web search 适合查公开资料，但不应替代项目源码阅读。对版本、价格、法规、产品功能等易变信息，允许 Codex 搜索会更可靠。

### 8.4 Image generation

官方 Features 文档说明，Codex App 支持在线程中生成或编辑图片，可自然语言请求，也可显式使用 `$imagegen`。内置图片生成使用 `gpt-image-2`，消耗 Codex 使用额度；大批量生成可以改用 API Key 走 API 计费。

### 8.5 Voice、浮窗、artifact 与 IDE 同步

官方 Features 文档还列出了几个容易漏掉的 App 能力：

| 能力 | 用法 | 注意点 |
|------|------|--------|
| Voice dictation | 在 composer 可见时按住 `Ctrl` + `M` 说话，让 Codex 转写提示词 | 发送前仍应检查转写文本 |
| Floating pop-out window | 把当前线程弹出成独立窗口，可放到浏览器、编辑器或设计预览旁边 | 适合前端迭代和跨窗口对照 |
| Non-code artifacts | 侧边栏可预览 PDF、表格、文档、演示文稿等生成物 | 给 Codex 明确文件类型、结构和验收标准 |
| IDE Extension sync | App 与 IDE Extension 在同一项目中可同步上下文和线程 | 开启 Auto context 时，App 会参考 IDE 正在查看的文件 |

---

## 第9部分：Automations 自动化

Automations 是 App 的后台周期任务系统。它不是传统 CI，也不是 GitHub webhook 的等价物。

### 9.1 Automation 做什么

官方定义的关键点：

- 在后台自动执行重复任务
- 有发现时把结果放到 Triage / Inbox
- 没有发现时可自动归档
- 可以和 skills 组合，完成更复杂任务

### 9.2 管理入口

在 App 侧边栏的 Automations pane 中查看：

- 所有 automations
- 每次 run
- Triage 收件箱
- 未读/全部过滤

### 9.3 让 Codex 创建或更新 Automation

官方推荐的用法不是手写一个 `.codex/automations.toml`，而是直接让 Codex 创建或更新：

```text
每天上午 9 点检查这个仓库过去 24 小时的提交。
如果发现可能的 bug 或缺失测试，把结果放到 Triage；没有发现就归档。
```

### 9.4 Thread automations

Thread automation 适合“晚点继续这个线程”：

```text
30 分钟后继续检查这个修复是否还在跑测试；如果测试失败，汇总失败原因。
```

适合：

- 等待 CI 或长测试
- 晚点复查日志
- 周期性提醒同一线程的后续动作

### 9.5 Test automations

测试类自动化可以定期运行小范围测试、汇总失败、生成修复建议。建议初期用只读或保守权限，避免后台任务自动改代码。

### 9.6 与 Skills 结合

示例：

```text
每周一检查我上周提交的代码，使用 $recent-code-bugfix 思路找一个最可能的回归。
如果没有明确回归，归档；如果有，放到 Triage 并说明证据。
```

核心原则：Automation 负责“什么时候跑”，Skill 负责“怎么跑”。

### 9.7 权限与风险

Automations 是无人值守任务，使用默认沙盒设置。官方文档明确提醒：

- read-only 下，需要写文件、访问网络或操作本机 app 的工具调用会失败
- full access 下，后台自动化风险更高
- 可以通过 Settings 调整沙盒，并用 rules 选择性 allowlist 命令

建议：

| 场景 | 权限建议 |
|------|----------|
| 生成摘要、检查提交 | Read-only |
| 创建报告文件 | Workspace-write / 需要 Review |
| 自动修复代码 | 谨慎，必须进入 Review |
| 操作外部 app 或网页 | 默认不要放到无人值守自动化里 |

---

## 第10部分：本地环境、终端与 MCP

### 10.1 Integrated terminal

App 提供集成终端，适合：

- 运行测试
- 启动本地开发服务器
- 查看 git 状态
- 手动执行 Codex 建议的命令

如果某个动作经常执行，可以在 local environment 中定义 action，让 App 顶部出现快捷按钮。

### 10.2 Local environments

Local environment 用来描述项目如何启动、测试、格式化和提供快捷动作。它不是 `.env` 的替代品，也不应该存放明文密钥。

建议把团队常用动作写清楚：

```text
test: npm test
lint: npm run lint
dev: npm run dev
typecheck: npm run typecheck
```

### 10.3 MCP

MCP 让 Codex 连接外部工具。App、CLI、IDE 扩展可能共享 `config.toml` 中的 MCP 配置。

安全原则：

- 不装不需要的 MCP server
- 每个 server 都写明用途、权限、数据范围
- 对数据库、GitHub、Google Drive、Slack 等高敏工具使用最小权限 token

---

## 第11部分：App 内常用命令

Codex App 支持在对话中使用命令来查看或调整当前线程状态。命令列表会随版本变化，以 App 中 `/help` 或命令补全为准。

常见命令类型：

| 命令类型 | 用途 |
|----------|------|
| 帮助/状态 | 查看当前线程、项目、模型、沙盒、审批状态 |
| 模型 | 查看或切换当前可用模型 |
| 审批 | 调整当前线程的审批行为 |
| MCP | 查看 MCP server 连接状态 |
| 上下文 | 压缩上下文、恢复历史、查看可用资源 |
| Memories | 控制当前线程是否使用或生成 memory |

示例：

```text
/help
/model
/approvals
/mcp
/status
/memories
```

注意：

- 不要把命令表写死成永久完整列表。
- 团队规范应写“需要检查模型/审批/MCP 状态”，而不是绑定某个 UI 文案。
- 如果 App 中没有某个命令，以当前 App 为准。

---

## 第12部分：Windows 与故障排查

### 12.1 Windows 版注意事项

Windows App 已是 Codex App 主路径之一。常见注意点：

- 安装入口以 Microsoft Store、winget 或官方页面当前说明为准。
- 终端默认可能是 PowerShell，命令示例不要假设 bash。
- 路径包含空格或中文时，任务提示里最好用完整路径并加引号。
- 如果项目依赖 Linux 工具链，说明是否需要 WSL2、Git Bash 或 Node/Python/Rust 等本机运行时。
- Browser use、Computer Use、系统权限和杀毒软件拦截问题应按 Windows 当前权限提示处理。

### 12.2 登录和启动问题

| 问题 | 排查 |
|------|------|
| App 打不开 | 检查系统版本、安装来源、是否被安全软件拦截 |
| 登录后回不到 App | 尝试默认浏览器、重新登录、检查代理/VPN |
| 线程无法读取项目 | 确认项目目录权限、trusted project、路径是否移动 |
| 终端命令失败 | 检查 shell、PATH、Node/Python 版本和项目依赖 |

### 12.3 Worktree 问题

| 问题 | 排查 |
|------|------|
| 找不到改动 | 确认当前看的是 Local 还是 Worktree 线程 |
| 空间占用大 | 清理旧托管 worktree、依赖目录和构建缓存 |
| 分支冲突 | 不要多个线程同时改同一文件；必要时 handoff 到一个主线程 |
| 旧线程恢复失败 | 查看快照和 git 状态，不要假设所有临时 worktree 永久存在 |

### 12.4 Browser / Computer Use 问题

| 问题 | 排查 |
|------|------|
| 页面打不开 | 检查 dev server 是否启动、端口是否正确、站点是否被 block |
| Codex 看不到页面状态 | 使用 in-app browser 打开同一 URL，必要时描述当前登录状态 |
| Computer Use 无法操作 | 检查系统 Screen Recording / Accessibility 等权限 |
| 自动化里浏览器任务失败 | 后台任务不适合访问敏感页面或需要人工登录的页面 |

### 12.5 Automation 问题

| 问题 | 排查 |
|------|------|
| 没有结果 | 查看 Triage/Inbox、run 历史和归档策略 |
| 自动化失败 | 检查默认沙盒、网络权限、依赖命令是否可运行 |
| 通知太多 | 收窄 prompt、只在有明确发现时通知 |
| 误改代码 | 降低权限，要求只读摘要或进入 Review 后再应用 |

---

## FAQ

### Q1：Codex App 会不会自动改我的主分支？

A：Local 线程会作用在当前 checkout；Worktree 线程会在独立 worktree 中工作。是否写文件、执行命令，仍受沙盒和审批设置约束。重要仓库建议优先用 Worktree + Review。

### Q2：Worktree 会复制整个仓库吗？

A：Git worktree 会有自己的工作目录文件，但共享 Git 元数据。依赖目录、构建缓存等仍可能占空间，所以需要关注清理策略。

### Q3：Automation 能在电脑关机时运行吗？

A：App 后台自动化依赖本机 App 环境。需要 7x24 云端执行时，应考虑 Codex Cloud、GitHub Actions 或其他云端调度，而不是假设桌面 App 会在关机时运行。

### Q4：插件安装后是不是能直接访问我的 Gmail / Drive / Slack？

A：不是。插件让能力可用，但外部 app 仍需要登录和授权；Codex 的审批和沙盒设置也仍然生效。

### Q5：Computer Use 可以放心全自动吗？

A：不建议。Computer Use 涉及系统级权限和桌面操作，应该限制场景、保留确认、避免敏感页面。

### Q6：`/personality` 是 App 命令吗？

A：不要按旧稿写成固定 `/personality` 命令。官方 App 设置里有 Personality 选项（Friendly / Pragmatic / None）和 custom instructions。

### Q7：只学 App，要不要看 CLI / Web / SDK？

A：不需要作为主线。CX-03 到 CX-11 只作为 App 生态补充：当你需要排查配置、接 MCP、做自动化、上团队安全规范时再看。

---

## 术语表

| 术语 | 通俗解释 |
|------|----------|
| Local | Codex 在当前本地 checkout 中工作 |
| Worktree | Codex 用 Git worktree 创建的隔离工作目录 |
| Handoff | 在线程的 Local 和 Worktree 环境之间迁移工作 |
| Review | 审查 Codex 改动、评论、暂存或回退 |
| Triage | Automation 有发现时进入的收件箱 |
| Plugin | 扩展包，可包含 skills、apps、MCP server |
| Browser use | 通过浏览器插件访问网页并受 allow/block 管理 |
| Computer Use | Codex 操作桌面 app 的能力，需要系统权限 |
| Memories | 跨线程保留有用上下文的能力，受可用性和设置限制 |

---

## 下一步

- **CX-03**：Codex CLI 终端 Agent 完整指南
- **CX-05**：Codex 配置系统完整指南
- **CX-08**：Codex Automations 与集成完整指南
