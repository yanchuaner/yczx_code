# YCZX Code 暑期开发计划

| 项目 | 内容 |
| --- | --- |
| 文档状态 | 正式执行版 |
| 计划批准 | 待团队确认 |
| 目标 | 三周内完成可运行、可测试、可演示的轻量级终端 Coding Agent 骨架 |
| 周期 | 连续三周，具体起止日期待团队确认 |
| 团队 | 成员 A（ReAct）、成员 B（Plan-and-Solve）、成员 C（Reflection） |
| 正式主线 | 单 Agent、单 ReAct、单代码库、只读工具 |
| 初始基线 | CLI 工程壳、最小文档和 CI；Agent、Provider、工具与安全策略待开发 |

## 1. 项目目标

预览版完成后，用户安装项目并执行：

```bash
yczx [WORKSPACE]
```

省略 `WORKSPACE` 时使用当前目录。YCZX Code 进入连续对话，通过 ReAct 循环调用只读工具，完成代码搜索、文件阅读、项目规则读取和基于证据的回答。

必须具备：

- `yczx` 终端入口和连续对话；
- 一个 OpenAI-compatible Provider 和一个测试 FakeProvider；
- 单 ReAct 执行器；
- `list_dir`、`read_file`、`search_files`、`get_project_rules`；
- 工作区隔离、敏感文件拒绝、步数和输出限制；
- GitHub Actions 自动检查和受保护的 `main` 分支；
- 最小但完整的项目文档和测试。

本期不做文件写入、任意 Shell、多 Agent、MCP、插件、复杂 TUI、会话持久化、长期记忆，以及 Plan-and-Solve/Reflection 正式实现。两种学习方向只保留个人实验，不向主线增加文件或框架。

## 2. 当前状态

| 已有内容 | 主要缺口 |
| --- | --- |
| Python 3.12、uv、Typer、pytest、Ruff、`src/` 布局 | Agent 主链尚未实现 |
| `yczx --help`、`yczx --version` | 裸 `yczx` 不能启动对话 |
| 2 项 CLI 测试 | 无 Agent、Provider、工具和安全测试 |
| 根 README、docs 总览、架构、安全、计划和协作规范 | 文档需随实现持续校准 |

初始整理前的未提交内容已按最小结构归并；后续不得覆盖其他成员的新改动，也不得代替其他成员提交。

## 3. 最小仓库结构

目标结构如下。没有实际实现或测试需求时，不提前创建空目录、空接口或占位文件。

```text
yczx_code/
├── .github/
│   └── workflows/
│       └── ci.yml
├── docs/
│   ├── README.md
│   ├── ARCHITECTURE.md
│   ├── SAFETY.md
│   └── TEAM_PLAN.md
├── src/yczx_code/
│   ├── __init__.py
│   ├── __main__.py
│   ├── cli.py
│   ├── application.py
│   ├── models.py
│   ├── config.py
│   ├── provider.py
│   ├── agent.py
│   ├── tools.py
│   ├── security.py
│   └── context.py
├── tests/
│   ├── fixtures/readonly_repo/
│   ├── test_cli.py
│   ├── test_agent.py
│   ├── test_provider.py
│   ├── test_context.py
│   ├── test_tools.py
│   ├── test_security.py
│   └── test_readonly_e2e.py
├── AGENTS.md
├── CONTRIBUTING.md
├── README.md
├── pyproject.toml
├── uv.lock
├── .python-version
└── .gitignore
```

### 文档职责

| 文件 | 唯一职责 |
| --- | --- |
| `README.md` | 项目介绍、范围、安装、配置、启动和演示 |
| `docs/README.md` | 文档总览和阅读顺序 |
| `docs/ARCHITECTURE.md` | 模块边界、数据流、公共接口和技术选择 |
| `docs/SAFETY.md` | 路径、敏感文件、资源限制和必测场景 |
| `CONTRIBUTING.md` | 分支、Commit、PR、评审和检查规则 |
| `AGENTS.md` | Coding Agent 在本仓库中的工作边界 |
| `docs/TEAM_PLAN.md` | 三周任务、分工、里程碑和验收 |

