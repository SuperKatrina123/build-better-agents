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

- [[1.1-Agent-Loop最小实现]]
- [[1.2-Workflow-vs-Agent]]
- [[1.3-五种控制模式图谱]]
- [[1.4-设计决策1-控制模式选型]]

---

## 本章核心结论

| 节 | 一句话 |
|---|---|
| 1.1 | Loop 极简且稳定，所有扩展叠在外部，messages[] 是唯一状态容器 |
| 1.2 | 控制权在代码 = Workflow；控制权在 LLM = Agent；混合最常见 |
| 1.3 | 五种模式覆盖绝大多数场景，真实系统是多种模式嵌套 |
| 1.4 | 五问决策法：每个"是"叠加一种模式 |

## 本章产出物

完成本章后你应该有：

- ✅ 对功能追齐 Agent 的控制模式选型（Chaining + Routing + Parallel + Eval-Opt）
- ✅ 一份自己项目的控制模式选型清单

---

## 下一章

> [!note] 第二章：上下文工程
> 控制模式骨架搭好之后，Agent 能不能跑好，
> 取决于模型在每一步"能看到什么"。
> → [[02-context-engineering]]
