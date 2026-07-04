# OhMyOpenAgent 子 Agent 完整介绍

> oh-my-openagent 内置 **10 个 Agent** 的完整解读：角色定位、模型配置、职责分工、源码位置、提示词路由，以及 Agent 分类体系与协作关系图。

## 一、Agent 总览

oh-my-openagent 共有 **10 个内置 Agent**：

```typescript
// dist/agents/types.d.ts
type BuiltinAgentName =
  | "sisyphus"          // 主编排器
  | "hephaestus"        // 深度自主工作者
  | "oracle"            // 高级咨询师
  | "librarian"         // 代码库/文档研究员
  | "explore"           // 快速代码搜索
  | "multimodal-looker" // 多模态分析
  | "metis"             // 计划顾问
  | "momus"             // 计划评论家
  | "atlas"             // 编排器
  | "sisyphus-junior";  // 专注任务执行器
```

---

## 二、每个 Agent 详细介绍

### 1. 🏛️ Sisyphus（西西弗斯）— 主编排器

| 属性     | 值                                      |
| -------- | --------------------------------------- |
| 角色     | 主 Orchestrator（编排器）               |
| Mode     | `primary`（主 agent）                   |
| 你的配置 | `coding_plan/glm-5.2`                   |
| 模型路由 | GPT / GLM / Claude 各有不同 prompt 优化 |

**职责**：

- 规划、委派、协调、验证
- 并行派出多个子 agent
- 不达 100% 不停止
- 是整个系统的大脑

**源码位置**：

- `packages/omo-opencode/src/agents/builtin-agents/sisyphus-agent.ts`
- `packages/omo-opencode/src/agents/sisyphus-agent-config.ts`

**三种模型变体**：

```typescript
// dist/agents/sisyphus-agent-config.d.ts
buildGptSisyphusAgentConfig()    // GPT 模型优化
buildGlmSisyphusAgentConfig()    // GLM 模型优化
buildClaudeSisyphusAgentConfig() // Claude 模型优化
```

### 2. 🔨 Hephaestus（赫菲斯托斯）— 深度自主工作者

| 属性     | 值                                       |
| -------- | ---------------------------------------- |
| 角色     | 自主深度执行者                           |
| Mode     | `primary`                                |
| 绰号     | "The Legitimate Craftsman"（合法工匠）   |
| 模型限制 | 仅支持 GPT 模型                          |

**职责**：

- 给你一个目标，不给步骤——他自己探索代码库、研究模式、端到端执行
- 不需要手把手指导
- 适合复杂、模糊、需要自主探索的任务

**源码位置**：

- `packages/omo-opencode/src/agents/hephaestus/agent.ts`

**提示词路由**：

```typescript
type HephaestusPromptSource = "gpt-5-5" | "gpt-5-4" | "gpt";
// 只支持 GPT 模型，其他模型会抛出 UnsupportedHephaestusModelError
```

### 3. 🦉 Oracle（神谕）— 高级咨询师

| 属性     | 值                      |
| -------- | ----------------------- |
| 角色     | 只读高级咨询师          |
| Mode     | `subagent`              |
| 你的配置 | `coding_plan/glm-5.2`   |
| 成本     | **EXPENSIVE**（昂贵）   |

**职责**：

- 只读分析，不写任何代码
- 高 IQ 推理，适合：
  - 调试棘手问题
  - 架构设计评审
  - 复杂逻辑分析
  - 代码审查

**何时使用**：

> Oracle 是只读咨询 agent。高 IQ 推理专家，用于调试难题和高难度架构设计。

### 4. 📚 Librarian（图书馆员）— 代码库/文档研究员

| 属性     | 值                              |
| -------- | ------------------------------- |
| 角色     | 多仓库代码库理解                |
| Mode     | `subagent`                      |
| 你的配置 | `coding_plan/deepseek-v4-flash` |
| 成本     | **CHEAP**（便宜）               |

**职责**：

- 跨仓库分析代码库
- 搜索远程代码库
- 检索官方文档（Context7）
- 在 GitHub 上找实现示例
- 回答「这个库是怎么用的？」

**关键触发**：