原 `DEVELOPMENT_ROADMAP.md`、`LEARNING_ROADMAP.md` 和 `TECH_STACK.md` 的有效内容已归入本计划和架构文档，冗余文件不再保留。

## 4. Git 与 GitHub 规范

### 自动检查

只保留一个 `.github/workflows/ci.yml`，在 Pull Request 和 `main` 更新时运行两个 Job：

| Job | 检查内容 |
| --- | --- |
| `git-policy` | 严格校验分支名和 PR 标题；PR 差异通过 `git diff --check`；禁止跟踪 `.env*`、密钥/证书、日志、数据库、缓存、虚拟环境和构建产物 |
| `quality` | `uv lock --check`、`uv sync --locked --dev`、`uv run ruff check .`、`uv run pytest`、`uv run yczx --help` |

工作流使用 `pull_request` 和仅限 `main` 的 `push` 事件、只读仓库权限及完整 Git 历史；不使用 `pull_request_target` 执行 PR 代码。PR 差异检查必须比较 base SHA 与 head SHA，不能在干净检出后只运行无范围的 `git diff --check`。

### 命名与历史规则

| 对象 | 强制规则 |
| --- | --- |
| 长期分支 | 只保留 `main` |
| 开发分支 | `^(feat|fix|docs|test|refactor|chore|ci|revert)/[a-z0-9]+(-[a-z0-9]+)*$`，主题段使用小写 kebab-case |
| PR 标题 | Conventional Commits：`type(scope): summary`；scope 可省略，type 与分支类型一致，summary 不超过 72 个字符 |
| `main` 提交 | 初始提交后只接受 GitHub Squash Merge；一个 PR 对应一个提交 |
| 合并方式 | 只启用 Squash Merge，禁用 merge commit 和 rebase merge；合并后删除开发分支 |
| 发布标签 | 使用语义化版本，例如预览版 `v0.1.0a0` |

### 仓库设置

GitHub 分支保护不是工作流文件的一部分，需要在仓库设置中完成：

- 禁止直接推送、强制推送和删除 `main`；
- 使用仓库规则集拒绝未采用许可前缀的新分支，再由 `git-policy` 校验完整分支正则；
- 所有变更通过 Pull Request；
- 合并前要求 `git-policy`、`quality` 成功且分支已基于最新 `main`；
- 每个 PR 均要求另外两位成员批准，新提交使旧批准失效，所有评审会话必须解决；
- 要求线性历史，不允许管理员绕过保护规则；
- 一个 PR 只处理一个计划任务，并填写目的、改动、验证和限制。

配置依据：[GitHub Protected Branches](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-protected-branches/about-protected-branches)、[GitHub Squash Merge](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/configuring-pull-request-merges/configuring-commit-squashing-for-pull-requests)。

### 初始版本基线

截至 2026-08-12，W1-01 已完成，基线记录如下：

| 项目 | 结果 |
| --- | --- |
| 历史 | 本地与远端 `main` 均以 `chore: initialize YCZX Code` 作为唯一根提交 |
| 备份 | 旧 Git 历史已保存为仓库外 `git bundle`，恢复位置由仓库管理员记录 |
| 分支 | 旧开发分支已删除，远端仅保留 `main` |
| CI | `git-policy` 和 `quality` 首次运行通过 |
| 保护 | 已启用两人审批、必需检查、线性历史、禁止绕过/强推/删除和 Squash Merge |

W1-01 是唯一允许改写 `main` 的任务。后续禁止通过 rebase、reset 或 force-push 改写共享历史；功能撤销使用新的 `revert` PR。

## 5. Agent 骨架

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

| 模块 | 职责 |
| --- | --- |
| `models.py` | Message、ToolCall、ToolResult、AgentResult、事件和错误类型 |
| `provider.py` | Provider Protocol、FakeProvider、OpenAI-compatible 适配器 |
| `agent.py` | 有最大步数和停止原因的 ReAct 循环 |
| `tools.py` | Tool、Registry 和四个只读工具 |
| `security.py` | 工作区路径、符号链接、敏感文件和资源限制 |
| `context.py` | 会话消息、项目规则、裁剪和去重 |
| `application.py` | 组装配置、Provider、Agent、工具和会话 |
| `cli.py` | 输入、状态展示、最终回答和退出；不直接访问 SDK 或文件系统 |

