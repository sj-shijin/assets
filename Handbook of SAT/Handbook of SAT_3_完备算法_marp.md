---
marp: true
theme: am_yoimiya
paginate: true
headingDivider: [2,3]
footer: \ *石晋* *2024.03.12*
math: mathjax
---

<!-- _class: cover_a-->
<!-- _paginate: "" -->
<!-- _footer: "" -->

# 第3章 完备算法(Complete Algorithms)

Handbook of Satisfiability (2nd ed.), Chapter 3

## 章节简介

完备算法：当程序运行终止时，能够对SAT问题的满足性做出正确判断的算法。

- 存在量化(Existential Quantification)

  在不改变满足性情况下，对变量进行消除。将复杂问题转化为简单测试。

- 可靠和完备的推理规则(Sound and Complete Inference Rule)

  使用推理规则，直到产生矛盾（空字句）。

- 真值赋值空间中的系统搜索(Systematic Search in the Space of Truth Assignments)

  相较于前两种方法占用空间较少。

## 知识准备

SAT问题的合取范式形式（CNF）：
$$
(A \lor B \lor \neg C)\land(\neg A \lor D)\land(B \lor C \lor D)
$$
简化形式：
$$
\Delta = \{\{A,B,\neg C\},\{\neg A,D\},\{B,C,D\}\}
$$
重要事实：若$\emptyset \in \Delta$，则SAT问题不可满足。

### 解析(Resolution)/变量消除

若$P \in C_i$且$\neg P \in C_j$，通过变量消除推理规则可以导出新子句$(C_i-\{P\})\cup(C_j-\{\neg P\})$。
$$
\{A,B,\neg C\},\{\neg A,D\} \to \{B,\neg C,D\}
$$
变量消除推理规则：

- 蕴含(Implication)
- 可靠但不完备
- 推理出的子句都是符合SAT系统的，但不是所有符合SAT系统的子句都可以被推出。
- 如果SAT系统是不可满足的，一定可以通过变量消除推理规则得到空字句。这也是证明SAT问题不可满足的主要方式。

### 约束(Conditioning)/变量赋值

对SAT系统$\Delta$中的变量（文字$L$）进行赋值：$\Delta|L$

- $L \in C_i$，该子句已经满足，从系统中删除该子句。
- $\neg L \in C_i$​，该文字对子句已经没有贡献，从子句中删除该文字。

$$
\begin{align}
\Delta =& \{\{A,B,\neg C\},\{\neg A,D\},\{B,C,D\}\} \\
\Delta|C =& \{\{A,B\},\{\neg A,D\}\} \\
\Delta|\neg C =& \{\{\neg A,D\},\{B,D\}\}
\end{align}
$$

## 存在量化

存在量化：$\exists P \Delta \overset{def}{=} (\Delta| P)\lor(\Delta|\neg P)$​，反复执行使得变量减少。

问题（非书中内容）：$\Delta| P$和$\Delta|\neg P$均为CNF形式，但$\exists P \Delta$不是，在复杂的情况下会产生大量子句。
$$
\begin{align}
\Delta =& \{\{\neg A,B\},\{\neg B,C\}\} \\
\Delta|B =& \{\{C\}\} \\
\Delta|\neg B =& \{\{\neg A\}\} \\
\exists B \Delta =& \{\{\neg A, C\}\}
\end{align}
$$

- DP(Davis-Putnam)算法
- 符号SAT求解

## 推理规则

- 解析/变量消除推理规则

- Stalmarck算法

  合取三元组：$p \Leftrightarrow (q \otimes r)$，其中$p$是变量，$q$和$r$是文字，$\otimes$可以是逻辑连词或等价词。
  $$
  \neg ((a \Leftrightarrow b \land c)\land(b \Leftrightarrow \neg c)\land a)
  $$

  $$
  \begin{align}
  v_1 \Leftrightarrow& (b \land c) \\
  v_2 \Leftrightarrow& (a \Leftrightarrow v_1) \\
  v_3 \Leftrightarrow& (b \Leftrightarrow \neg c) \\
  v_4 \Leftrightarrow& (v_3 \land a) \\
  v_5 \Leftrightarrow& (v_2 \land v_4) \\
  \end{align}
  $$

  此时$\neg v_5$代表了原始公式，通过设定一系列传播规则，期望从$\neg v_5=false$推导出矛盾。

