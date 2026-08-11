# 参与开发

## 环境

```bash
uv sync --dev
uv run yczx --help
uv run pytest
```

项目固定使用 Python 3.12 和仓库内 `.venv`。依赖变更使用 `uv add`，并同时提交 `pyproject.toml` 与 `uv.lock`。

## 分支

`main` 是唯一长期分支。开发分支必须匹配：

```text
^(feat|fix|docs|test|refactor|chore|ci|revert)/[a-z0-9]+(-[a-z0-9]+)*$
```

示例：`feat/react-runner`、`fix/path-policy`、`docs/quick-start`。禁止直接推送、强制推送或删除 `main`。

## Commit 与 Pull Request

PR 标题采用 Conventional Commits：

```text
type(scope): summary
```

`scope` 可省略，`type` 必须与分支类型一致，`summary` 不超过 72 个字符。每个 PR 只处理一个主题，并说明：

- 目的；
- 主要改动；
- 验证命令和结果；
- 已知限制。

每个 PR 需要另外两位成员批准，解决全部评审会话，并通过 `git-policy` 和 `quality`。仓库只使用 Squash Merge，使 `main` 中一个 PR 对应一个提交；合并后删除开发分支。

## 公共接口

以下内容变更前必须由三位成员确认：

- Message、ToolCall、ToolResult、AgentResult 和错误类型；
- Provider、Agent、Tool、Registry 和安全策略接口；
- Context、事件、配置项和环境变量名称。

原型不得复制公共类型。只有两个以上正式实现都需要的字段，才提升为公共字段。

## 检查

提交前运行：

```bash
uv lock --check
uv run ruff check .
uv run pytest
git diff --check
```

涉及 CLI 时再运行对应命令。自动测试不得访问真实模型 API；Provider 使用固定 fake 响应。

## 安全

不得提交 `.env*`、API Key、私钥、证书、日志、数据库、缓存、虚拟环境、构建产物、个人路径或大段模型输出。发现敏感信息时立即停止提交并通知仓库管理员，不能只依赖后续删除 Commit。
