---
marp: true
theme: am_yoimiya
paginate: true
headingDivider: [2,3]
footer: \ *石晋* *2024.03.14*
math: mathjax
---

<!-- _class: cover_a-->
<!-- _paginate: "" -->
<!-- _footer: "" -->

# 第4章 CDCL(Conflict-Driven Clause Learning) SAT求解器

Handbook of Satisfiability (2nd ed.), Chapter 4

## 章节简介

CDCL SAT求解器将SAT问题的求解能力从90年代的几百个变量提升到目前的**几百万**个变量。

CDCL SAT求解器最关键的部分是冲突分析。

本章主要介绍现代CDCL SAT求解器普遍的组成部分。

## 知识准备

CNF形式稍有改变：$\neg x \to \bar x$
$$
F = \{\{a,\bar b\},\{b,c\},\{b,\bar d\},\{\bar a,\bar c,d\}\}
$$
变量需要的属性：

- 值$v(x_i)$​：真、假、未赋值
- 决策等级$\delta(x_i)$：自然数、未赋值
- 赋值原因$\alpha(x_i)$：从单位子句推断（$C_i$）、从决策等级赋值、未赋值

## 顶层组织

<!-- _class: cols-2-46 -->

<div class=ldiv>

- 单位传播(UnitPropagation)：推理变量，返回是否存在冲突。
- 选择变量(PickBranchVariable)：选择下一个赋值的变量以及值。
- 冲突分析(ConflictAnalysis)：学习子句等，返回回溯等级。
- 搜索重启(TimeToRestart)：判断是否重启搜索。

</div>

![#c h:460](./_Handbook%20of%20SAT_4_CDCL%20SAT%E6%B1%82%E8%A7%A3%E5%99%A8.assets/image-20240312212753362.png)

## 冲突分析（Conflict Analysis）
<!-- _class: cols-2 -->

<div class=ldiv>

$$
\{1.\{\bar a,\bar b,c\},2.\{\bar a,d\},3.\{\bar c,\bar d,e\},4.\{\bar h,\bar e,f\},5.\{\bar e,g\},6.\{\bar f,\bar g\}\}
$$

![#c](./_Handbook%20of%20SAT_4_CDCL%20SAT%E6%B1%82%E8%A7%A3%E5%99%A8.assets/image-20240312220712793.png)

</div>

<div class=rdiv>

![#c](./_Handbook%20of%20SAT_4_CDCL%20SAT%E6%B1%82%E8%A7%A3%E5%99%A8.assets/image-20240312221632869.png)

![#c](./_Handbook%20of%20SAT_4_CDCL%20SAT%E6%B1%82%E8%A7%A3%E5%99%A8.assets/image-20240313122755011.png)

</div>

### 独一蕴含点（First UIP）

<!-- _class: cols-2 -->

<div class=ldiv>

![#c](./_Handbook%20of%20SAT_4_CDCL%20SAT%E6%B1%82%E8%A7%A3%E5%99%A8.assets/image-20240313122815762.png)

- 如何快速得到UIP：记录原因子句中决策等级低于当前等级的变量。
- 在UIP得到的学习子句特点：有且仅有一个当前决策等级（最大）的变量。
  - 保证回溯到决策等级第二大的变量时，一定可以使用该子句和单位传播规则确定一个变量的取值

</div>

<div class=rdiv>

![#c](./_Handbook%20of%20SAT_4_CDCL%20SAT%E6%B1%82%E8%A7%A3%E5%99%A8.assets/image-20240313123941169.png)

- 在FUIP得到的学习子句特点：该子句其他变量的数量不多于其他UIP的子句。
  - 回溯到的决策等级最低

</div>

### 学习子句最小化

<!-- _class: cols-2 -->

<div class=ldiv>

$$
\{\{\bar a,\bar b,r\},\{\bar r,d\},\{\bar c,\bar d,e\},\{\bar c,\bar d,f\},\{\bar d,s\},\{\bar s,g\},\{\bar e,\bar g,h\},\{\bar f,\bar h,\bar a\}\}
$$

![#c h:270](./_Handbook%20of%20SAT_4_CDCL%20SAT求解器.assets/image-20240313133620355.png)

FUIP的学习子句：$\{\bar a,\bar g,\bar d,\bar r,\bar c\}$

- 局部最小化(Local Minimization)

  自包含变量消除：$\{x,\alpha\},\{\bar x,\alpha,\beta\} \to \{\alpha,\beta\}$

  例：选择$C_2=\{\bar r,d\}$，得到：$\{\bar a,\bar g,\bar r,\bar c\}$

</div>

<div class=rdiv>

- 递归最小化(Recursive Minimization)

  - 在较低决策等级上继续进行蕴含图的遍历
  - 目的：找到能被学习子句中其他变量直接推导出的变量，标记为冗余。

  ![image-20240313161443669](./_Handbook%20of%20SAT_4_CDCL%20SAT求解器.assets/image-20240313161443669.png)

</div>

### 学习子句的质量

文字块距离（LBD）：将子句变量按照决策等级分组，组的数量称为文字块距离。
$$
LBD(\{{}^1\bar a,{}^2\bar g,{}^2\bar d,{}^2\bar r,{}^4\bar c\})=3
$$
LBD较小，倾向于保留；LDB较大，倾向于删除。

LBD较大，可以进行学习子句最小化的操作。

结论：FUIP得到子句的LDB不大于当次冲突分析中任意UIP得到子句的LDB。

## 搜索重启(Search Restart)

保留当前学习到的子句，重新开始搜索。

目的：平衡探索和利用(Exploration and Exploitation)（强化学习的思想）

- 合适的重启策略

  包括重启时机，重启频率，重启后的行为，是一系列的超参数

- 保证算法完备性

- 阶段节省(Phase Saving)：考虑变量的赋值偏好

## 冲突驱动分支(Conflict-Driven Branching)

分支：选择赋值变量。

变量状态独立衰减和(VSIDS)：

- 每个变量给予一个计数器
- 每当学习子句时：对所有驱动该子句生成的变量，计数器增加
- 每当选择赋值变量时：选择未赋值且计数器最大的变量
- 计数器定期衰减：同时除以一个常数

## 惰性数据结构

监视文字(Watched Literal)：

- 对于每个子句，设置**2个**监视引用(Watch Reference)：两个指针
- 当且仅当监视文字被赋值为假时：寻找另一个未被赋值的文字进行更新，如果找不到，则另一个监视文字满足进行单位传播的条件。
- 其余情况均不更新监视引用：
  - 给除监视变量外的变量赋值
  - 监视文字赋值为真
  - 回溯

## 总结

<!-- _class: cols-2 -->

<div class=ldiv>

现代CDCL SAT求解器共同特点：

- 冲突驱动子句学习
- 搜索重启（阶段节省）
- 冲突驱动分支（变量选取）
- 惰性数据结构
- 文字块距离

</div>

<div class=rdiv>

其他技术：

- 预处理和内处理(Inprocessing)
- 机器学习优化分支启发策略
- 新的重启策略
- 优化的子句最小化技术
- 用于处理xor的高斯消元

</div>