公共对象优先使用标准库 `dataclass`、`Enum` 和 `Protocol`。正式流程使用结构化 tool calling，不解析自由文本 Action，不展示模型隐藏思维。

### 公共接口开发顺序

| 顺序 | 先冻结的内容 | 解锁的后续工作 |
| ---: | --- | --- |
| 1 | Message、ToolCall、ToolResult、AgentResult、错误和停止原因 | Provider、Agent 和工具协议 |
| 2 | Provider、Agent、Tool、Registry、Security、Context 协议和配置名称 | FakeProvider、Registry 和安全策略 |
| 3 | FakeProvider、Registry、参数校验和安全策略 | 只读工具、ReAct、Context 和真实 Provider 并行开发 |
| 4 | Agent/Session/Event 契约和 FakeProvider | Application 与 CLI 并行开发，无需等待真实 Provider |
| 5 | 工具、ReAct、Context、Application 和 CLI 主链 | 离线 E2E；真实 Provider 单独作为手测前置 |

各层先以小型契约 PR 合入，再开发消费者；后续层不得在本地模块复制或改写公共类型。

## 6. 团队分工

| 成员 | 正式主线职责 | 学习方向处理 |
| --- | --- | --- |
| A | `agent.py`、`tools.py`、ReAct 流程和工具测试 | ReAct 实验直接转为主线测试 |
| B | `models.py`、`provider.py`、`config.py`、`application.py` 和文档汇总 | Plan-and-Solve 仅个人分支实验 |
| C | `security.py`、`context.py`、CLI、事件与错误展示、GitHub Actions、安全/E2E/固定评测 | Reflection 仅个人分支实验 |

公共接口由三人确认，每个文件在同一迭代只设一位负责人。

G2 通过且无 P0 缺陷后，B、C 可各安排最多 0.5 天，分别在 `chore/plan-solve-prototype`、`chore/reflection-prototype` 短分支维护学习原型。原型分支不合入 `main`，不作为里程碑依赖，实验结束后删除。

## 7. 三周计划

### 第 1 周：仓库与接口骨架

| ID | 负责人（协作） | 工期 | 任务与前置 | 交付与验收 |
| --- | --- | ---: | --- | --- |
| W1-01（已完成） | 仓库管理员（待团队确认；全员确认） | 0.5 天 | 建立最小初始版本，无前置 | 交付根提交 SHA、仓库外备份记录和保护配置；验收 `main` 仅一个根提交且 CI 通过 |
| W1-02 | B（A、C） | 1 天 | 定义公共对象和 Provider 接口，依赖 W1-01 | 模型、错误、Provider/FakeProvider 契约有单测 |
| W1-03 | A（B、C） | 1 天 | 定义 Agent、Tool 和 Registry 接口，依赖 W1-02 | 接口契约测试通过，无重复协议 |
| W1-04 | C（A） | 1 天 | 定义安全与上下文边界，依赖 W1-02 | 路径拒绝、资源上限和 Context 输入输出有测试样例 |
| W1-05 | B（C） | 1 天 | 实现配置加载和 FakeProvider，依赖 W1-02 | 错误信息明确指出缺失配置；测试完全离线 |
| W1-06 | A（C） | 1 天 | 实现 Registry 与参数分发，依赖 W1-03、W1-04 | 未知工具、参数错误和权限拒绝统一返回 ToolResult |
| G1 | A（B、C） | 每人 0.5 天 | 周验收，依赖 W1-02 至 W1-06 | 公共契约、FakeProvider、Registry 和安全边界可演示；全部检查通过 |

### 第 2 周：只读 ReAct 主链路

