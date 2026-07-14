# YCZX Code

YCZX Code 是轻量级 CLI Coding Agent 的统一主仓库，代码、学习指南、开发路线和项目复盘都在这里共同维护。

当前目标已经收敛为：跟着本仓库的文件走，系统学习 AI 应用开发，并在暑期开发出一个类似 Claude Code 的轻量级 CLI Agent。它不追求复刻完整 Claude Code，而是优先做出一个能读代码库、调用工具、生成或修改文件、运行命令、记录过程、可演示、可复盘的最小可用 Coding Agent。

## 最终目标

暑期结束时，希望你能拿出：

- 一个可运行的 `yczx` 命令行工具。
- 一个轻量 Agent Core：模型适配、工具调用、上下文管理、权限确认、日志记录。
- 几个真实 Demo：生成 README、分析报错、修改小项目代码、运行测试、生成文档。
- 一套项目文档：学习路线、开发路线、技术栈依据、架构图、演示脚本、复盘文章。
- 一段能用于实习面试的项目叙述：你不是只调 API，而是做了一个 AI Coding Agent 的 Harness。

## 先读哪些文件

建议按这个顺序阅读：

1. [FIRST_PRINCIPLES.md](./docs/FIRST_PRINCIPLES.md)：为什么 CLI Agent 是暑期最优解之一。
2. [TECH_STACK.md](./docs/TECH_STACK.md)：为什么主技术栈选择 Python + Typer + Rich + Pydantic + uv。
3. [READING_GUIDE.md](./docs/READING_GUIDE.md)：如何按 `hello-agents` 和 `learn-claude-code` 两条主线学习。
4. [LEARNING_ROADMAP.md](./docs/LEARNING_ROADMAP.md)：按周学习 AI 应用开发和 Agent Engineering。
5. [DEVELOPMENT_ROADMAP.md](./docs/DEVELOPMENT_ROADMAP.md)：按周开发轻量级 Claude Code-like CLI Agent。

## 仓库结构

项目统一维护在一个仓库中：

| 路径 | 作用 | 当前阶段 |
|---|---|---|
| `C:\Dev\yanchuaner\yczx_code` | 代码、文档、测试和评测的统一根目录 | 暑期主战场 |
| `docs/` | 学习指南、开发路线、架构与复盘 | 持续维护 |
| `src/yczx_code/` | CLI、Agent Core、工具和模型适配 | 逐步实现 |
| `tests/`、`evals/` | 自动化测试与 Agent 评测 | 随功能同步建设 |

暑期先在本仓库内部实现 `agent_core` 包，GUI、TUI 和桌面端也优先作为仓库内的独立模块演进。等接口稳定、具备独立发布与维护价值后，再评估是否拆分仓库，不提前维护空项目。

## 项目定位

YCZX Code 不是校友会官网，也不是传统 Web 工具箱。它的核心定位是：

> 面向学生开发者和 AI 应用学习者的轻量级 CLI Coding Agent。

它应该逐渐具备这些能力：

- 理解当前代码库结构。
- 读取 `AGENTS.md` 或项目说明。
- 根据用户任务规划步骤。
- 调用工具搜索、读取、创建、修改文件。
- 在用户批准后运行命令。
- 展示 diff 和执行日志。
- 生成 README、部署文档、学习资料、演示文档。
- 记录 token 用量、成本估算和会话历史。
- 为后续 GUI / TUI / MCP / 多模型协作留接口。

## 学习主线

本路线以两份系统教程为主线：

- [shareAI-lab/learn-claude-code](https://github.com/shareAI-lab/learn-claude-code)：学习从零构建 Agent Harness，重点是 agent loop、tool use、permission、hooks、memory、context compact。
- [datawhalechina/hello-agents](https://github.com/datawhalechina/hello-agents)：系统学习智能体理论与实践，重点是 Agent 基础、ReAct、工具调用、RAG、上下文工程、评测。

简单说：

- `hello-agents` 回答“Agent 是什么，AI 应用开发有哪些知识板块”。
- `learn-claude-code` 回答“怎样把这些知识做成一个 Claude Code-like Agent Harness”。

[shareAI-lab/Kode-CLI](https://github.com/shareAI-lab/Kode-CLI) 只作为后期产品形态参考，重点看 terminal assistant、工具系统、上下文管理、AGENTS.md、权限审批、MCP、多模型协作，不作为暑期主教材。

也参考官方文档：

- [Claude Code Docs](https://code.claude.com/docs)
- [Anthropic Tool Use](https://docs.anthropic.com/en/docs/agents-and-tools/tool-use/overview)
- [OpenAI Agents SDK](https://openai.github.io/openai-agents-python/)
- [OpenAI Function Calling](https://developers.openai.com/api/docs/guides/function-calling)
- [OpenAI Structured Outputs](https://developers.openai.com/api/docs/guides/structured-outputs)
- [Model Context Protocol](https://modelcontextprotocol.io/docs/getting-started/intro)

## 暑期 MVP

第一版只做 CLI Agent，不做 Web 后台，不做完整 GUI，不做支付订阅。

最低可用版本包含：

- `yczx ask "任务"`：一次性任务执行。
- `yczx chat`：交互式会话。
- `yczx init`：在项目中生成 `AGENTS.md` 模板。
- 模型 Provider：至少支持一个 OpenAI 或 Anthropic 接口。
- 工具系统：`list_files`、`read_file`、`grep`、`write_file`、`apply_patch`、`run_shell`。
- 权限系统：写文件和运行命令前必须确认。
- 上下文系统：读取目录树、Git 状态、`AGENTS.md`、用户任务。
- 会话日志：保存任务、工具调用、结果、token 用量。
- 演示任务：生成 README、分析报错、修改小函数并运行测试。

## 不做什么

暑期第一版不做：

- 校友会官网。
- 传统 Web 管理后台。
- API 聚合中转、代充、转卖。
- 自动支付和订阅系统。
- 完整桌面 `.exe`。
- 完整 MCP 客户端。
- 多 Agent 并行调度。
- 复杂插件市场。

这些可以作为第二阶段方向。第一阶段先把 Agent Harness 做扎实。

## 为什么这对 AI 应用开发有价值

一个像 Claude Code 的 CLI Agent 会逼你学到 AI 应用开发最核心的东西：

- 模型 API 调用。
- Prompt 和系统指令。
- Tool calling / function calling。
- JSON Schema 和结构化输出。
- 上下文工程。
- 文件系统和命令执行。
- 权限、安全、日志。
- 流式输出和终端交互。
- 测试、评测和 Demo 设计。

这比单纯做一个聊天网页更适合作为 AI 应用开发路线的底气。

## 下一步

1. 在 `C:\Dev\yanchuaner\yczx_code` 初始化 Python CLI 项目。
2. 按 [TECH_STACK.md](./docs/TECH_STACK.md) 安装工具链。
3. 按 [LEARNING_ROADMAP.md](./docs/LEARNING_ROADMAP.md) 每周学习。
4. 按 [DEVELOPMENT_ROADMAP.md](./docs/DEVELOPMENT_ROADMAP.md) 每周开发。
5. 每周保留可演示产物和复盘记录。
