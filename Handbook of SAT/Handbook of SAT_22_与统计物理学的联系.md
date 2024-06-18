---
marp: true
theme: am_yoimiya
paginate: true
headingDivider: [2,3]
footer: \ *石晋* *2024.04.24*
math: mathjax
---

<!-- _class: cover_a-->
<!-- _paginate: "" -->
<!-- _footer: "" -->

# 第22章 与统计物理学的联系

Handbook of Satisfiability (2nd ed.), Chapter 22

## 简介

随机k-SAT问题随密度产生的求解难度变化与**相变**这一物理概念具有相似的性质。

相变(Phase Transitions)：当某个参数在某个阈值附近变动时，能够对系统性质产生极大的影响

22.2与22.3两节介绍了相变这一物理概念及其与随机SAT问题的联系（略）

## 连续感知机问题

高维空间（$\mathbb{R}^N$）中的二分类问题：对于两类向量$\vec T_i,\vec S_i$，寻找一个权重向量$\vec\sigma$，使得
$$
\begin{align}
\vec\sigma \cdot \vec T_i&>0\\
\vec\sigma \cdot \vec S_i&<0
\end{align}
$$
可分性：对于$\mathbb{R}^N$中随机的M个向量$\vec T_i$，存在$\vec\sigma$，使得$\vec\sigma \cdot \vec T_i>0$的概率
$$
P(N,M)=\frac{1}{2^{M-1}}\sum_{i=0}^{min(N-1,M-1)}\binom{M-1}{i}
$$

---

<!-- _class: cols-2 -->

<div class=limg>

![#c](./_Handbook%20of%20SAT_22_%E4%B8%8E%E7%BB%9F%E8%AE%A1%E7%89%A9%E7%90%86%E5%AD%A6%E7%9A%84%E8%81%94%E7%B3%BB.assets/image-20240424160012213-1713969739200-1.png)

</div>

<div class=rimg>

![#c](./_Handbook%20of%20SAT_22_%E4%B8%8E%E7%BB%9F%E8%AE%A1%E7%89%A9%E7%90%86%E5%AD%A6%E7%9A%84%E8%81%94%E7%B3%BB.assets/image-20240424155932414-1713969749622-3.png)

</div>

## 因子图

特例（树）：
$$
(x_1\lor\bar x_2\lor x_3)\land(\bar x_3\lor x_4\lor\bar x_5)\land(x_4\lor x_6\lor x_7)
$$
![#c h:350](./_Handbook%20of%20SAT_22_%E4%B8%8E%E7%BB%9F%E8%AE%A1%E7%89%A9%E7%90%86%E5%AD%A6%E7%9A%84%E8%81%94%E7%B3%BB.assets/image-20240424162141310.png)

---

- 子句：$a,b,...$；变量：$i,j,...$
- $\partial a$​：代表子句相邻变量的集合
- $\partial i$：代表变量相邻子句的集合
- $\partial_+i(a)$：代表$\partial i\backslash a$中与$a$中$i$​符号相同的子句集合
  - 支持子句$a$中变量$i$为正的子句
- $\partial_-i(a)$：代表$\partial i\backslash a$中与$a$中$i$符号不同的子句集合
  - 支持子句$a$中变量$i$为负的子句
- $\partial_\sigma i$：代表$\partial i$中在赋值$\sigma_i=\sigma$​下满足的子句集合

## 信息传递

$$
\begin{align}
h_{i\to a}&=\sum_{b\in\partial_+i(a)}u_{b\to i}-\sum_{b\in\partial_-i(a)}u_{b\to i} \\
u_{a\to i}&=-\frac{1}{2}\ln\left(1-\prod_{j\in \partial a\backslash i}\frac{1-\tanh h_{j\to a}}{2}\right) \\
&\tanh(x)= \frac{e^x-e^{-x}}{e^x+e^{-x}}  
\end{align}
$$
![#c h:250](./_Handbook%20of%20SAT_22_%E4%B8%8E%E7%BB%9F%E8%AE%A1%E7%89%A9%E7%90%86%E5%AD%A6%E7%9A%84%E8%81%94%E7%B3%BB.assets/image-20240424203209437.png)

## 信息利用

$$
\begin{align}
\mu(\sigma_i)&=\frac{1+\sigma_i\tanh h_i}{2}\\
h_i &=\sum_{a\in\partial_+i}u_{a\to i}-\sum_{a\in\partial_-i}u_{a\to i}
\end{align}
$$

## 调查传播算法

原先的信息传播算法在循环图的情况下可能不收敛，进行改进

信息传递：
$$
\begin{align}
\delta_{a\to i}=&\prod_{j\in\partial a\backslash i}\gamma_{j\to a} \\
\gamma_{i\to a}=&\frac{(1-\pi_{i\to a}^-)\pi_{i\to a}^+}{\pi_{i\to a}^++\pi_{i\to a}^--\pi_{i\to a}^+\pi_{i\to a}^-} \\
&with\begin{cases}
\pi_{i\to a}^+=\prod_{b\in\partial_+i(a)}(1-\delta_{b\to i}) \\
\pi_{i\to a}^-=\prod_{b\in\partial_-i(a)}(1-\delta_{b\to i})
\end{cases}
\end{align}
$$

---

信息利用：
$$
\begin{align}
\gamma_i^+=&\frac{(1-\pi_i^+)\pi_i^-}{\pi_i^++\pi_i^--\pi_i^+\pi_i^-},\quad\gamma_i^-=\frac{(1-\pi_i^-)\pi_i^+}{\pi_i^++\pi_i^--\pi_i^+\pi_i^-},\quad \gamma_i^0 = 1-\gamma_i^+-\gamma_i^-\\
&with\begin{cases}
\pi_i^+=\prod_{a\in\partial_+i}(1-\delta_{a\to i}) \\
\pi_i^-=\prod_{a\in\partial_-i}(1-\delta_{a\to i})
\end{cases}
\end{align}
$$
