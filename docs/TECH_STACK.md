# 技术栈选择

这份文档基于当前主线：开发一个类似 Claude Code 的轻量级 CLI Agent。

## 结论

暑期主技术栈：

```txt
Python 3.12+
uv
Typer
Rich
Pydantic
OpenAI SDK / Anthropic SDK
pytest
Ruff
SQLite
```

后续增强：

```txt
Textual      # 终端 TUI
MCP SDK      # 插件和外部工具协议
TypeScript   # GUI / Electron / Web 包装
PyInstaller  # Windows exe 打包
```

## 技术栈总览

| 层级 | 选择 | 用途 |
|---|---|---|
| 主语言 | Python 3.12+ | Agent Core、CLI、工具系统、文件处理 |
| 项目管理 | uv | 虚拟环境、依赖、运行命令、锁文件 |
| CLI 框架 | Typer | `yczx ask`、`yczx chat`、`yczx init` 等命令 |
| 终端展示 | Rich | Markdown、表格、语法高亮、进度、diff |
| 数据校验 | Pydantic | tool schema、模型响应、配置文件、日志结构 |
| LLM Provider | OpenAI SDK / Anthropic SDK | Responses API、Messages API、tool calling |
| 测试 | pytest | 单元测试、集成测试、Agent eval |
| 格式与 lint | Ruff | 格式化、静态检查、自动修复 |
| 本地存储 | SQLite | 会话、日志、token 用量、配置 |
| TUI 可选 | Textual | 后期做类似 Codex GUI 的终端界面 |
| 协议可选 | MCP | 后期连接外部工具和插件 |

## 为什么主语言选 Python

选择 Python 不是因为它“简单”，而是因为它和 AI 应用开发的关键任务高度匹配。

OpenAI Agents SDK 官方说明它是 Python-first，并提供 agent loop、function tools、MCP tool calling、sessions、human-in-the-loop、tracing 等能力。Anthropic 的工具调用文档也提供 Python 示例，并且工具调用本质上需要你在应用侧执行函数和返回结果。

Python 还能很好覆盖：

- HTTP API 调用
- JSON / JSON Schema
- 文件系统操作
- shell 子进程
- CLI 工具
- 数据校验
- 测试和 eval
- 文档与文件生成

这正是 CLI Coding Agent 的核心。

## 为什么不是 Java 或 C++

Java 和 C++ 都值得学，但不适合作为这个暑期项目主语言。

Java 的优势是企业后端、Spring、微服务和长期工程规范。它适合后续做平台化服务，但做 CLI Agent MVP 会增加样板代码和迭代成本。

C++ 的优势是性能、系统、推理引擎、底层工具。它适合长期计算机基本功和高性能模块，但当前项目的难点不是性能，而是 Agent Harness。

如果暑期主线选 Java / C++，你会把大量时间花在语言和框架细节上，反而减少对 AI 应用核心问题的训练。

## 为什么不用 Web 技术作为主线

Web 技术适合做用户入口和管理后台，但你已经有校友会官网，而且当前目标是类似 Claude Code 的 Agent。

CLI Agent 的核心学习点是：

- 代码库上下文
- 文件工具
- shell 工具
- 权限确认
- 流式输出
- diff 展示
- 工具调用循环

这些都天然发生在终端和本地项目目录中。

## 核心依赖说明

### uv

uv 是一个 Python 包和项目管理器，官方描述为用 Rust 编写的极快 Python package and project manager。它可以管理依赖、虚拟环境、Python 版本、工具运行和锁文件。第一版项目用 uv 可以减少环境配置混乱。

建议命令：

```powershell
uv init
uv add typer rich pydantic openai anthropic pytest ruff
uv run yczx --help
uv run pytest
uv run ruff check .
```

### Typer

Typer 是基于 Python type hints 的 CLI 框架。它适合做：

- `yczx ask`
- `yczx chat`
- `yczx init`
- `yczx config`
- `yczx cost`

Typer 的优点是学习成本低、和类型标注结合好、适合命令行子命令。

