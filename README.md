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
├── graph_v_of_v_idealID_DPBF_only_ec.h  # [本人编写] DPBF 核心算法 (~100 行)
├── 数据结构大作业报告.docx               # 大作业实验报告
├── README.md                             # 本文件
├── .gitignore                            # Git 忽略规则
│
└── GST-DPBF/                             # 完整可编译项目
    ├── CMakeLists.txt                    # CMake 构建配置
    ├── CppProperties.json                # VS 项目属性
    ├── README.md                         # 项目详细说明（环境配置、数据格式等）
    ├── img_1.png                         # 问题示意图
    ├── src/
    │   └── try_cpu.cpp                   # 主程序入口
    ├── data/
    │   ├── data0.csv ~ data9.csv         # 测试数据（图数据 + 分组信息）
    │   └── result                         # 标准输出结果
    └── rucgraph/                         # 图算法框架
        ├── graph_v_of_v_idealID/
        │   └── graph_v_of_v_idealID.h
        ├── graph_hash_of_mixed_weighted/
        │   ├── graph_hash_of_mixed_weighted.h
        │   ├── graph_hash_of_mixed_weighted_binary_operations.h
        │   └── two_graphs_operations/
        │       └── graph_hash_of_mixed_weighted_to_graph_v_of_v_idealID.h
        ├── graph_v_of_v_idealID_DPBF_only_ec.h          # [本人编写] DPBF 算法实现（同上）
        ├── graph_hash_of_mixed_weighted_read_for_GSTP.h  # GSTP 数据输入
        ├── graph_hash_of_mixed_weighted_generate_random_groups_of_vertices.h
        ├── test_cpu.h                                     # 测试框架
        └── text_mining/
            ├── parse_string.h
            └── print_items.h
```

### 本人编写的核心文件

- **`graph_v_of_v_idealID_DPBF_only_ec.h`**（根目录）：DPBF 算法独立版本，可单独查看
- **`GST-DPBF/rucgraph/graph_v_of_v_idealID_DPBF_only_ec.h`**：集成在项目中的同一文件

其余 `rucgraph/` 下的文件为框架代码，提供图的存储结构、格式转换、输入解析等基础设施。

## 运行环境与编译

详细的环境配置和运行说明请参见 **[GST-DPBF/README.md](GST-DPBF/README.md)**。

简要步骤：

- **操作系统**: Windows 11
- **编译器**: Visual Studio 17 2022 (MSVC)
- **依赖**: Boost 1.87, CMake 3.29+

```cmd
cd GST-DPBF
mkdir build
cd build
cmake .. -G "Visual Studio 17 2022"
cmake --build .
./bin/Debug/DPBF.exe
```

预期输出（与 `GST-DPBF/data/result` 一致）：

```
start..
------------------------------------------------------------
iteration 0
3
------------------------------------------------------------
iteration 1
4
...
------------------------------------------------------------
iteration 9
18
```

## 参考

- 算法参考论文：*Finding Top-k Min-Cost Connected Trees in Databases* — https://www.microsoft.com/en-us/research/wp-content/uploads/2016/02/icde07steiner.pdf
- 原项目参考：https://github.com/SakuraMarble/GST-DPBF
