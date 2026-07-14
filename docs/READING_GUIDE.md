# 两份系统教程怎么读

这份文档专门回答：`hello-agents` 和 `learn-claude-code` 应该怎么配合着看。

结论：不要先完整读完一个，再读另一个。应该双主线并行：

```txt
hello-agents        学理论地图、Agent 概念、经典范式、上下文、评测
learn-claude-code   学工程实现、agent loop、tools、permissions、memory、MCP
YCZX-Code           每周把学到的东西落到自己的 CLI Agent
```

## 为什么以这两个项目为主线

`hello-agents` 的定位是《从零开始构建智能体》，它把内容拆成智能体基础、语言模型基础、经典范式、框架实践、自研 Agent 框架、记忆与检索、上下文工程、通信协议、评估和综合案例。它适合作为系统学习地图。

`learn-claude-code` 的定位是从 0 到 1 构建一个 nano Claude Code-like agent harness。它明确强调 Agent 产品需要 Model + Harness，并把课程拆成 `s01` 到 `s20`：agent loop、tool use、permission、hooks、todo、subagent、skill loading、context compact、memory、system prompt、error recovery、task system、background tasks、agent teams、MCP、comprehensive agent。它适合作为工程实现主线。

所以最优策略是：

- 先用 `hello-agents` 建立概念坐标。
- 再用 `learn-claude-code` 学会一个个 harness 机制。
- 每周都在 `YCZX-Code` 实现一个对应模块。

## 阅读原则

### 1. 不要把阅读和开发分开

错误方式：

```txt
先读两个月教程
然后再开始写项目
```

正确方式：

```txt
每周读一点
每周实现一点
每周演示一点
```

AI 应用开发是工程学科，必须边读边跑代码。

### 2. hello-agents 读“主干”

`hello-agents` 很系统，但内容很大。暑期主线不需要把所有章节等量投入。

优先级：

- 必读：第一章、第三章、第四章、第七章、第八章、第九章、第十章、第十二章、第十六章。
- 快速浏览：第二章、第五章、第六章、第十一章、第十三到十五章。
- 暂缓深入：Agentic-RL、训练相关内容。

原因：你的暑期目标是 AI 应用开发和 Coding Agent，不是训练模型。

### 3. learn-claude-code 按 s01-s20 主线读

它的 README 说明新读者应读根目录 `s01_agent_loop/` 到 `s20_comprehensive/` 的当前 20 课主线，不要混用 legacy 12 课编号。

暑期优先级：

- 必读必做：s01、s02、s03、s05、s08、s09、s10、s11、s19、s20。
- 建议阅读：s04、s06、s07、s12。
- 后期再看：s13、s14、s15、s16、s17、s18。

原因：第一版 YCZX Code 需要 agent loop、tools、permission、planning、context、memory、system prompt、error recovery、MCP 概念和综合组装；多 Agent、worktree isolation、cron 可以后置。

### 4. 每章都要有一个输出

每读一章，至少产出一个东西：

- 一段自己的理解。
- 一个最小代码实验。
- 一个对 YCZX-Code 的设计决定。
- 一个 issue 或 TODO。

不要只收藏、划线、点 star。

## 8 周双主线安排

| 周次 | hello-agents | learn-claude-code | YCZX-Code 产出 |
|---|---|---|---|
| 第 0 周 | 前言、第一章、第三章 | README、s01 | 明确 Agent / Harness，跑通 `yczx --help` |
| 第 1 周 | Python/API 基础自行补齐，第三章复习 | s01 | CLI 骨架、文件树工具、路径安全 |
| 第 2 周 | 第四章经典范式 | s01、s02 | 模型 Provider、流式输出、只读工具 |
| 第 3 周 | 第七章自研 Agent 框架 | s02、s03 | ToolRegistry、tool calling、权限雏形 |
| 第 4 周 | 第八章记忆与检索、第九章上下文工程 | s08、s10 | ContextBuilder、AGENTS.md、prompt assembly |
| 第 5 周 | 第九章上下文工程、第十二章评估 | s03、s05、s11 | 文件编辑、diff、confirm、错误恢复 |
| 第 6 周 | 第十章协议概念、第十二章评估 | s12、s19 | shell 工具、任务记录、MCP 概念设计 |
| 第 7 周 | 第十二章评估、第十六章毕业设计 | s09、s20 | SQLite 会话、成本、eval cases、完整 Demo |
| 第 8 周 | 第十六章毕业设计与未来展望 | s20 复盘 | README、架构文档、演示视频、项目复盘 |

## 每周阅读流程

每周按这个顺序：

1. 先读 `hello-agents` 对应章节，写下本周概念。
2. 再读 `learn-claude-code` 对应章节，理解工程机制。
3. 跑它们提供的代码或示例。
4. 在 `YCZX-Code` 实现自己的简化版本。
5. 写一段复盘：这个机制为什么需要、怎么实现、有什么风险。

## 每章笔记模板

```md
# 章节

## 这一章解决什么问题

## 关键概念

## 和 YCZX-Code 的关系

## 我实现了什么

## 还没理解什么

## 下一步
```

## 具体怎么看 hello-agents

### 第一遍：快速建立地图

用 2 到 3 天快速扫完 README 和内容导航，只记住五大部分：

- 智能体与语言模型基础
- 构建大语言模型智能体
- 高级知识扩展
- 综合案例进阶
- 毕业设计及未来展望

第一遍不要纠结细节。

### 第二遍：按项目需要精读

精读这些：

- 第一章：智能体定义、类型、范式与应用
- 第三章：大语言模型基础
- 第四章：ReAct、Plan-and-Solve、Reflection
- 第七章：从 0 构建 Agent 框架
- 第八章：记忆、RAG、存储
- 第九章：上下文工程
- 第十章：MCP 等通信协议
- 第十二章：评估
- 第十六章：毕业设计

### 第三遍：项目完成后回看

等 YCZX-Code 第一版跑起来，再回看低代码平台、多 Agent 案例、DeepResearch、赛博小镇等内容。那时你会更容易理解这些高级案例为什么这样设计。

## 具体怎么看 learn-claude-code

### 第一遍：理解总原则

先读 README，重点理解：

- Agency 来自模型。
- 我们做的是 harness。
- Harness = tools + knowledge + observation + action interfaces + permissions。
- Claude Code 的本质是一个 agent loop 加一组 harness 机制。

### 第二遍：按 YCZX-Code 功能精读

按这个顺序：

1. s01 Agent Loop
2. s02 Tool Use
3. s03 Permission
4. s10 System Prompt
5. s08 Context Compact
6. s09 Memory
7. s05 TodoWrite
8. s11 Error Recovery
9. s19 MCP Plugin
10. s20 Comprehensive

这个顺序比原始顺序更贴合你的暑期 MVP。

### 第三遍：后期扩展

后续再读：

- s04 Hooks
- s06 Subagent
- s07 Skill Loading
- s12 Task System
- s13 Background Tasks
- s15-s18 Agent Teams / Worktree Isolation

这些是第二阶段能力，不要压垮暑期第一版。

## 判断是否真的学会

每周问自己三个问题：

1. 我能不能用自己的话讲清楚这个机制为什么存在？
2. 我能不能在 YCZX-Code 里实现一个简化版本？
3. 我能不能用一个 Demo 展示它解决了真实问题？

三个问题都能回答，才算学会。
