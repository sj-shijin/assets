---
marp: true
theme: am_yoimiya
paginate: true
headingDivider: [2,3]
footer: \ *石晋* *2024.04.23*
math: mathjax
---

<!-- _class: cover_a-->
<!-- _paginate: "" -->
<!-- _footer: "" -->

# 第9章 预处理

Handbook of Satisfiability (2nd ed.), Chapter 9

## 简介

其他领域中的问题：自动编码生成SAT问题，再使用SAT求解器求解。

冗余：自动编码会产生冗余的变量或子句

预处理技术：消除冗余（重新编码），提高求解效率（推理）

内处理技术：对CDCL的生成子句应用预处理技术

冗余不一定是坏事：CDCL求解器一定程度上利用了冗余

---

- 基于解析及其变体
  - 单位传播
  - 有界变量消除
  - 超二元解析
  - 高级技术（蒸馏、活化）
- 冗余检测
  - 字句包含
  - 隐藏文字消除
  - 等价文字替换
  - 阻塞子句消除（及推广）

## 经典预处理技术

- 单位传播
  $$
  \begin{align}
  &(x)\land(\bar x\lor y)\land(\bar y\lor z\lor v) \\
  \Rightarrow &(z\lor v)
  \end{align}
  $$
  
- （前瞻）失败文字检测
  $$
  \begin{align}
  &(x\lor u)\land(\bar x\lor u)\land(\bar u \lor y\lor z)\land(\bar u \lor \bar y\lor \bar z) \\
  \Rightarrow &(y\lor z)\land(\bar y\lor  \bar z)
  \end{align}
  $$

- 纯文字检测
  $$
  \begin{align}
  &(\bar x \lor y \lor z)\land(\bar y\lor \bar z) \\
  \Rightarrow &(\bar y\lor \bar z)
  \end{align}
  $$

---

- 包含：$(\bar x \lor z)$包含$(\bar x \lor y \lor z)$
  $$
  \begin{align}
  &(\bar x \lor y \lor z)\land(\bar x \lor z) \\
  \Rightarrow &(\bar x \lor z)
  \end{align}
  $$

  - 正向包含（当前子句是否被其他子句包含）：将某个子句的所有变量全部临时赋值为假，使用单监视文字判断

  - **反向包含**（当前子句是否包含其他子句）：维护每个文字在子句中的出现列表

  - 自包含解析：
    $$
    \begin{align}
    &(\bar x \lor y \lor z \lor u)\land(\bar x \lor z\lor \bar u) \\
    \Rightarrow &(\bar x \lor y \lor z)\land(\bar x \lor z\lor \bar u)
    \end{align}
    $$

---