| ID | 负责人（协作） | 工期 | 任务与前置 | 交付与验收 |
| --- | --- | ---: | --- | --- |
| W2-01 | B（A） | 1.5 天 | 实现真实 Provider 和结构化工具调用转换，依赖 W1-02、W1-05 | 固定响应测试不联网；真实适配器不泄漏供应商字段 |
| W2-02 | C（A） | 1.5 天 | 实现安全策略，依赖 W1-04 | 拒绝越界、外部符号链接、`.env*`、密钥和超限文件 |
| W2-03 | A（C） | 2 天 | 实现四个只读工具，依赖 W1-06、W2-02 | 工具统一经过 Registry 和安全策略，返回结构化结果 |
| W2-04 | A（B、C） | 2 天 | 实现 ReAct 执行器，依赖 W1-03 至 W1-06 | 覆盖直接回答、工具调用、工具错误、重复调用和最大步数 |
| W2-05 | C（A、B） | 1 天 | 实现 Context 和事件展示，依赖 W1-02、W1-04 | 上下文可裁剪、去重；事件不包含隐藏思维或敏感内容 |
| W2-06 | B（A、C） | 1 天 | 实现 Application Session，依赖 W1-03、W1-05、W2-05 | 支持依赖注入；会话重置后状态清空 |
| W2-07 | C（B） | 1 天 | 实现裸 `yczx` REPL，依赖 W2-06 | 默认当前目录；连续对话、退出和异常路径可测试 |
| G2 | B（A、C） | 每人 0.5 天 | 离线端到端验收，依赖 W2-03 至 W2-07 | FakeProvider 完成“提问→读工具→回答”完整闭环，测试不联网 |

### 第 3 周：检查、文档与发布演练

| ID | 负责人（协作） | 工期 | 任务与前置 | 交付与验收 |
| --- | --- | ---: | --- | --- |
| W3-01 | C（A） | 1 天 | 安全和资源回归，依赖 G2 | SAFETY 必测场景全部自动化且通过 |
| W3-02 | C（A、B） | 0.5 天 | 固定评测，依赖 G2 | 交付 5 个固定测试夹具和评分表；核心演示用例可重复执行 |
| W3-03 | B（A、C） | 1 天 | 真实模型手测，依赖 W2-01、W3-02 | 至少一次结构化工具调用成功；记录脱敏结果，不记录密钥 |
| W3-04 | C（B） | 0.5 天 | Git 合规演练，依赖 W1-01 | 分支名、空白错误和禁入文件违规样例均被阻止合并 |
| W3-05 | A 协调（B、C） | 每人最多 1 天 | 阻塞缺陷修复，依赖 W3-01 至 W3-04 | 缺陷记录指定模块负责人，另一成员评审；无 P0，P1 有明确结论 |
| W3-06 | B（A、C） | 1 天 | 同步最终文档，依赖 W3-01 至 W3-05 | README 可独立完成安装、配置、启动和演示，修复后的行为已复核 |
| G3 | A（B、C） | 每人 0.5 天 | 干净环境最终验收，依赖 W3-05、W3-06 | 全部检查通过，安装后 `yczx` 可启动对话 |

## 8. 关键路径与里程碑

```text
共同基线
-> 最小文档 + CI + 公共契约
-> 安全策略 + FakeProvider
-> 四个只读工具 + ReAct
-> Application + yczx REPL
-> 离线 E2E
-> 安全回归 + 真实模型手测
-> 文档与干净环境验收
```

| 里程碑 | 完成标准 |
| --- | --- |
| G1：骨架可开发 | 目录、文档、CI 和公共契约稳定 |
| G2：主链路可运行 | FakeProvider 驱动完整只读 ReAct 会话 |
| G3：预览版可交付 | 真实模型可用、安全测试通过、文档与命令一致 |

## 9. 协作节奏

- 每周开始分配任务和文件负责人；每周结束演示对应里程碑；
- 每日异步同步“已完成、下一步、阻塞”；具体时间待团队确认；
- 每个 PR 均由另外两人评审；公共接口和安全边界必须在编码前先确认契约；
- 接口 PR 先合并，消费者随后同步；不在多个分支复制公共类型；
- 依赖变更由 B 统一执行 `uv add` 并同时更新 `uv.lock`；
- Plan-and-Solve 和 Reflection 实验不进入预览版 PR。

