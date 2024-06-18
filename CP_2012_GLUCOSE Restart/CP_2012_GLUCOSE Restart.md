---
marp: true
theme: am_yoimiya
paginate: true
headingDivider: [2,3]
footer: \ *石晋* *2024.04.04*
math: mathjax
---

<!-- _class: cover_a-->
<!-- _paginate: "" -->
<!-- _footer: "" -->

# Refining restarts strategies for SAT and UNSAT

CP 2012 (CCF B)

## 简介

CDCL SAT求解器：

- 搜索空间的探索者
- **基于解析操作的“子句生产者”**

衡量子句质量：文字块距离(LBD)
$$
LBD(\{{}^1\bar a,{}^2\bar g,{}^2\bar d,{}^2\bar r,{}^4\bar c\})=3
$$
调和SAT实例和UNSAT实例：

- SAT实例：更多的赋值
- UNSAT实例：更多的子句（LBD较小）

## 重启策略

- 全局平均$LBD$
- 局部平均$LBD$：本次搜索最新产生的$X$个子句
- 若$localLBD\times K>globalLBD$​，重启

![#c h:300](./_CP_2012_GLUCOSE%20Restart.assets/image-20240331203211125.png)

- SAT实例：与超参数关系不大
- UNSAT实例：$X$较小时求解效果好

## 阻塞策略

- 最近5000次冲突时，已赋值变量的平均数量$AvgTrail$
- 最近$X$次冲突时（恰好满队列），已赋值变量数量$Trail$
- 若$Trail>R\times AvgTrail$，再尝试$X$次冲突（清空队列）
- 阻塞策略优先于重启策略

![#c h:300](./_CP_2012_GLUCOSE%20Restart.assets/image-20240331204611470.png)

## 实验结果

![#c h:550](./_CP_2012_GLUCOSE%20Restart.assets/image-20240331205046858.png)

## 总结

SAT问题是一个经典的NP完全问题。许多其他领域的问题都可以转化为SAT问题，并利用SAT求解器进行求解。搜索重启是现代SAT求解器的关键部分之一。一个优秀的重启策略可以提高现代SAT求解器的效率。作者将现代SAT求解器看作“子句生成器”，并使用文字块距离(LBD)对子句质量进行衡量。作者使用全局平均和局部平均的LBD作为动态评估子句质量的方法，并根据两者之间的差距，判断是否进行重启。针对该重启策略的实验表明：该重启策略能显著提高SAT问题中不可满足实例的判定效率，但对可满足实例没有显著的效率提升。作者又提出，当本次搜索中已赋值的变量数量显著多于之前搜索的平均数量时，对重启操作进行阻塞，以帮助可满足实例的判定。我认为本文提出的重启策略和阻塞策略想法简明、实现有效，有助于后续SAT求解器的设计。
