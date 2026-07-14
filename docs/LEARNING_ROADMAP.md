# YCZX Code 暑期学习路线

这份路线的目标是：跟着学完后，你能系统理解 AI 应用开发，并能开发一个轻量级 Claude Code-like CLI Agent。

周期按 8 周设计。如果暑假时间更短，可以压缩为 6 周，但不要跳过“工具调用、上下文、权限、测试评测”这四个核心主题。

## 学习总目标

学完后，你应该能回答并实践：

- 什么是 Agent，什么是 Agent Harness。
- 如何调用 OpenAI / Anthropic 模型。
- 如何做 streaming、structured outputs、tool calling。
- 如何设计 `ModelProvider` 抽象。
- 如何把 Python 函数变成 Agent 工具。
- 如何让 Agent 读取代码库上下文。
- 如何安全地编辑文件和运行命令。
- 如何记录会话、工具调用、token 用量。
- 如何测试和评测一个 Agent。
- 如何把项目包装成一个 CLI 工具。

## 每日节奏

建议每天 2 到 4 小时：

1. 阅读 30 分钟：看指定资料或官方文档。
2. 编码 90 到 150 分钟：写一个小模块或小实验。
3. 记录 15 分钟：写学习日志和问题。
4. 复盘 15 分钟：把当天学到的概念变成自己的话。

学习日志模板：

```md
## 日期

### 今天学了什么

### 今天写了什么

### 遇到的问题

### 明天要验证什么
```

## 两条学习主线

暑期学习以两份系统教程为主线：

- `hello-agents`：系统理论主线，负责建立 AI Agent 知识地图。
- `learn-claude-code`：工程实现主线，负责学习 Claude Code-like Agent Harness。

详细阅读方法见 [READING_GUIDE.md](./READING_GUIDE.md)。

## 两份参考资料怎么学

### learn-claude-code

链接：https://github.com/shareAI-lab/learn-claude-code

学习重点：

- agent loop
- tool use
- permission
- hooks
- todo / task system
- subagent
- skill loading
- context compact
- memory
- error recovery
- MCP plugin

使用方式：把它当成“Agent Harness 拆解课”，每周只学习和当前开发阶段相关的章节。

### hello-agents

链接：https://github.com/datawhalechina/hello-agents

学习重点：

- Agent 基础概念
- ReAct 等经典范式
- 工具调用
- RAG 和上下文工程
- 多智能体通信协议
- Agent 评测
- 综合案例

使用方式：把它当成“理论主线”，帮你建立 AI Native Agent 的系统认知。

### Kode-CLI 后期参考

链接：https://github.com/shareAI-lab/Kode-CLI

Kode-CLI 不作为暑期主教材，只作为后期产品形态参考。暑期先把 `hello-agents` 和 `learn-claude-code` 吃透，再看 Kode-CLI 的 terminal assistant、权限、上下文、多模型、MCP 等工程组织方式。

## 第 0 周：环境与目标收敛

目标：明确项目不是 Web 工具箱，而是轻量级 CLI Coding Agent。

学习内容：

- 阅读 `README.md`
- 阅读 `FIRST_PRINCIPLES.md`
- 阅读 `TECH_STACK.md`
- 浏览三份参考资料的 README
- 了解 Claude Code 官方定位

实践任务：

- 安装 Git、Python 3.12+、uv、VS Code 或 Cursor。
- 在 `C:\Dev\yanchuaner\yczx_code` 初始化 Python 项目。
- 学会 `uv init`、`uv add`、`uv run`。
- 写第一份 `AGENTS.md` 项目说明草案。

验收标准：

- 能说清楚为什么暑期主线是 CLI Agent。
- 能说清楚本仓库内文档、Agent Core 和未来界面模块的职责边界。
- 能在本地运行一个空的 `yczx --help`。

## 第 1 周：Python for AI Application

目标：补齐做 AI 应用开发最需要的 Python 能力。

学习内容：

- Python 类型标注
- `pathlib`
- `subprocess`
- `json`
- 异常处理
- dataclass / Pydantic 基础
- pytest 基础

实践任务：

- 写一个 CLI 小工具：读取一个目录，输出文件树。
- 写一个 `safe_path` 函数，确保路径不能越过工作目录。
- 写 pytest 测试覆盖 `safe_path`。
- 用 Ruff 格式化和检查代码。

验收标准：

- 能解释为什么文件路径安全对 Coding Agent 很重要。
- `uv run pytest` 通过。
- `uv run ruff check .` 通过。

## 第 2 周：模型 API、Prompt 与流式输出

目标：从“会聊天”进入“会构建可控模型调用”。

学习内容：

- OpenAI Responses API 或 Anthropic Messages API
- system / user / assistant 消息
- temperature、max tokens、timeout、retry
- streaming
- usage 统计
- 环境变量管理 API Key

实践任务：

- 实现 `ModelProvider` 抽象。
- 至少接入一个真实模型 Provider。
- 实现流式输出到终端。
- 保存一次模型请求和响应日志。

验收标准：

- `yczx ask "解释什么是递归"` 可以流式输出。
- API Key 不写入代码仓库。
- 调用日志包含 provider、model、输入摘要、输出摘要、状态、耗时。