### 高冲突文件

| 文件 | 冲突原因 | 协作办法 |
| --- | --- | --- |
| `models.py`、`provider.py`、`tools.py` | 公共类型和协议被多人依赖 | 分别由 B、B、A 持有；先合契约 PR，消费者随后同步 |
| `application.py`、`cli.py` | 会话、事件和错误展示交界 | B 与 C 先确认输入输出；同一迭代不并行编辑同一文件 |
| `pyproject.toml`、`uv.lock` | 依赖操作会同时改两份文件 | 仅 B 执行 `uv add`，依赖 PR 串行合入 |
| `README.md`、`docs/ARCHITECTURE.md`、本计划 | 功能 PR 容易重复改说明 | B 汇总；行为变化在对应 PR 只改必要段落 |
| `.github/workflows/ci.yml` | Job 名称直接关联分支保护 | C 持有；改名或拆 Job 前由全员确认并同步仓库设置 |

## 10. 风险与范围缩减

| 风险或阻塞项 | 触发信号 | 处理方式 |
| --- | --- | --- |
| 三人投入时间不明确 | 周任务连续两天无人推进 | 第 1 周确认可用时间；优先关键路径，学习原型立即延期 |
| 公共契约反复变化 | 同一字段在两个 PR 中被重复修改 | 停止消费者 PR，先合最小契约修订和迁移测试 |
| Provider 服务或格式未确定 | W1-05 结束仍无法固定配置与 tool calling | 只支持一个经验证的 OpenAI-compatible Provider，不增加厂商抽象 |
| 安全测试失败 | 出现越界、符号链接或敏感文件读取 | 阻止发布；缩减展示和评测，不降低安全边界 |
| 两人审批造成等待 | 合格 PR 超过一个工作日未评审 | 每日同步指定两位评审人；不得改为直推 `main` |

可用工期不足时按顺序缩减：取消学习原型；减少非必要 CLI 展示；固定评测保留 3 个核心场景；只保留一个真实 Provider。不得删除 ReAct、四个只读工具、工作区安全、FakeProvider 离线 E2E 或裸 `yczx` 入口。

## 11. 最终验收清单

- [ ] 仓库结构符合第 3 节，没有空模块、缓存、日志或一次性文件；
- [ ] 根 README、docs 总览、架构、安全、协作和计划文档完整且无重复；
- [ ] 初始历史已安全备份，远端 `main` 以唯一根提交 `chore: initialize YCZX Code` 开始；
- [ ] 旧开发分支已清理，当前分支名和 PR 标题均通过 `git-policy`；
- [ ] `main` 已启用两人审批、线性历史和禁止绕过/强推/删除，所有 PR 必须通过 `git-policy` 和 `quality`；
- [ ] `uv lock --check`、`uv run ruff check .`、`uv run pytest`、PR 范围 `git diff --check` 全部通过；
- [ ] `uv run yczx` 默认在当前目录启动连续对话；
- [ ] Provider、Agent、工具、安全、上下文和 CLI 通过公共对象连接；
- [ ] 四个只读工具均只能通过 Registry 和安全策略执行；
- [ ] 工作区逃逸、符号链接、敏感文件和资源超限均被拒绝；
- [ ] FakeProvider 端到端测试不联网、不需要真实 API Key；
- [ ] 至少一个真实 OpenAI-compatible 模型完成工具调用和最终回答；
- [ ] 终端和错误不泄漏密钥、完整提示词、隐藏思维或未裁剪内容；
- [ ] 无文件写入、任意 Shell、多 Agent、MCP、插件和复杂 TUI；
- [ ] 无未关闭 P0 缺陷，三位成员共同确认 G3。

## 12. 待团队确认

- 三周具体日期和成员每日可投入时间；
- 真实 Provider 的服务地址、模型、凭据和调用预算；
- 仓库公开发布前采用的开源许可证。
