# YCZX Code

YCZX Code（命令名 `yczx`）是一个面向代码阅读和项目理解的轻量级终端 Coding Agent。项目以清晰、可测试、可替换的单 Agent 架构为目标，体验参考 Claude Code，但不复刻其完整能力。

> 当前状态：初始工程骨架。CLI 已提供帮助和版本信息，ReAct、Provider 和只读工具按照团队计划继续开发。

## 预览版目标

用户在本地代码库中启动会话后，ReAct Agent 可以调用只读工具查找证据并回答问题：

```bash
yczx [WORKSPACE]
```

省略 `WORKSPACE` 时使用当前目录。

预览版只包含：

- 单 Agent、单 ReAct 循环和连续终端会话；
- 一个 OpenAI-compatible Provider 和测试 FakeProvider；
- `list_dir`、`read_file`、`search_files`、`get_project_rules`；
- 工作区隔离、敏感文件拒绝和资源限制；
- 确定性测试、GitHub Actions 和最小项目文档。

预览版不包含文件写入、任意 Shell、多 Agent、MCP、插件、复杂 TUI、长期记忆或会话持久化。

## 开发环境

项目要求 Python 3.12，使用 uv 管理仓库内的 `.venv`：

```bash
uv sync --dev
uv run yczx --help
uv run pytest
```

增加运行依赖使用 `uv add <package>`，增加开发依赖使用 `uv add --dev <package>`。不得使用全局 `pip` 安装项目依赖。

## 架构

```text
CLI -> Application -> ReAct Agent <-> Provider
                         |
                  Tool Registry -> Security Policy -> Read-only Tools
                         |
                      Context
```

CLI 只负责输入输出；Agent、Provider、工具、上下文和安全策略通过公共对象连接。详细设计见 [架构说明](docs/ARCHITECTURE.md)。

## 文档

- [文档总览](docs/README.md)
- [架构说明](docs/ARCHITECTURE.md)
- [安全设计](docs/SAFETY.md)
- [团队计划](docs/TEAM_PLAN.md)
- [参与开发](CONTRIBUTING.md)
- [Agent 协作约定](AGENTS.md)

## 提交前检查

```bash
uv lock --check
uv run ruff check .
uv run pytest
git diff --check
```

所有变更通过符合命名规则的短分支和 Pull Request 合入 `main`，具体规则见 [CONTRIBUTING.md](CONTRIBUTING.md)。
