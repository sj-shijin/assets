---
marp: true
theme: am_yoimiya
paginate: true
headingDivider: [2,3]
footer: \ *石晋* *2024.05.30*
math: mathjax
---

<!-- _class: cover_a-->
<!-- _paginate: "" -->
<!-- _footer: "" -->

# 并行/分布式SAT求解器(SC23)

## 并行SAT求解的7个挑战

<!-- _class: cols2_ol_sq fglass -->

- 动态资源分配
- 任务分解
- 预处理
- 知识分享
- 整数分解：使用SAT判断大数分解
- 特殊编码：除CNF之外的新编码
- 从零开始：并行专用SAT算法和数据结构

来源：[Seven Challenges in Parallel SAT Solving](./_%E5%B9%B6%E8%A1%8CSAT%E6%B1%82%E8%A7%A3%E5%99%A823.assets/Seven_Challenges_in_Parallel_SAT_Solving.pdf)

## SAT求解器概述

![#c](./_%E5%B9%B6%E8%A1%8CSAT%E6%B1%82%E8%A7%A3%E5%99%A823.assets/SAT%E6%B1%82%E8%A7%A3%E5%99%A8.svg)

### PRS

<!-- _class: cols-2 -->

<div class=ldiv>

- ParKissat(SC22并行\#1)：使用Painless框架，基于KissatMAB(SC21串行\#1)

  - Random Shuffle(RS)变量赋值顺序
  
- PRS(SC23并行\#1)：ParKissat-RS的改进，基于Kissat-Inc(SC22串行\#2)

  - 所有变量平均分配到每个线程，作为决策变量
  - 定期共享学习子句到**全部其他线程**

</div>

<div class=rdiv>

- PRS-DIST(SC23分布\#2)

  - 每个节点运行16线程的PRS
  - 定期共享学习子句到**相邻节点**

| 基求解器            | 共享数量限制 | 比例 |
| ------------------- | :----------: | :--: |
| Kissat-Inc -sat     |     1500     | 1/8  |
| Kissat-Inc -unsat   |     3000     | 1/4  |
| Kissat-Inc -default |     1500     | 1/2  |
| Maple-COMSPS        |     3000     | 1/8  |

</div>

### Mallob(SC22)

- MPI进程**直接管理**4个求解器线程修改为MPI进程下的一个**子进程管理**4个求解器线程：单个求解器的崩溃不会影响到MPI进程，但会带来一定的通信开销。

- 改进子句过滤机制：使用一个**比特数组**标记已经被分享过的子句，并与子句缓存一起**共享**。

- 针对极大规模任务：**每个MPI**至少负责1个任务改进为**每台机器**至少负责1个任务。

提交设置：

- Mallob-Ki(并行\#4)：基于Kissat（由于失误实际使用lingeling）
- Mallob-Kicaliglu(分布\#1)：640Kissat+480Cadical+320Lingeling+160Glucose

### Mallob(SC23)

- 1个MPI进程管理**4个物理线程**修改为管理**1台物理机器**

- LBD修改：当子句被共享到一个新任务中时，将LBD值修改到**最大**（子句长度），思想来源于TopoSAT2

- 共享频率：**1次/s**修改为**0.33/s**，但缓冲区修改为原先的**1/3**

- 完善子句过滤数据结构，添加垃圾回收机制，优化锁的实现

提交设置：

- Mallob64/32(并行#2/6)：基于kissat
- Mallob1600(分布#1)：800Kissat+533Cadical+267Lingeling