> 提到外部库 → 派出 librarian

### 5. 🔍 Explore（探索者）— 快速代码搜索

| 属性     | 值                            |
| -------- | ----------------------------- |
| 角色     | 代码库上下文 grep             |
| Mode     | `subagent`                    |
| 你的配置 | `coding_plan/kimi-k2.7-code`  |
| 成本     | **CHEAP**（便宜）             |

**职责**：

- 「X 在哪里？」
- 「哪个文件有 Y？」
- 「找到做 Z 的代码」
- 快速 grep 搜索
- 适合并行派出多个进行广泛搜索

### 6. 👁️ Multimodal Looker（多模态观察者）— 媒体分析

| 属性     | 值                                 |
| -------- | ---------------------------------- |
| 角色     | 分析媒体文件                       |
| Mode     | `subagent`                         |
| 你的配置 | `highway/zai-org/glm-5v-turbo`     |
| 成本     | **CHEAP**（便宜）                  |

**职责**：

- 分析 PDF、图片、图表
- 从文档中提取特定信息
- 描述视觉内容
- 需要解释而非原始文本时使用

### 7. 🧠 Metis（墨提斯）— 计划顾问

| 属性     | 值                              |
| -------- | ------------------------------- |
| 角色     | 计划预审顾问                    |
| Mode     | `subagent`                      |
| 你的配置 | `coding_plan/deepseek-v4-pro`   |
| 成本     | **EXPENSIVE**（昂贵）           |

**职责**：

- 分析需求中的隐藏意图
- 发现歧义和 AI 失败点
- 在 Prometheus 写计划前做差距分析
- 检查：矛盾、缺失约束、范围蔓延、未验证的假设

> **名字来源**：希腊神话中的智慧女神，宙斯的第一任妻子。

### 8. 👑 Momus（莫摩斯）— 计划评论家

| 属性     | 值                              |
| -------- | ------------------------------- |
| 角色     | 计划审查专家                    |
| Mode     | `subagent`                      |
| 你的配置 | `coding_plan/deepseek-v4-pro`   |
| 成本     | **EXPENSIVE**（昂贵）           |

**职责**：

- 评估工作计划的质量
- 检查：清晰度、可验证性、完整性
- 用于 high-accuracy review 双审查
- 与 Oracle 一起做独立审查

> **名字来源**：希腊神话中的嘲讽之神，专门挑错。

### 9. 🌍 Atlas（阿特拉斯）— 编排器

| 属性     | 值                              |
| -------- | ------------------------------- |
| 角色     | Master Orchestrator（主编排器） |
| Mode     | `primary`                       |
| 你的配置 | `coding_plan/deepseek-v4-flash` |

**职责**：

- 通过 `task()` 协调工作
- 完成 todo 列表中的所有任务
- 并行派出、验证、循环

> 我（当前角色）就是这个 agent。

**提示词路由**：

```typescript
type AtlasPromptSource =
  | "default"      // Claude 4.6 系列
  | "gpt"          // GPT-5.5 优化
  | "gemini"       // Gemini 优化
  | "kimi"         // Kimi K2.x
  | "kimi-k2-7"    // Kimi K2.7
  | "opus-4-7"     // Claude Opus 4.7
  | "glm";         // GLM 5.2
```

**源码位置**：

- `packages/omo-opencode/src/agents/atlas/agent.ts`
- `packages/omo-opencode/src/hooks/atlas/` ← 32 个文件

### 10. ⚡ Sisyphus-Junior（小西西弗斯）— 专注任务执行器

| 属性     | 值                              |
| -------- | ------------------------------- |
| 角色     | 专注任务执行者                  |
| Mode     | `subagent`                      |
| 你的配置 | `coding_plan/deepseek-v4-flash` |
| 默认模型 | `anthropic/claude-sonnet-4-6`   |
| 默认温度 | `0.1`                           |

**职责**：

- 直接执行委派的任务，不产生其他 agent
- 通过 `category` 派出时使用
- 每个 category 有领域特定的配置

**提示词路由**：

