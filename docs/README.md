# YCZX Code 文档

本目录只保留预览版开发和验收需要的文档。

| 文档 | 内容 | 适合阅读时机 |
| --- | --- | --- |
| [ARCHITECTURE.md](ARCHITECTURE.md) | 主流程、模块边界、公共契约和技术基线 | 开发功能前 |
| [SAFETY.md](SAFETY.md) | 路径、敏感文件、资源限制和安全测试 | 实现或评审工具前 |
| [TEAM_PLAN.md](TEAM_PLAN.md) | 三周分工、任务、Git 规范、里程碑和验收 | 安排迭代时 |

仓库根目录还包含：

- [README.md](../README.md)：项目介绍、范围、安装和入口；
- [CONTRIBUTING.md](../CONTRIBUTING.md)：人类开发者的 Git 与 PR 规则；
- [AGENTS.md](../AGENTS.md)：Coding Agent 的工作边界。

## 维护规则

- 一个主题只保留一份权威文档，其他文档使用链接引用；
- 行为、命令或公共接口变化时，在同一 PR 更新相关文档；
- 个人学习笔记和一次性调研不进入本目录；
- 不提交生成的 PDF，PDF 由需要者从 Markdown 本地导出。
