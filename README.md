# oh-my-openagent 源码分析

> 一份对 [oh-my-openagent](https://github.com/code-yeongyu/oh-my-openagent)（omo / oh-my-opencode）的中文源码解读与 Agent 体系梳理。

oh-my-openagent 是面向 OpenCode 与 Codex 的多 Agent 编排插件——把「规划 → 编排 → 执行」拆成三个角色协作，配合 50+ Hooks、Skills 与 Boulder 状态系统，在复杂代码库上实现自主任务完成。

**本仓库是该项目源码的中文分析文档集，不是项目本身。** 如需查看原始 TypeScript 源码，请前往上游仓库 [`code-yeongyu/oh-my-openagent`](https://github.com/code-yeongyu/oh-my-openagent)。

---

## 文档目录

| 文档 | 内容 | 链接 |
| --- | --- | --- |
| 源码分析 | 目录结构 / 核心 Hook 源码 / 架构分层 / Boulder 状态系统 / 完整工作流时序 | [源码分析.md](./源码分析.md) |
| 子 Agent 完整介绍 | 10 个内置 Agent 的角色 / 模型配置 / 职责 / 提示词路由 / 分类体系 / 关系图 | [OhMyOpenAgent 子 Agent.md](./OhMyOpenAgent%20%E5%AD%90%20Agent.md) |

---

## 核心概念速览

### 三层协作模型

oh-my-openagent 由三个以希腊神话命名的 Agent 组成，形成「规划 → 编排 → 执行」流水线：

| Agent | 角色 | 决定的事 |
| --- | --- | --- |
| **Prometheus**（普罗米修斯） | 规划顾问 | 做什么 |
| **Atlas**（阿特拉斯） | 主编排器 | 谁来做、什么时候做、做完没 |
| **Sisyphus**（西西弗斯） | 任务执行者 | 动手做 |

### 10 个内置子 Agent

按职能分为四类：

- **探索类**（exploration）：`explore`、`librarian` — 代码搜索与文档研究
- **专家类**（specialist）：`oracle`、`multimodal-looker` — 高级推理与媒体分析
- **顾问类**（advisor）：`metis`、`momus` — 计划预审与审查
- **工具类**（utility）：`sisyphus-junior` — 专注任务执行

外加主编排器 `sisyphus` / `atlas` 与深度自主工作者 `hephaestus`。详见[子 Agent 完整介绍](./OhMyOpenAgent%20%E5%AD%90%20Agent.md)。

### 关键机制

| 机制 | 说明 |
| --- | --- |
| **Hooks 系统** | 50+ 个 Hook 挂载到工具执行 / 消息收发 / 会话生命周期，实现角色切换、写保护、自动续行等 |
| **Skills 系统** | `/ulw-plan`、`/ulw-research`、`/start-work` 等命令通过 `SKILL.md` 注入角色指令 |
| **Boulder 状态** | `.omo/boulder.json` 持久化工作状态，跨会话 / 跨重启恢复 |
| **Ralph 循环 / Todo 续行** | 不达 100% 不停止的执行回路 |

---

## 建议阅读顺序

1. 先读 [源码分析.md](./源码分析.md) —— 建立目录结构、Hook 机制、状态系统的整体认知
2. 再读 [子 Agent 完整介绍](./OhMyOpenAgent%20%E5%AD%90%20Agent.md) —— 理解每个 Agent 的定位与协作关系

---

## 致谢

本仓库的所有分析对象——oh-my-openagent 项目——由 [`code-yeongyu`](https://github.com/code-yeongyu) 开发并维护。原始源码、设计与命名均归属上游项目。

本仓库仅为学习与分析用途的衍生文档，不包含上游项目的可运行源码。
