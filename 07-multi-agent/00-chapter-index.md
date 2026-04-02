---
title: 第七章：多 Agent 系统——突破单 Agent 的边界
aliases:
  - 第七章导航
  - chapter-07-index
tags:
  - agent-course
  - chapter-07
  - index
status: draft
---

# 第七章：多 Agent 系统——突破单 Agent 的边界

## 章节目标

完成本章后，你将能够：

1. **识别瓶颈**：判断一个任务是否真的需要多 Agent，而不是被"多 Agent 更高级"的直觉误导
2. **选择拓扑**：根据任务特征（顺序依赖、并行可能性、专业化需求）选择合适的 Agent 协作模式
3. **设计 Orchestrator**：在"足够协调"和"不越界执行"之间找到平衡，建立清晰的责任边界
4. **设计通信层**：在 Agent 之间高效传递信息，避免上下文污染和 token 浪费

---

## 通用原理

多 Agent 不是“更高级的默认答案”，而是当单 Agent 在上下文容量、并行性、专业化或职责边界上遇到瓶颈时的一种组织方式。本章真正关心的是：

- 什么时候应该拆分职责，而不是继续堆上下文
- 拆分之后该选什么拓扑、怎么分配控制权
- Agent 之间如何协作，才能降低耦合和通信成本

这些问题同样存在于研究、客服和业务流程场景，不只属于代码类 Agent。

## 场景映射

| Agent 类型 | 为什么会拆多 Agent | 典型角色划分 | 主要通信对象 |
|-----------|------------------|-------------|-------------|
| Coding Agent | 仓库多、上下文重、分析与执行职责不同 | Orchestrator、Research、Analysis、Execution | 任务状态、差异结果、执行摘要 |
| Research Agent | 主题广、资料多、需要并行探索 | Coordinator、Searcher、Verifier、Writer | 来源摘要、证据结论、待验证问题 |
| Support Agent | 需要意图识别、检索、回复、升级分层 | Router、Retriever、Responder、Escalation | 用户问题、命中知识、处理建议 |
| 流程 / 业务执行 Agent | 业务步骤复杂、节点责任不同 | Scheduler、Checker、Executor、Auditor | 当前节点、规则结果、执行回执 |

## 章节地图

节间依赖关系（建议阅读顺序）：

```
7.1 为什么需要多 Agent
    │  （确认需要后，选择架构）
    ▼
7.2 多 Agent 拓扑模式
    │  （选定层级拓扑，研究 Orchestrator）
    ▼
7.3 Orchestrator 设计
    │  （Orchestrator 管理 Sub-Agent，研究 Sub-Agent 设计）
    ▼
7.4 Sub-Agent 设计
    │  （Agent 有了，研究如何通信）
    ▼
7.5 Agent 间通信
    │  （所有设计整合）
    ▼
7.6 设计决策 7（架构综合）
```

---

## 章节内容

| 节次 | 标题 | 关键词 | 状态 |
|------|------|--------|------|
| [[7.1-为什么需要多Agent\|7.1]] | 为什么需要多 Agent：单 Agent 的天花板在哪里 | 上下文瓶颈、并行、专业化 | draft |
| [[7.2-多Agent拓扑模式\|7.2]] | 多 Agent 拓扑模式：五种架构选择 | Sequential、Parallel、Hierarchical | draft |
| [[7.3-Orchestrator设计\|7.3]] | Orchestrator 设计：协调者的责任边界 | 任务分发、状态追踪、结果聚合 | draft |
| [[7.4-SubAgent设计\|7.4]] | Sub-Agent 设计：专业化的执行单元 | 专业化、上下文隔离、结构化输出 | draft |
| [[7.5-Agent间通信\|7.5]] | Agent 间通信：数据如何在 Agent 之间流动 | Blackboard、直接传递、通信压缩 | draft |
| [[7.6-设计决策7-多Agent架构\|7.6]] | 设计决策 7：功能追齐 Agent 多 Agent 完整架构 | 完整架构、并行策略、数据流 | draft |

---

## 本章配套资产

| 文件 | 说明 |
|------|------|
| `![[orchestrator-subagent.excalidraw]]` | Orchestrator-SubAgent 架构关系图 |
| `![[agent-communication.excalidraw]]` | Agent 间通信模式对比（直接传递 vs 共享状态） |

---

## 主案例展开：功能追齐 Agent

### 三个 Sub-Agent 分工

| Sub-Agent | 职责 | 工具 | 输出 |
|-----------|------|------|------|
| Research Agent | 探索目标仓库，客观记录代码结构和功能现状 | `list_repo_files`, `read_repo_file` | 仓库结构摘要 + 代码片段 |
| Analysis Agent | 对比 App vs H5，输出优先级差距清单 | `diff_feature` | P0/P1/P2 功能差距列表 |
| Execution Agent | 基于差距清单，生成最小化代码修改方案 | `read_repo_file`, `resolve_feature_spec` | Unified diff + 说明 |

### Orchestrator 职责

- ✅ 读取 `memory/MEMORY.md`，获取待追齐功能
- ✅ 分发任务给 Sub-Agent（最多 3 个并行）
- ✅ 读取各阶段 `summary`，追踪进度
- ✅ 处理失败（降级策略，记录并继续）
- ✅ 更新 `memory/completed.json`
- ❌ 不直接调用 `read_repo_file`、`diff_feature` 等业务工具
- ❌ 不在上下文中缓存 Sub-Agent 的完整输出

### 数据流

```
MEMORY.md
  ↓ Orchestrator 读取
Research Agent → task-state/{id}.json (research.findings)
  ↓ Analysis Agent 读取
Analysis Agent → task-state/{id}.json (analysis.gaps)
  ↓ Execution Agent 读取
Execution Agent → task-state/{id}.json (execution.diffs)
  ↓ Orchestrator 整合
completed.json
```

### 并行策略

- Research 阶段：最多 3 个并行（`Promise.allSettled`，单个失败不影响其他）
- Analysis + Execution：顺序执行（有数据依赖）
- 批次间隔：1 秒（防止 API 限流）

---

## 与其他章节关联

- **← [[../05-planning-long-tasks/00-chapter-index|第五章：规划与任务分解]]**：Orchestrator 的任务分解逻辑依赖第五章方法论
- **← [[../06-harness/00-chapter-index|第六章：Agent Harness 与工具层]]**：Sub-Agent 使用的所有工具在第六章中定义和注册
- **→ [[../08-evaluation/00-chapter-index|第八章：多 Agent 系统评测]]**：评测 Research 准确率、Analysis 精度、Execution 代码质量的方法

这套 Orchestrator + Sub-Agent 分工是主案例的具体拓扑，不是本章唯一结论。更通用的是：

- 先确认是否真的需要拆分，再决定怎么拆
- 拆分后优先确保职责清晰、上下文隔离、通信可压缩
- 多 Agent 带来的是协作收益，也带来新的协调成本
