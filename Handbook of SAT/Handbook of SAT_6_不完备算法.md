---
marp: true
theme: am_yoimiya
paginate: true
headingDivider: [2,3]
footer: \ *石晋* *2024.04.05*
math: mathjax
---

<!-- _class: cover_a-->
<!-- _paginate: "" -->
<!-- _footer: "" -->

# 第6章 不完备算法

Handbook of Satisfiability (2nd ed.), Chapter 6

## 简介

不完备算法：不能保证程序会结束或结束时输出正确的结果

结果：可满足的赋值或**失败**

随机本地搜索(SLS)：调整变量赋值，使得满足的子句最多

## 贪婪搜索(GSAT)

- 策略：反转使得**不满足的子句数最少**的变量

![#c](./_Handbook%20of%20SAT_6_%E4%B8%8D%E5%AE%8C%E5%A4%87%E7%AE%97%E6%B3%95.assets/image-20240404212852765.png)

## 随机游走(Walksat)

- 策略：若反转变量不会使得**已满足的子句变为不满足**，反转；否则：
  - 以概率$p$随机游走：反转随机变量
  - 以概率$1-p$贪婪：反转使得**已满足的子句变为不满足**数量最少的变量

![#c h:400](./_Handbook%20of%20SAT_6_%E4%B8%8D%E5%AE%8C%E5%A4%87%E7%AE%97%E6%B3%95.assets/image-20240404215602579.png)

## 基础算法扩展

- 子句权重
- 动态化参数（随机游走、字句权重）
- 基于离散拉格朗日的字句权重

本地搜索算法的评估方法：

- 深度(Depth)：能否**快速**满足较多的子句
- 机动性(Mobility)：能否**快速**转移搜索空间
- 覆盖率(Coverage)：对整个搜索空间的探索程度

## 离散拉格朗日方法

$$
\begin{align}
&min &&N(x)=\sum_{i=1}^mU_i(x)\\
&s.t. &&U_i(x)=0 &&\forall i\in \{1,2,...,m\}
\end{align}
$$

引入拉格朗日乘数：
$$
L_d(x,\lambda)=N(x)+\sum_{i=1}^m\lambda_iU_i(x)
$$
鞍点：$(x^*,\lambda^*)\in \{0,1\}^n\times \mathbb{R}^m$

- $x^*$局部最小，$\lambda^*$局部最大

$$
L_d(x^*,\lambda)\le L_d(x^*,\lambda^*)\le L_d(x,\lambda^*)
$$

结论：如果存在鞍点$(x^*,\lambda^*)$，那么$x^*$​是局部最小解。

---

差分梯度：$\Delta_xL_d(x,\lambda)\in \{-1,0,1\}^n$​，且最多一个非零元素

- 对于$x$的所有邻居$y’$（包括自身）
  $$
  y=x\oplus\Delta_xL_d(x,\lambda)\le y'
  $$

迭代算法：其中$c\in \mathbb{R}^+$
$$
\begin{align}
x(k+1)&=x(k)\oplus \Delta_xL_d(x(k),\lambda(k)) \\
\lambda(k+1)&=\lambda+c\cdot U(x(k))
\end{align}
$$
后续问题：对于一些问题（奇偶校验、汉诺塔），该方法会掉入陷阱(trap)

解决方法：将最近访问过的点作为惩罚项加入拉格朗日函数

## 随机k-SAT问题

每个子句：随机从n个变量中选择k个，正负概率0.5

衡量指标：密度（字句数量/变量数量）

问题难度：简单-困难-简单

![#c h:400](./_Handbook%20of%20SAT_6_%E4%B8%8D%E5%AE%8C%E5%A4%87%E7%AE%97%E6%B3%95.assets/图片2.png)

## 调查传播(Survey Propagation)

近线性时间内成功求解一百万变量的随机3-SAT问题

- 启发赋值算法，但几乎不出错

可参考[第22章](./Handbook%20of%20SAT_22_与统计物理学的联系.html)详细介绍