## 第 3 周：结构化输出与 Tool Calling

目标：让模型能稳定请求工具，而不是只返回自然语言。

学习内容：

- OpenAI function calling / structured outputs
- Anthropic tool use
- JSON Schema
- Pydantic 模型转 schema
- tool result 回传模型

实践任务：

- 定义 `Tool` 接口。
- 实现 `ToolRegistry`。
- 实现 3 个只读工具：
  - `list_files`
  - `read_file`
  - `grep`
- 让模型可以选择调用工具。

验收标准：

- 用户问“这个项目有哪些文件”时，Agent 会调用 `list_files`。
- 用户问“README 里写了什么”时，Agent 会调用 `read_file`。
- 工具输入输出都经过 Pydantic 校验。

## 第 4 周：代码库上下文工程

目标：让 Agent 理解“当前项目”，而不是只看用户一句话。

学习内容：

- 上下文窗口
- token 预算
- 项目目录摘要
- Git 状态
- `AGENTS.md`
- ignore 规则
- 上下文压缩

实践任务：

- 实现 `ContextBuilder`。
- 自动读取：
  - 当前工作目录
  - Git 分支和变更摘要
  - `AGENTS.md`
  - 目录树
  - 用户显式提到的文件
- 设置最大上下文长度。

验收标准：

- 在一个小项目中运行 `yczx ask "帮我解释项目结构"`，输出能引用真实文件。
- 大文件不会被完整塞进 prompt。
- `.git`、`node_modules`、虚拟环境、构建产物默认忽略。

## 第 5 周：文件编辑、Diff 与权限系统

目标：让 Agent 从“建议怎么改”变成“能安全地改”。

学习内容：

- unified diff
- apply patch
- 文件写入安全
- 人类确认
- 危险操作拦截
- Kode-CLI permission / safe mode 思路

实践任务：

- 实现 `write_file`，默认需要确认。
- 实现 `apply_patch` 或最小 diff apply。
- 修改前展示 diff。
- 增加权限模式：
  - `ask`：写文件和命令都询问
  - `read-only`：只读
  - `auto`：低风险操作自动，高风险询问

验收标准：

- Agent 修改文件前必须展示计划或 diff。
- 用户拒绝后不能写入。
- 不能写出工作目录。
- 不能覆盖二进制文件。

## 第 6 周：Shell 工具与任务闭环

目标：让 Agent 能运行测试和命令，完成类似 Claude Code 的开发闭环。

学习内容：

- `subprocess`
- shell 超时
- stdout / stderr 捕获
- 命令 allowlist / denylist
- Windows PowerShell 注意事项
- 错误恢复

实践任务：

- 实现 `run_shell` 工具。
- 默认执行前确认。
- 限制工作目录。
- 增加超时和输出截断。
- 支持让 Agent 运行 `pytest` 或项目测试命令。

验收标准：

- Agent 能修改一个小函数并运行测试验证。
- 危险命令默认拒绝或强制确认。
- 命令失败时，Agent 能读取 stderr 并给出下一步。

## 第 7 周：会话、成本、评测

目标：从能跑的 Demo 变成可复盘、可评估的项目。

学习内容：

- SQLite
- session / message / tool_call 数据模型
- token usage
- cost tracking
- eval case
- regression test

实践任务：

- 保存会话历史。
- 保存工具调用历史。
- 实现 `/cost` 或 `yczx cost`。
- 写 5 个 eval case：
  - 生成 README
  - 解释报错
  - 搜索文件
  - 修改小函数
  - 生成文档

验收标准：

- 每次 Agent 运行都可追溯。
- 能查看历史会话和成本估算。
- eval case 至少能人工稳定通过 4 个。

## 第 8 周：打磨、演示与作品集

目标：形成可展示、可继续开发、可用于实习的项目。

学习内容：

- CLI 打包
- README 写作
- 架构图
- Demo 脚本
- 技术复盘
- 面试叙述

实践任务：

- 完成 `README.md`。
- 完成 `docs/architecture.md`。
- 完成 `docs/demo-script.md`。
- 录制 3 分钟 Demo。
- 写一篇项目复盘。

验收标准：

- 新电脑能按文档运行。
- 面试时能讲清楚 Agent loop、tool calling、权限系统、上下文工程。
- 有一个能稳定演示的命令：

```powershell
yczx ask "阅读这个项目，帮我生成 README，并在确认后写入文件"
```

## 每周固定产出

每周至少沉淀：

- 一份学习笔记。
- 一个可运行小功能。
- 一个测试或 eval。
- 一次 Demo 截图或录屏。
- 一段复盘：本周学到什么、卡在哪里、下周怎么改。

## 补充学习：Java / C++

Java 和 C++ 不是本项目暑期主线，但你作为计算机专业学生仍然要学。

建议安排：

- Java：跟学校课程或自学 Spring Boot 基础，目标是理解企业后端。
- C++：跟数据结构、算法、操作系统课程，目标是打牢系统能力。

不要让 Java / C++ 抢走 CLI Agent 主线时间。当前项目最能体现 AI 应用开发的是 Python Agent Harness。
