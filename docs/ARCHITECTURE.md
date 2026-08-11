# 架构说明

## 目标

YCZX Code 首个预览版只建立一条可理解、可测试的只读 Agent 主链。设计原则：

1. CLI、应用编排、Agent、模型、工具、上下文和安全策略分层；
2. 跨模块对象使用统一含义，不为不同 Agent 复制协议；
3. 模型输出和工具参数均视为不可信输入；
4. 只为当前真实需求建模块，不预建空目录或通用插件框架。

## 主流程

```text
CLI -> Application -> Context -> ReAct Agent <-> Provider
                                    |
                                 ToolCall
                                    v
                         Registry -> Policy -> Read-only Tool
                                    |
                                ToolResult -> Context

ReAct Agent -> AgentEvent -> CLI
```

一次请求按以下顺序运行：

1. Application 建立工作区、配置和本轮 Context；
2. ReAct Agent 将公共 Message 交给 Provider；
3. Provider 返回最终文本或结构化 ToolCall；
4. ToolCall 经过 Registry 参数校验和 Security Policy；
5. ToolResult 写回 Context，Agent 决定继续或结束；
6. 最终回答、资源达限、不可恢复错误或用户中断结束本轮。

不解析自由文本形式的 `Action:`，不向终端展示模型隐藏思维。

## 模块

模块按任务逐步创建；没有实现和测试时不创建占位文件。

| 模块 | 职责 |
| --- | --- |
| `cli.py` | 命令参数、连续输入、事件展示和退出 |
| `application.py` | 组装配置、Provider、Agent、工具和会话 |
| `models.py` | Message、ToolCall、ToolResult、AgentResult、事件和错误 |
| `config.py` | 环境变量读取、默认限制和配置校验 |
| `provider.py` | Provider Protocol、FakeProvider 和 OpenAI-compatible 适配器 |
| `agent.py` | Agent Protocol 和有界 ReAct 循环 |
| `tools.py` | Tool、Registry 和四个只读仓库工具 |
| `security.py` | 工作区路径、符号链接、敏感文件和资源限制 |
| `context.py` | 对话、项目规则、工具结果、裁剪和去重 |

CLI 不直接访问 SDK 或文件系统；Agent 不负责终端样式；仓库工具不能绕过 Registry 和安全策略。

## 公共契约

首版只定义跨模块必需的数据：

- `Message`：角色和文本内容；
- `ToolCall`：调用 ID、工具名和已解析参数；
- `ToolResult`：成功状态、结构化内容、摘要、错误和截断信息；
- `AgentEvent`：模型、工具、最终回答和失败事件；
- `AgentResult`：最终回答、停止原因和请求统计；
- `Provider`：公共消息和工具描述到公共模型响应的转换；
- `Tool`：名称、说明、参数 schema 和执行入口。

内部对象优先使用标准库 `dataclass`、`Enum` 和 `Protocol`。供应商字段只存在于 Provider 适配器。

## ReAct 边界

正式主线只有一个 ReAct runner，至少处理：

- 模型直接回答；
- 一个或多个结构化工具调用；
- 可恢复工具错误；
- 最大步数和重复调用限制；
- Provider、配置和内部错误；
- 用户中断。

Plan-and-Solve 与 Reflection 只作为个人学习实验，不进入正式源码结构。

## 技术基线

| 领域 | 选择 |
| --- | --- |
| 语言 | Python 3.12 |
| 依赖管理 | uv 和仓库内 `.venv` |
| CLI | Typer |
| 测试 | pytest 和 FakeProvider |
| 静态检查 | Ruff |
| 模型接口 | 单一 OpenAI-compatible Provider |
| 会话 | 进程内存，不持久化 |

安全细节见 [SAFETY.md](SAFETY.md)，开发顺序见 [TEAM_PLAN.md](TEAM_PLAN.md)。