- 连通块：超图(Hypergraph)：变量为顶点，子句为超边(Hyperedge)
  $$
  (x\lor y\lor \bar z)\land(\bar y\lor z)\land(u \lor \bar v)\land(v\lor \bar w)
  $$
  ![#c h:200](./_Handbook%20of%20SAT_9_%E9%A2%84%E5%A4%84%E7%90%86.assets/image-20240418234243898.png)

## 基于解析的预处理

- 有界变量消除：对某个变量执行所有可以进行的解析，并删除所有带有该变量的子句。
  $$
  \begin{align}
  &F = (x\lor e)\land(y\lor e)\land(\bar x\lor z\lor \bar e)\land(y\lor \bar e)\land(y\lor z) \\
  \Rightarrow &F\land(x\lor\bar x\lor z)\land(x\lor y)\land(y\lor\bar x\lor z)\land(y) \\
  \Rightarrow &(y\lor z)\land(x\lor\bar x\lor z)\land(x\lor y)\land(y\lor\bar x\lor z)\land(y)
  \end{align}
  $$
  问题：子句数量增长速度为平方级

  - 选择正/负文字较少的变量
  - 结合重言式检测、包含检测等

---

- 等价文字替换：二元蕴含图中的**强连通分量**
  $$
  \begin{align}
  &(\bar x\lor y)\land(\bar y\lor z)\land(\bar z\lor u)\land(\bar u\lor y)\land(\bar x\lor\bar z)\land(x\lor z\lor u) \\
  \Rightarrow &(\bar x\lor y)\land(\bar x\lor\bar y)\land(x\lor y)
  \end{align}
  $$
  ![#c h:200](./_Handbook%20of%20SAT_9_%E9%A2%84%E5%A4%84%E7%90%86.assets/image-20240419184455909.png)

  冲突检测：存在正文字到负文字的路径

---

- 超二元解析：单位传播的推广
  $$
  \begin{align}
  &(x\lor y)\land(\bar x\lor y)\land(x\lor\bar y)\land(\bar x\lor\bar y) \\
  \Rightarrow &(y)\land(x\lor\bar y)\land(\bar x\lor\bar y)
  \end{align}
  $$
  定义：
  $$
  \begin{align}
  &(l_1\lor...\lor l_n)\land(\bar l_1 \lor l)\land...\land(\bar l_{n-1}\lor l)\\
  \Rightarrow &(l_n\lor l)
  \end{align}
  $$

---

- 现代探测技术：

  非对称文字：若$F \land \bar C$可以通过单位传播推出$\bar l$，则$C'=C\lor l$中的$l$被称为非对称文字
  $$
  \begin{align}
  F &=(a\lor\bar x)\land(x\lor \bar l)\\
  C &=(a\lor b)\\
  F\land \bar C &= F\land(\bar a)\land(\bar b) \\
  F\land C'&\Rightarrow F\land C
  \end{align}
  $$
  以上操作被称为**非对称文字消除**，但为了寻找冗余，考虑用这种方法**添加文字**

  非对称重言AT（隐藏重言消除HTE）：
  $$
  \begin{align}
  F &=(\bar x\lor z)\land(\bar y\lor \bar z)\\
  C &=(\bar x\lor\bar y)\\
  C' &=(\bar x\lor\bar y \lor\bar z) \\
  C'' &=(\bar x\lor\bar y \lor\bar z\lor z)
  \end{align}
  $$

---

反向单位传播RUP：$F\land\bar C$会产生冲突

基本方法：将C中的文字设置为false再进行单位传播

活化(Distillation)：每当一个文字设置为false，进行一次单位传播

- C中如果有文字被设置为真，则该子句为非对称重言，删除或替换为学习子句
- C中如果有文字被设置为假，则该文字为非对称文字，可以删除该文字

蒸馏(Vivification)：使用trie减少冗余

## 可满足性等价的预处理

- 阻塞子句：子句C中某个文字的所有解析都为重言式
  $$
  \begin{align}
  &F = (\bar x \lor z)\land(\bar y\lor \bar x)\\
  &C = (x\lor y)
  \end{align}
  $$
  $x$没有阻塞C，$y$阻塞C，删除C不影响可满足性。

  - 解析非对称重言RAT：子句C中的一个文字l的所有解析都为非对称重言式

- 覆盖文字：子句C中某个文字的所有解析中的非重言式 都包含某个文字k，则被称为覆盖文字
  $$
  \begin{align}
  F &=(\bar x\lor y)\land(\bar y\lor z)\\
  C &=(x\lor\bar z)\\
  C' &=(x\lor\bar z\lor y)
  \end{align}
  $$

## 模型重建

在预处理（模型简化）的过程中，可能会消除部分变量（有界变量消除等）或删除部分子句（阻塞子句等）

需求：当最终判断SAT可满足时，需要反推到原始模型

方法：尝试-反转

可参考[内处理](https://shijin.space/blog/2024/03/28/ijcai_2012_inprocessing/)论文

## 简单类

- 平凡可满足公式：每个公式至少有一个正/负文字
- Horn公式：每个公式至多有一个正文字
  - 证明：如果存在一个单位子句，进行赋值并单位传播，直到不存在单位子句。此时若产生空子句，则不满足；否则符合平凡可满足公式。
  - dual-Horn公式：每个公式至多有一个负文字
- 2-CNF公式：每个公式有且仅有两个文字
  - 方法1：转换为检测图上是否存在$x\to \neg x$的路径
  - 方法2：随机游走
  - 方法3：解析，2-CNF解析仅能产生2-CNF，且2-CNF的总数为$O(n^2)$
- 仿射公式（XOR公式、奇偶校验）：$x_1\oplus ... \oplus x_k=\delta$转换为$2^{k-1}$个子句
  $$
  \begin{align}
  &l_1\oplus l_2\oplus l_3 = 1\\
  \Rightarrow &(l_1\lor l_2\lor l_3)\land(\bar l_1\lor\bar l_2\lor l_3)\land (l_1\lor\bar l_2\lor\bar l_3)\land(\bar l_1\lor l_2\lor\bar l_3)
  \end{align}
  $$
  - 高斯消元法
