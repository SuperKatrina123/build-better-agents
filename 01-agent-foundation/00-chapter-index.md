---
title: 第一章：Agent 的认知基础
aliases:
  - 第一章
  - Chapter 01
tags:
  - agent-course
  - chapter-01
  - index
status: draft
---

# 第一章：Agent 的认知基础

> [!abstract] 本章定位
> 在动手搭任何系统之前，先建立正确的心智模型。
> 本章回答一个问题：**Agent 到底在"跑什么"，以及你的任务应该怎么组织它的控制流。**

---

## 通用原理

这一章不是在讲某一种具体 Agent，而是在回答所有 Agent 都绕不过去的三个基础问题：

- 运行时到底是谁在决定下一步动作
- 状态存在哪里，又如何影响下一轮判断
- 任务应该采用哪种控制模式，而不是靠直觉堆功能

无论你做的是 Coding Agent、Research Agent、Support Agent 还是业务流程 Agent，只要系统拥有“目标、状态、动作、反馈”的闭环，这一章讨论的边界就成立。

## 场景映射

| Agent 类型 | 本章关注的控制问题 | 典型动作 | 典型状态容器 |
|-----------|------------------|---------|-------------|
| Coding Agent | 什么时候让模型决定下一步，什么时候由代码编排 | 读文件、改文件、调用工具 | `messages[]`、任务状态文件 |
| Research Agent | 什么时候继续搜索，什么时候汇总结论 | 检索、阅读、比较、摘要 | 对话历史、研究笔记、来源列表 |
| Support Agent | 什么时候直接回复，什么时候升级或转人工 | 检索知识、生成回复、更新工单 | 工单状态、用户上下文、回复草稿 |
| 流程 / 业务执行 Agent | 什么时候自动推进，什么时候等待外部条件 | 读状态、调用业务 API、推进节点 | 流程记录、审批状态、执行日志 |

## 章节地图

```
1.1 Agent Loop 的最小实现
    → 搞清楚 while(true) + messages[] + 工具执行的核心结构

1.2 Workflow vs Agent：控制权在谁手里
    → 判断什么时候用 Agent，什么时候用 Workflow，为什么混合是正确答案

1.3 五种控制模式图谱
    → Chaining / Routing / Parallelization / Orchestrator-Workers / Evaluator-Optimizer

1.4 设计决策 #1：给你的任务选对控制模式
    → 五个问题的决策框架 + 功能追齐 Agent 的最终选型
```

## 节点导航

- [1.1-Agent-Loop最小实现](1.1-Agent-Loop最小实现.md)
- [1.2-Workflow-vs-Agent](1.2-Workflow-vs-Agent.md)
- [1.3-五种控制模式图谱](1.3-五种控制模式图谱.md)
- [1.4-设计决策1-控制模式选型](1.4-设计决策1-控制模式选型.md)

---

## 本章核心结论

| 节 | 一句话 |
|---|---|
| 1.1 | Loop 极简且稳定，所有扩展叠在外部，messages[] 是唯一状态容器 |
| 1.2 | 控制权在代码 = Workflow；控制权在 LLM = Agent；混合最常见 |
| 1.3 | 五种模式覆盖绝大多数场景，真实系统是多种模式嵌套 |
| 1.4 | 五问决策法：每个"是"叠加一种模式 |

## 主案例展开：功能追齐 Agent

这一章的主案例仍然是“功能追齐 Agent”，但这里它只是一个实例化对象：

- 在这个案例里，核心问题是“哪些步骤应交给 LLM 判断，哪些由代码骨架固定”
- `Chaining + Routing + Parallel + Eval-Opt` 是对这一个案例的控制模式选型结果
- 你应该复用的是“五问决策法”和控制权判断框架，而不是直接照搬它的模式组合

## 本章产出物

完成本章后你应该有：

- ✅ 对功能追齐 Agent 的控制模式选型（Chaining + Routing + Parallel + Eval-Opt）
- ✅ 一份自己项目的控制模式选型清单

---

## 下一章

> [!note] 第二章：上下文工程
> 控制模式骨架搭好之后，Agent 能不能跑好，
> 取决于模型在每一步"能看到什么"。
> → [02-context-engineering](../02-context-engineering/00-chapter-index.md)
