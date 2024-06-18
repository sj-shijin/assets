---
marp: true
theme: am_yoimiya
paginate: true
headingDivider: [2,3]
footer: \ *石晋* *2024.04.02*
math: mathjax
---

<!-- _class: cover_a-->
<!-- _paginate: "" -->
<!-- _footer: "" -->

# 第5章 基于前瞻的SAT求解器

Handbook of Satisfiability (2nd ed.), Chapter 5

## 简介

![#c](./_Handbook%20of%20SAT_5_Look-Ahead%20SAT求解器.assets/image-20240401135725790.png)

## 应用场景

- 密度(density)：子句数量/变量数量
  - 单位传播的代价
  - 密度越大，子句数量多，惰性数据结构具有优势
- 直径(diameter)：$d=max_{u,v\in V} \delta(u,v)$，其中$\delta(u,v)$代表最短路径长度
  - 解析图：点（子句），边（子句之间能进行解析）
  - 局部性好，子句学习具有优势

---

![#c h:600](./_Handbook%20of%20SAT_5_Look-Ahead%20SAT求解器.assets/image-20240401181421553.png)

## 前瞻(LookAhead)

![#c h:550](./_Handbook%20of%20SAT_5_Look-Ahead%20SAT求解器.assets/image-20240401191259374.png)

## 启发

- 差异启发：前瞻变量的效果，选出$x_{decision}$
- 方向启发：决策变量的赋值选择，$x_{decision}=0$或$x_{decision}=1$
- 预选启发：前瞻变量的选择

## 差异启发(Diff)

差异基于：$F[x_i=1]\backslash F$

$\gamma_k$：大小为k的子句的重要性

$F_k$​​：大小为k的子句子集
$$
F_{LA}=
\begin{align}
&(\neg x_1 \lor \neg x_2 \lor \neg x_7)\land
&&(\neg x_1 \lor x_2 \lor \neg x_6)\land
&&(\neg x_1 \lor \neg x_3 \lor \neg x_4)\land \\
&(\neg x_1 \lor \neg x_3 \lor x_4)\land
&&(x_1 \lor x_2 \lor x_6)\land
&&(x_1 \lor x_2 \lor x_7)\land \\
&(x_1 \lor \neg x_3 \lor \neg x_4)\land
&&(x_1 \lor \neg x_3 \lor x_4)\land
&&(\neg x_2 \lor x_3 \lor \neg x_5)\land \\
&(\neg x_2 \lor x_3 \lor x_5)\land
&&(x_2 \lor x_3 \lor \neg x_4)\land
&&(x_2 \lor x_4 \lor \neg x_5)\land \\
&(x_2 \lor x_4 \lor x_5)
\end{align}
$$

$$
F[x_1=1]\backslash F= \left\{
\begin{align}
&(\neg x_2 \lor \neg x_7),(x_2 \lor \neg x_6),\\
&(\neg x_3\lor \neg x_4),(\neg x_3\lor x_4)
\end{align}
\right\}
$$

---

- 子句约简启发(Clause Reduction Heuristic)
  $$
  CRH(x_i)=\sum_{k\geq 2} \gamma_k\cdot|F[x_i=1]_k\backslash F|
  $$

  $$
  \gamma_2=1,\gamma_3=0.2,\gamma_4=0.05,\gamma_5=0.01,\gamma_6=0.003,\gamma_k=20.4514\cdot 0.218673^k (k\geq 7)
  $$

- 加权二元启发(Weight Binaries Heuristic)

  - 关注二元子句$x\lor y$和能与之进行解析的子句数量$\#(\neg x)+\#(\neg y)$

  $$
  WBH(x_i)=\sum_{(x\lor y)\in F[x_i=1]_2\backslash F}(\omega_{WBH}(\neg x)+\omega_{WBH}(\neg y))
  $$

  $$
  \omega_{WBH}(x_i)=\sum_{k\geq 2}\gamma_k\cdot \#_k(x_i)\qquad \gamma_k=5^{3-k}
  $$

---

- 主干搜索启发(Backbone Search Heuristic)
  $$
  BSH(x_i)=\sum_{(x\lor y)\in F[x_i=1]_2\backslash F}(\omega_{BSH}(\neg x)\cdot\omega_{BSH}(\neg y))
  $$

  $$
  \omega_{BSH}(x_i)=\sum_{k\geq 2}\gamma_k\cdot \#_k(x_i)\qquad \gamma_k=2^{3-k}
  $$

- 主干搜索重规范化启发(Backbone Search Renormalized Heuristic)
  $$
  BSRH(x_i)=\sum_{C\in F[x_i=1]\backslash F}\left(\gamma_{|C|}\cdot\prod_{x\in C}\frac{\omega_{BSH}(\neg x)}{\mu_{BSH}(x_i)}\right)
  $$
  
  $$
  \mu_{BSH}=\frac{\sum_{C\in F[x_i=1]\backslash F}\sum_{x\in C}\omega_{BSH}(\neg x)}{\sum_{C\in F[x_i=1]\backslash F}|C|}
  $$

### 混合差异(MIXDIFF)

$MIXDIFF=DIFF(x_i)\cdot DIFF(\neg x_i)$​

![#c h:300](./_Handbook%20of%20SAT_5_Look-Ahead%20SAT%E6%B1%82%E8%A7%A3%E5%99%A8.assets/image-20240402195657893.png)
$$
F[x_1=1]\backslash F= \left\{
\begin{align}
&(\neg x_2 \lor \neg x_7),&&(x_2 \lor \neg x_6),\\
&(\neg x_3\lor \neg x_4),&&(\neg x_3\lor x_4)
\end{align}
\right\}
$$

- $WBH(x_1)=(6+1)+(3+1)+(3+4)+(3+3)=24$
- $BSH(x_1)=(6\times1)+(3\times1)+(3\times4)+(3\times3)=30$

## 方向启发

**如果**始终选择正确的赋值：无需回溯即可求解可满足的实例，P=NP

- 较少计算时间：较快遇到冲突（CDCL求解器中较多）
  - posit求解器：$F$的短子句中正文字多，$x=0$
- 较高可满足可能性
  - kcnfs求解器：$F$中正文字多，$x=1$
  - march求解器：$DIFF(x)<DIFF(\neg x)$，$x=1$​
  - OKsolver求解器：选择较小值$\sum_{k\geq 2}-|F_k|\cdot\ln(1-2^{-k})$​
- 混合
  - march_ks求解器：若$0.1 \leq \frac{DIFF(\neg x)}{DIFF(x)}\leq 10$，正常决策；否则反向决策

## 预选启发

收益：减少计算开销；损失：无法选择有效的变量

- $prop_z$

  - 二元子句出现次数
  - 深度较浅时不执行预选
  - 至少预选10个变量

- 子句约简近似(Clause Reduction Approximation)
  $$
  CRA(x)=\left(\sum_{(x\lor y_i)\in F}\#_{>2}(\neg y_i)\right)\cdot\left(\sum_{(\neg x\lor y_i)\in F}\#_{>2}(\neg y_i)\right)
  $$

  - $RANK_{10\%}$​

  - $$
    RANKADAPT_n=L+S\cdot\frac{\sum_{i=1}^{n}\#failed_i}{n}
    $$

## 局部学习

$$
F_{learning}=
\begin{align}
&(\neg x_1 \lor \neg x_2 \lor x_3)\land
&&(\neg x_1 \lor x_2)\land
&&(\neg x_1 \lor \neg x_3 \lor x_4)\land
&&(x_1 \lor \neg x_6)\land \\
&(x_1 \lor x_3 \lor x_6)\land
&&(x_1 \lor x_4 \lor \neg x_5)\land
&&(x_4 \lor x_5 \lor x_6)\land
&&(x_5 \lor \neg x_6)
\end{align}
$$

![#c h:200](./_Handbook%20of%20SAT_5_Look-Ahead%20SAT求解器.assets/image-20240403140343681.png)
$$
F_{learnt}= \left\{
\begin{align}
&(x_1 \lor x_3),
&&(\neg x_1 \lor x_3),
&&(\neg x_1\lor x_4),
&&(\neg x_2\lor \neg x_3), \\
&(x_2 \lor \neg x_6),
&&(x_5 \lor x_4),
&&(\neg x_6 \lor x_3),
&&(\neg x_6 \lor x_4)
\end{align}
\right\}
$$

- 必要赋值：$x_i$和$\neg x_i$的前瞻都对$x_j$​产生相同的赋值
  - $(x_1=0\to x_3=1)\land(x_1=1\to x_3=1)$
- 约束解析：**不是**通过二元+二元解析形成的子句
  - 除$(x_2\lor \neg x_6)$外都符合约束解析（实际中没有这么多）

## 自发(Autarkies)推理

目的：检测出可以直接赋值的变量，化简公式

- 纯文字：$F=(x_1\lor \neg x_2)\land(\neg x_1 \lor x_3)\land(\neg x_2\lor \neg x_3)$

- 差异启发中检测
  $$
  F_{autarky}=
  \begin{align}
  &(\neg x_1 \lor x_2 \lor \neg x_3)\land
  &&(\neg x_1 \lor x_5)\land
  &&(x_1 \lor \neg x_2)\land \\
  &(\neg x_2 \lor \neg x_3 \lor \neg x_4)\land
  &&(x_2 \lor x_4 \lor \neg x_5)\land
  &&(x_3\lor x_4)
  \end{align}
  $$

  |            | $x_1$ | $\neg x_1$ | $x_2$ | $\neg x_2$ | $x_3$ | $\neg x_3$ | $x_4$ | $\neg x_4$ | $x_5$ | $\neg x_5$ |
  | ---------- | :---: | :--------: | :---: | :--------: | :---: | :--------: | :---: | :--------: | :---: | :--------: |
  | $CRH(x_i)$ |   2   |     2      |   1   |     2      |   2   |     0      |   1   |     2      |   1   |     0      |

- 自发推理：前瞻后仅有一个子句未被满足

  $x_2$剩余：$\neg x_3 \lor \neg x_4$。（$x_4$：$\neg x_2 \lor \neg x_3$；$x_5$：$x_2 \lor x_4$）

  推理：$\neg x_3 \to x_2\quad(x_3\lor x_2)$和$\neg x_4 \to x_2\quad(x_4\lor x_2)$​

  - 前瞻的顺序会影响到DIFF启发的评分(WBH,BSH,CRA)

## 两次前瞻

条件：$|F[x=1]_2\backslash F|>\Delta_{trigger}(65,0.17num_{var},...)$

- 两次前瞻推理

  第一次：$x_i$

  第二次：第一次结果中所有自由变量，假设检测到失败文字$x_r$

  推理：$x_i \to \neg x_r\quad(\neg x_i\lor\neg x_r)$​

  - 两次前瞻会影响到DIFF启发的评分

## 急切数据结构（Eager Data-Structure）

目的：减少单位传播的成本

惰性数据结构不适合：无法快速得到约简后的新子句$F[x_i=1]\backslash F$

- 二元蕴含数组

  ![#c](./_Handbook%20of%20SAT_5_Look-Ahead%20SAT求解器.assets/image-20240403162315566.png)

  - 子句被分为$F_2\cup F_{>2}$​
  - 单位传播时优先进行二元子句

---

- 移除不活跃子句

  - 已经满足的子句

  - 转化为二元子句

- 基于树的前瞻

  冗余：$(x_1\lor \neg x_2)\land(x_1\lor \neg x_3)$，前瞻$x_1$被前瞻$x_2$和前瞻$x_3$所包含。

  ![#c h:350](./_Handbook%20of%20SAT_5_Look-Ahead%20SAT求解器.assets/image-20240403183428748.png)

## 总结

- 基于启发的完备算法
  - 差异
  - 方向
  - 预选
- 局部子句学习
- 急切数据结构