- HeerHugo算法：总体来说与Stalmarck算法类似。

## 搜索算法

变量赋值+搜索回溯

![#c h:420](./_Handbook%20of%20SAT_3_完备算法.assets/简单搜索例子.png)

### 单位解析(Unit Resolution)/单位传播

$$
\Delta|A=\{\{B\},\{\neg B,\neg C\},\{C,\neg D\}\}
$$

单位传播：

- 如果存在单位子句($\{P\}$)，进行变量赋值：$\Delta|P$
- 如果存在单位子句($\{\neg P\}$)，进行变量赋值：$\Delta|\neg P$
- 重复进行以上操作

单位传播是以搜索为基础的SAT求解器中一个**非常重要**的组成部分。

DPLL算法：

- 变量赋值+搜索回溯
- 单位传播

## 搜索与推理结合

<!-- _class: cols-2-46 -->

<div class=ldiv>

DPLL算法的不足之处：时间顺序回溯（单步回溯），难以快速摆脱根源的错误。
$$
\Delta=\left\{
\begin{align}
1.&\{A,B\},\\
2.&\{B,C\},\\
3.&\{\neg A,\neg X,Y\},\\
4.&\{\neg A,X,Z\},\\
5.&\{\neg A,\neg Y,Z\},\\
6.&\{\neg A,X,\neg Z\},\\
7.&\{\neg A,\neg Y,\neg Z\}
\end{align}
\right\}
$$
</div>

![#c h:420](./_Handbook%20of%20SAT_3_完备算法.assets/搜索推理例子.png)

### 冲突集

推断过程中涉及到的变量设置为冲突集：

$$
\begin{align}
A=1,B=1,C=1,X=1 &\to Y=1(C_3),Z=1(C_5) \to \{\}(C_7) \\
\{A=1,X=1,&Y=1,Z=1\} \\
A=1,B=1,C=1,X=0 &\to Z=1(C_4) \to \{\}(C_6) \\
\{A=1,X=0,&Z=1\}
\end{align}
$$

经过两次冲突，发现当$A=1$时，X取真或假均不满足，故直接回溯到A（非时间顺序回溯）。

- 在选择变量赋值时，难以避免选择到与最终冲突无关的变量，用冲突集检查
- $\{\neg A,\neg X\},\{\neg A, X\}$都可以由初始子句进行变量消除得到，如果一开始就有这个子句，搜索过程将被简化，如何得到这个子句？是否还有其他有价值的子句？

### 蕴含图与独一蕴含点(UIP)

<!-- _class: cols-2-46 -->

<div class=ldiv>

独一蕴含点：从最新赋值的变量到冲突点的所有路径都经过的点，可能有多个。

首个独一蕴含点(First UIP/1UIP)：距离冲突点最近的独一蕴含点。

</div>

![#c h:360](./_Handbook%20of%20SAT_3_%E5%AE%8C%E5%A4%87%E7%AE%97%E6%B3%95.assets/蕴含图例子.png)

### 其他思考

- First UIP可能会出现重复搜索的情况：但算法依然完备
  $$
  \Delta = \{\{A,B\},\{\neg B,C\},\{\neg B,D\},\{\neg C,\neg D,E\},\{\neg E,F\},\{\neg E,\neg F\}\}
  $$
  ![#c h:180](./_Handbook%20of%20SAT_3_%E5%AE%8C%E5%A4%87%E7%AE%97%E6%B3%95.assets/1UIP重复例子.png)

- 冲突驱动子句过多：需要删除

  - 蕴含关系
  - 启发式方法（删除较长的、较旧的、较不活跃的）

- 重启：实验表明重启是有利的，但可能导致算法不完备

  - 冲突次数/赋值变量数量：目前搜索空间过大

## 总结

- 三类完备算法：

  - 存在量化
  - 逻辑推理
  - 系统搜索

- 现有主流算法：搜索+推理
- 认证(Certifying)：对于不满足的SAT问题，显式推理到一个空子句（以及其他变体）。
