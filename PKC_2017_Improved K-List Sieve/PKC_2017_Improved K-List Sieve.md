---
marp: true
theme: am_yoimiya
paginate: true
headingDivider: [2,3]
footer: \ *石晋* *2024.11.11*
math: mathjax
---

<!-- _class: cover_a -->
<!-- _paginate: "" -->
<!-- _footer: "" -->

# Improved Algorithms for the Approximate k-List Problem in Euclidean Norm

Gottfried Herold, Elena Kirshanova
PKC(CCF-B) 2017

## 研究问题及研究意义

研究问题：欧几里得范数下的近似k-list问题（即k筛法）

研究意义：可以从理论层面降低筛法时间与空间复杂度，提高实际求解效率

k-list问题：给定k个列表$L_i \subseteq X(i = 1,...,k)$，找到满足某些条件的k元组$(x_1, ..., x_k)\in L_1 \times ...\times L_k$
筛法：$||x_1 \pm ... \pm x_k||<min\{||x_1||,...,||x_k||\}$

## 核心想法

- 观察到近似k-list问题的所有解在空间中构成了一个特定的配置

  - 配置问题：$(x_1, ..., x_k)$中的向量对$(x_i, x_j)$都满足某些内积条件，$|\langle x_i,x_j \rangle - C_{i,j}| \leq \varepsilon$

- 判断k元组是否构成配置，快于直接验证解

## 论文贡献

理论层面：

- 提出配置问题刻画k-list问题的解

- 证明寻找较短向量可以简化为配置问题

实现层面：

- 给出期望输入列表大小和预期筛选成功概率，实现了指数级的加速

- 使用类似局部敏感哈希的方式改进配置算法，进一步优化了算法时间和空间复杂度

## 个人评价

- 给出了一种新的思路对筛法进行优化

- 论文中提出的算法在理论和实践层面都有价值

## 总结

格中最短向量问题（SVP）是格密码学中的核心难题。格密码中许多底层问题（NTRU，SIS，LWE）的密码学分析都可以归结为格中最短向量问题。SVP求解算法分为两类：枚举与筛选。筛选算法对大规模的向量集合进行内部组合，逐步降低向量集合的整体长度。筛选算法的一大问题是需要使用指数级别的存储空间，而三筛法可以缓解这一问题。在该论文中，作者使用k-list问题对筛法进行刻画，并提出配置问题及算法，快速判断向量组合后能否产生更短的向量。我认为使用k-list问题分析筛法是一个好的思路，能够使用更加数学的方式对筛法进行优化。（22）