### Rich

Rich 用于让终端输出更可读：

- Markdown 渲染
- 表格
- 语法高亮
- 进度条
- diff
- 日志面板

一个 Coding Agent 的终端体验很重要，不能只 `print` 一堆文本。

### Pydantic

Pydantic 用于把不稳定的外部输入变成可验证的数据结构：

- 配置文件
- tool schema
- tool result
- model request / response
- session event
- eval case

OpenAI Structured Outputs 也强调用 JSON Schema 约束模型输出。Pydantic 正好可以把 Python 类型和 JSON Schema 连接起来。

### OpenAI / Anthropic SDK

第一版至少支持一个 Provider，建议优先 OpenAI Responses API 或 Anthropic Messages API 二选一。接口层必须抽象成统一 `ModelProvider`，后续再扩展多模型。

需要掌握：

- 普通文本生成
- streaming
- function calling / tool calling
- structured outputs
- usage 统计
- error / retry / timeout

### pytest

Agent 项目必须测试，否则每次改 prompt 或工具都可能退化。

测试分三类：

- 单元测试：工具函数、路径安全、配置解析。
- 集成测试：模型 Provider 可以被 mock。
- Eval 测试：给 Agent 一组固定任务，看是否达到预期。

### Ruff

Ruff 同时做 lint 和 format，速度快，适合团队统一代码风格。

建议所有提交前运行：

```powershell
uv run ruff format .
uv run ruff check .
uv run pytest
```

### SQLite

第一版用 SQLite 即可，不需要上 PostgreSQL。

保存：

- 会话
- 消息
- 工具调用
- 文件修改记录
- token 用量
- 成本估算

## 后续技术

### Textual

当 CLI 能力稳定后，可以用 Textual 做 TUI：

- 左侧会话列表
- 中间聊天
- 右侧文件预览
- 底部工具调用日志

Textual 可以运行在终端，也可以未来转成浏览器形态。它适合第二阶段。

### MCP

MCP 是连接 AI 应用与外部系统的开放标准。后期可以用它连接：

- GitHub
- 本地数据库
- 浏览器工具
- 文档系统
- 设计工具

第一版先学习概念，不急着实现完整 MCP。

### TypeScript / Electron

如果后期要做 GUI 或 `.exe`，可以用 TypeScript + Electron / Tauri，先作为当前仓库内的独立模块开发。但这不是暑期主线。

## 推荐学习顺序

1. Python 基础强化：类型、异常、文件、subprocess、async。
2. uv：项目、依赖、运行、锁文件。
3. Typer：命令行参数、子命令、配置。
4. Rich：终端输出、Markdown、diff。
5. Pydantic：模型、校验、JSON Schema。
6. OpenAI / Anthropic API：文本、streaming、工具调用。
7. Agent loop：工具注册、工具执行、权限确认。
8. pytest / eval：保证 Agent 行为可验证。
9. SQLite：会话和日志持久化。
10. Textual / MCP：作为后期增强。

## 资料依据

- OpenAI Agents SDK: https://openai.github.io/openai-agents-python/
- OpenAI Function Calling: https://developers.openai.com/api/docs/guides/function-calling
- OpenAI Structured Outputs: https://developers.openai.com/api/docs/guides/structured-outputs
- OpenAI Streaming: https://developers.openai.com/api/docs/guides/streaming-responses
- Anthropic Tool Use: https://docs.anthropic.com/en/docs/agents-and-tools/tool-use/overview
- Claude Code Docs: https://code.claude.com/docs
- Model Context Protocol: https://modelcontextprotocol.io/docs/getting-started/intro
- Typer: https://typer.tiangolo.com/
- Rich: https://rich.readthedocs.io/en/stable/introduction.html
- Textual: https://textual.textualize.io/
- Pydantic: https://docs.pydantic.dev/latest/
- uv: https://docs.astral.sh/uv/
- Ruff: https://docs.astral.sh/ruff/
- pytest: https://docs.pytest.org/en/stable/
