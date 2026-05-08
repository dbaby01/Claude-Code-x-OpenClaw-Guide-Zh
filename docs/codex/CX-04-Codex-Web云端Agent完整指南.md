# Codex Web / Cloud 云端 Agent 完整指南：云端后台任务与 PR 工作流

> **课程信息**
>
> - **作者**：老金
> - **GitHub**：https://github.com/KimYx0207
> - **公众号**：老金带你玩AI
> - **X（Twitter）**：老金带你玩AI
> - **个人博客**：https://aiking.dev
> - **预计学时**：2-3小时
> - **难度等级**：⭐⭐ 基础进阶
> - **更新日期**：2026年5月
> - **适用版本**：Codex Web / Cloud 当前版本（以 chatgpt.com/codex 实际界面为准）
> - **信息来源**：[GitHub](https://github.com/openai/codex) | [Codex 官方文档](https://openai.com/codex)
> - **前置课程**：CX-01 Codex 安装与认证完整指南
> - **重要说明**：Codex 云端功能、套餐开放范围和速率限制会变化，本文只写稳定工作流，不写固定额度、固定并发、固定容器规格。

---

> **定位说明**：本篇是 Codex App 主线的补充资料。只使用桌面 App 时不需要先学 Web / Cloud；当你需要云端后台任务、GitHub 仓库任务、PR 工作流或 App handoff 到 cloud 时再看。

## 📚 本课学习目标

完成本课学习后，你将能够：

1. 理解 Codex Web / Cloud 的定位：把任务委派给 OpenAI 云端沙盒执行
2. 连接 GitHub 仓库并创建可复用 Environment
3. 区分 Ask、Code、Review、Cloud handoff 等常见工作流
4. 配置环境脚本、环境变量和网络访问策略
5. 审查云端结果并创建或更新 Pull Request
6. 判断什么时候该用 Web/Cloud，什么时候该用本地 CLI/App

---

## 第1部分：Codex Web / Cloud 是什么

Codex Web / Cloud 是 OpenAI 提供的云端软件工程 Agent。你把任务、仓库和环境交给 Codex，它在隔离沙盒里读取代码、运行命令、修改文件，并把结果交给你审查。

它和本地 Codex 的核心区别：

| 维度 | Codex Web / Cloud | Codex CLI / App 本地模式 |
|------|-------------------|--------------------------|
| 执行位置 | OpenAI 云端沙盒 | 你的电脑或本地 worktree |
| 代码来源 | 通常通过 GitHub 仓库/Environment | 本地文件系统 |
| 适合任务 | 后台任务、并行尝试、PR 修复、远程审查 | 近距离结对、快速编辑、本地调试 |
| 网络与密钥 | 通过环境配置和权限控制 | 取决于本机沙箱、审批和环境变量 |
| 结果交付 | Diff、任务日志、PR、同步回本地 | 本地文件改动、终端输出、git diff |

不要把 Web/Cloud 写成“用户一定能手动切换到某个模型”或“固定 30 分钟任务”。官方 Models 文档当前推荐多数 Codex 任务优先使用 `gpt-5.5`，但 Cloud tasks 当前不能手动更改默认模型；任务上限和可用额度也会随产品策略、套餐和组织设置变化。

---

## 第2部分：访问与前提

### 2.1 入口

常见入口包括：

- ChatGPT 左侧导航里的 Codex。
- `https://chatgpt.com/codex`。
- Codex App 中把本地任务 handoff 到 cloud。
- GitHub PR 中的 Codex review 工作流。
- 组织集成或自动化入口。

实际是否可见取决于你的账号计划、工作区管理员设置、地区/产品开放范围和 GitHub 连接状态。

### 2.2 账号和权限

你通常需要：

1. 一个支持 Codex 的 ChatGPT 账号或组织工作区。
2. 如果要操作私有仓库，需要连接 GitHub，并授权相关 repository。
3. 如果是企业/团队空间，需要管理员启用 Codex Cloud、RBAC 或相关 workspace 设置。
4. 如果要创建 PR，需要对目标仓库有对应写权限。

帮助中心当前说明 Codex 可随 Plus、Pro、Business、Edu、Enterprise 等计划使用，并且特定时期可能对 Free/Go 或其他计划开放；因此教程不要写死“Free 一定不可用”。

---

## 第3部分：Environment

### 3.1 Environment 是什么

Environment 是 Codex 云端任务的运行配置。它通常包含：

- 关联的 GitHub 仓库。
- 默认分支或工作分支。
- 依赖安装/初始化脚本。
- 环境变量和 secret。
- 网络访问策略。
- 团队可见性和权限。

可以把 Environment 理解成“云端任务的可复用项目配置”，而不是一次性 prompt。

### 3.2 创建 Environment 的基本流程

```text
1. 打开 Codex Web / Cloud。
2. 连接 GitHub。
3. 选择要启用的仓库。
4. 创建 Environment，填写名称和说明。
5. 配置 setup script、环境变量和网络策略。
6. 用一个小任务验证依赖安装、测试命令和 PR 权限。
```

建议先用最小配置跑通，不要一开始就把复杂 CI 镜像、生产密钥和大范围网络权限全塞进去。

### 3.3 Setup Script

Setup script 用来准备云端沙盒，例如安装依赖、生成代码、缓存包管理器目录等。

Node.js 项目示例：

```bash
npm ci
npm run build --if-present
```

Python 项目示例：

```bash
python -m pip install -r requirements.txt
pytest --version
```

最佳实践：

- 脚本要幂等，多次执行不应破坏仓库状态。
- 只做开发/测试需要的初始化，不要连接生产数据库。
- 不要在 setup script 里打印 secret。
- 如果安装依赖很慢，优先优化依赖和缓存，而不是让每个任务临时猜命令。

---

## 第4部分：网络访问和 Secrets

### 4.1 Internet Access

Codex Cloud 的网络访问通常可以按环境配置。网络访问可能默认关闭，或由用户/组织显式允许。

适合允许的场景：

- 安装依赖。
- 查询公开文档。
- 下载测试 fixture。
- 调用非生产、只读的内部服务。

不建议允许的场景：

- 访问生产数据库。
- 使用高权限云账号。
- 对外部未知域名开放过宽 HTTP 方法。
- 把私有 token 暴露给不可信依赖脚本。

### 4.2 Secrets

如果云端任务需要 token，应通过产品提供的环境变量或 secret 管理能力配置，不要把密钥写进仓库或任务 prompt。

常见分层：

| 类型 | 示例 | 建议 |
|------|------|------|
| 构建变量 | `NODE_ENV=test` | 可写在环境配置里 |
| 只读 token | 文档服务、内部 staging API | 限权、可轮换 |
| 写权限 token | GitHub、部署平台 | 尽量用最小权限 |
| 生产密钥 | 生产 DB、支付、云 root key | 不要给通用 coding agent |

---

## 第5部分：创建任务

### 5.1 好任务的写法

```text
在 repo owner/project 的 main 分支上创建一个任务：

目标：修复登录页移动端按钮换行问题。
范围：只改 src/pages/login.tsx 和 src/styles/login.css。
验证：运行 npm test -- login，并提供截图或说明。
交付：不要直接合并，创建 PR，PR 描述里列出改动和测试结果。
```

优秀的云端任务一般具备：

- 仓库和分支明确。
- 范围明确。
- 验证命令明确。
- 交付方式明确。
- 不要求 Agent 猜业务背景。

### 5.2 Ask 与 Code

不同入口可能提供不同任务模式。可以按这个逻辑选择：

| 模式 | 适合 |
|------|------|
| Ask / Explore | 理解代码、定位模块、解释设计、给方案 |
| Code | 修改代码、运行测试、产出 diff |
| Review | 审查 PR、找风险、提出建议或补丁 |

如果你只是想先了解代码，别一上来要求改文件。先 Ask，等范围清楚后再 Code。

### 5.3 Best of N / 多尝试

部分云端入口支持让 Codex 对同一任务生成多个候选结果。适合：

- UI 方案比较。
- 重构方案比较。
- 复杂 bug 的多路径排查。

使用时要明确比较标准，例如“优先最小改动、通过现有测试、不要引入新依赖”。

---

## 第6部分：审查结果和 PR 工作流

任务完成后，你应该重点看：

1. Diff 是否只改了约定范围。
2. 是否跑了你要求的测试。
3. 日志里有没有失败、跳过或假阳性。
4. PR 描述是否准确。
5. 是否引入新依赖、迁移或配置变更。
6. 是否需要 follow-up 继续修复。

常见交付动作：

- 查看 diff。
- 继续追问或要求 follow-up。
- 创建新 PR。
- 更新已有 PR。
- 把云端改动同步回本地继续开发。
- 放弃结果并重新开任务。

不要把 Codex 创建 PR 等同于“代码已经可合并”。云端 Agent 的输出仍需要代码审查和 CI 验证。

---

## 第7部分：GitHub 自动 Review

Codex 可以用于 GitHub PR 审查。常见方式包括：

- 在 PR 中提及 `@codex review`。
- 在仓库或组织设置里启用自动 review。
- 让 Codex 对某个 PR 给出风险清单或建议补丁。

适合让 Codex review 的内容：

- 是否有行为回归。
- 是否缺测试。
- 是否有安全风险。
- 是否破坏 API 兼容。
- 是否有明显死代码或错误边界。

不适合完全交给 Codex 的内容：

- 产品取舍。
- 法务/合规审批。
- 高风险生产变更最终批准。

---

## 第8部分：Web/Cloud vs 本地 App/CLI

| 场景 | 推荐 |
|------|------|
| 想让任务在后台跑，自己继续做别的事 | Cloud |
| 想同时尝试多个方案 | Cloud / App 多 Agent |
| 需要本地浏览器、模拟器、设备或私有网络 | 本地 App/CLI |
| 需要频繁手动调试、看 UI、改一点跑一次 | 本地 App/CLI |
| 企业统一给团队接入仓库和审查 | Cloud + GitHub 集成 |
| 不希望代码离开本机环境 | 本地 App/CLI，并检查配置 |

很多真实工作流会混合使用：

```text
本地 App/CLI 调试问题
  -> handoff 到 Cloud 跑更长的实现或多方案尝试
  -> Cloud 创建 PR
  -> 本地拉回分支做最后验证
```

---

## 第9部分：常见问题

### Q1：Codex Web 一定只能 Plus 以上用吗？

不应这样写死。帮助中心当前说明 Codex 随多个 ChatGPT 计划提供，并且产品可能在特定时期扩展到更多计划。最稳妥的说法是：以当前账号页面、工作区设置和官方帮助中心为准。

### Q2：云端任务最长能跑多久？

不要写固定分钟数。任务时长、setup script 限制和速率限制会随计划、组织设置和产品版本变化。教程应建议把任务拆小、让 setup script 快速稳定、让验证命令明确。

### Q3：能不能访问互联网？

取决于 Environment 的 Internet Access 配置和组织策略。即使能访问，也应该限制域名、HTTP 方法和 secret 权限。

### Q4：Codex Web 用哪个模型？

官方 Models 文档当前推荐多数 Codex 任务优先使用 `gpt-5.5`，但同时说明 Cloud tasks 当前不能手动更改默认模型。Web/Cloud 教程应这样写：云端任务使用 Codex 当前默认模型；本地 CLI/App/IDE 可按模型选择器或 `-m`/`/model` 切换。

### Q5：云端容器的 CPU、内存、磁盘是多少？

除非官方当前页面明确列出并且你愿意持续维护，否则不要写固定规格。教学上更有价值的是：减少 setup 成本、避免依赖生产资源、把任务拆成可验证的小步骤。

---

## 关键纠错清单

- 不把 Cloud tasks 写成可自由切换模型；当前官方文档说明 Cloud 默认模型不能手动更改。
- 不写死 Free/Plus/Pro 的可用性、并发数、任务数或价格。
- 不写死 30 分钟任务时长、4GB 内存、10GB 磁盘等资源规格。
- 不把 “Approve” 描述成自动合并；实际动作取决于当前 UI、仓库权限和配置。
- 不把云端 PR 当成最终可发布结果，仍需 review 和 CI。
- Environment、Internet Access、GitHub 权限和组织设置才是 Web/Cloud 教程的核心。

---

**下一课**：CX-05 Codex 配置系统完整指南
