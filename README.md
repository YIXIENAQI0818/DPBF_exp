# DPBF — 组斯坦纳树问题的动态规划最佳优先算法

> 数据结构与算法 II 大作业 · 2025 春 · 程宣赫 (2023202250)

## 问题概述

本项目实现了 **Group Steiner Tree Problem (GSTP)** 的 **DPBF (Dynamic Programming Best-First)** 精确算法。给定一个带权无向图和若干节点组，GSTP 要求找到一棵代价最小的树，使得每个组中至少有一个节点被包含在树中。

GSTP 是经典 Steiner Tree 问题的推广，属于 NP-hard 问题，在网络设计、VLSI 布局、生物信息学等领域有广泛应用。

## 算法原理

DPBF 算法结合了 **状态压缩动态规划** 和 **最佳优先搜索**，核心思想如下：

### 状态定义

```
dp[v][flag] = 以节点 v 为根、覆盖了 flag 所表示的所有组的最小代价
```

其中 `flag` 是一个 bitmask，第 `i` 位为 1 表示第 `i` 个组已被覆盖。

### 算法流程

```
1. 初始化：对每组选取一个代表节点，将其 flag 设为对应位，cost 为 0，入队
2. 主循环：从优先队列中取出当前代价最小的树
   - 若已覆盖所有组 → 返回当前代价（即为最优解）
   - 否则执行两种扩展操作：

   2.1 Tree Grow（生长）：沿边扩展当前树到邻居节点
        newCost = cost + weight(v, neighbor)

   2.2 Tree Merge（合并）：将当前树与另一棵已到达同根节点的树合并
        mergedFlag = flag1 | flag2
        mergedCost = cost1 + cost2
```

### 关键技术点

| 技术 | 说明 |
|------|------|
| 优先队列 (Priority Queue) | 以代价为键的最小堆，保证始终扩展当前最优的部分解 |
| 状压 DP (Bitmask DP) | 用 `int` 的二进制位表示组覆盖状态，最多支持 31 个组 |
| 惰性删除 (Lazy Deletion) | 出队时检查 `dp[v][flag]` 是否为最新值，避免处理过期状态 |
| 提早终止 (Early Exit) | 一旦出队状态覆盖所有组，立即返回——这就是最优解 |

### 正确性保证

由于优先队列始终先扩展代价最小的状态，第一个被弹出且覆盖全集的树必然是全局最优解。这类似于 Dijkstra 算法的贪心性质——DPBF 在状态空间图上运行了一个 Dijkstra。

## 文件结构

```
DPBF大作业/
├── graph_v_of_v_idealID_DPBF_only_ec.h  # DPBF 核心算法实现 (~100 行)
├── 数据结构大作业报告.docx               # 大作业实验报告
└── README.md                             # 本文件
```

### 依赖

- `graph_hash_of_mixed_weighted` — 混合权重图数据结构
- `graph_v_of_v_idealID` — 邻接表图表示
- C++17 标准库 (`<vector>`, `<queue>`, `<unordered_set>`)

## 编译

```bash
# 需要链接 graph_hash_of_mixed_weighted 库
g++ -std=c++17 -O2 -o dpbf your_main.cpp
```

## 参考

该实现参考了关于 Group Steiner Tree 问题的 DPBF 精确算法论文，算法时间复杂度为 $O(3^k \cdot (n + m \log n))$，其中 $k$ 为组数，$n$ 为节点数，$m$ 为边数。