```typescript
type SisyphusJuniorPromptSource =
  | "default"   | "kimi-k2"   | "kimi-k2-7"
  | "gpt"       | "gpt-5-5"   | "gpt-5-4"
  | "gemini"    | "glm-5-2";
```

> 这就是每次我用 `task(category="quick", ...)` 时派出的 agent。

---

## 三、Agent 分类体系

```typescript
// dist/agents/types.d.ts
type AgentCategory =
  | "exploration"   // 探索类：explore, librarian
  | "specialist"    // 专家类：oracle, multimodal-looker
  | "advisor"       // 顾问类：metis, momus
  | "utility";      // 工具类：sisyphus-junior
```

| 分类          | Agent             | 成本       | 用途           |
| ------------- | ----------------- | ---------- | -------------- |
| `exploration` | explore           | CHEAP      | 快速代码搜索   |
| `exploration` | librarian         | CHEAP      | 文档/代码研究  |
| `specialist`  | oracle            | EXPENSIVE  | 高级推理       |
| `specialist`  | multimodal-looker | CHEAP      | 媒体分析       |
| `advisor`     | metis             | EXPENSIVE  | 计划预审       |
| `advisor`     | momus             | EXPENSIVE  | 计划审查       |
| `utility`     | sisyphus-junior   | —          | 任务执行       |

---

## 四、Category 系统（Sisyphus-Junior 的领域路由）

你的配置中定义了 **8 个 category**，每个映射到不同模型：

| Category             | 模型                 | 用途                  |
| -------------------- | -------------------- | --------------------- |
| `visual-engineering` | deepseek-v4-flash    | 前端 / UI / UX / 设计 |
| `artistry`           | deepseek-v4-pro      | 创造性问题解决        |
| `ultrabrain`         | glm-5.2              | 硬核逻辑 / 架构       |
| `deep`               | glm-5.2              | 自主研究 + 执行       |
| `quick`              | kimi-k2.7-code       | 单文件修改 / 简单修复 |
| `unspecified-high`   | deepseek-v4-flash    | 高复杂度杂项          |
| `unspecified-low`    | kimi-k2.7-code       | 低复杂度杂项          |
| `writing`            | minimax-m2.7         | 文档 / 写作           |

---

## 五、Agent 关系图

```text
你（用户）
  │
  ├─→ 正常聊天    → Atlas（我）/ Sisyphus
  ├─→ /ulw-plan   → Prometheus（通过 SKILL.md 注入角色）
  └─→ /start-work → Atlas（通过 Boulder 状态切换）

Atlas / Sisyphus（编排器）
  ├─→ task(category="quick")           → Sisyphus-Junior（直接执行）
  ├─→ task(subagent_type="explore")    → Explore（搜索代码）
  ├─→ task(subagent_type="librarian")  → Librarian（研究）
  ├─→ task(subagent_type="oracle")     → Oracle（咨询）
  ├─→ task(subagent_type="metis")      → Metis（预审）
  └─→ task(subagent_type="momus")      → Momus（审查）

Hephaestus（深度工作者）
  └─→ 自主探索 + 端到端执行（仅 GPT 模型）
```

---

## 六、Agent 定义文件位置汇总

| Agent                      | 定义文件（GitHub 源码）                                              |
| -------------------------- | ------------------------------------------------------------------- |
| Sisyphus                   | `packages/omo-opencode/src/agents/builtin-agents/sisyphus-agent.ts` |
| Hephaestus                 | `packages/omo-opencode/src/agents/hephaestus/agent.ts`              |
| Atlas                      | `packages/omo-opencode/src/agents/atlas/agent.ts`                   |
| Sisyphus-Junior            | `packages/omo-opencode/src/agents/sisyphus-junior/agent.ts`         |
| 其他（oracle、explore 等） | `packages/omo-opencode/src/agents/builtin-agents/general-agents.ts` |
| Agent 类型定义             | `packages/omo-opencode/src/agents/types.ts`                         |
| Agent 构建器               | `packages/omo-opencode/src/agents/agent-builder.ts`                 |
| 你的配置                   | `~/.config/opencode/oh-my-openagent.jsonc`                          |

---

> **文档结束** | 配套文档见 [`源码分析.md`](./源码分析.md)。
