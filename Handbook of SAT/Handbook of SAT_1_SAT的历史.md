---
marp: true
theme: am_yoimiya
paginate: true
headingDivider: [2,3]
footer: \ *石晋* *2024.03.29*
math: mathjax
---

<!-- _class: cover_a-->
<!-- _paginate: "" -->
<!-- _footer: "" -->

# 第1章 SAT的历史

Handbook of Satisfiability (2nd ed.), Chapter 1

## 前言：可满足性的概念

可满足性源于逻辑学

逻辑：有效性(Validity)和一致性(Consistency)。

使用否定($\neg$)来联系两者：从$\{p_1,...,p_n\}$到$\{q\}$的论证是**有效**的当且仅当$\{p_1,...,p_n,\neg q\}$是**不一致**的。

有效性和一致性是看待同一事物的两种方式，都可以通过句法(Syntax)和语义(Semantics)来表达。

### 句法

句法产生了证明论(Proof Theory)，针对公理系统的推导。（欧氏几何）

- 有效性：可推导性(Derivability)：由句法定义$\{p_1,...,p_n\}\vdash\{q\}$​
- 一致性：$\{p_1,...,p_n\}$是**一致**的，当且仅当，**无法**推导出矛盾$\{q\land \neg q\}$​

### 语义

语义产生了模型论(Model Theory)，主要研究句子和“世界”的关系。

- 真理：“与世界相对应”
- 代数结构：$A=\langle D,R \rangle$​，D代表世界W中非空对象的集合，R代表一种映射（解释）
- 句子p在W中为真：句子p的解释在世界W中符合。
- 句子p在A中为真：$A \models p$
- 有效性：从$\{p_1,...,p_n\}$到$\{q\}$的论证是**有效**的（记作$p_1,...,p_n \models q$），当且仅当，对于所有代数结构A，若$A \models p_1,...,A \models p_n$，则$A \models q$成立。
- 一致性：可满足性(Satisfiability)：$\{p_1,...,p_n\}$是**可满足**的，当且仅当，存在代数结构A，使得$A \models p_1,...,A \models p_n$​成立。

在完备的公理系统中，句法和语义是一致的。

## 亚里士多德和三段论（公元前4世纪）

主语(S)+谓语(P)+四种命题：“与世界相对应”

- 普遍肯定(A)：所有S是P为真
- 普遍否定(E)：没有S是P为真
- 特殊肯定(I)：一些S是P为真
- 特殊否定(O)：一些S不是P为真​

古典时期假设：逻辑是双值的，不能包含空集

- A和E不能同时为真，I和O不能同时为假

### 三段论

三段论：形如$(p\land q)\to r$且$p,q,r$是A，E，I，O命题
$$
(一些M是A\land一些C是A)\to 所有M是C
$$
如何反驳？以现实为例
$$
\{一些M是A,一些C是A,\neg (所有M是C)\}
$$

### 三段论推理

将$(p_1\land ...\land p_n)\to q$转化为三段论$(p_1\land p_2)\to r_1,(r_1\land p_3)\to r_2,...,(r_{n-2}\land p_n)\to q$​

- 公理1：$(所有X是Y\land所有Y是Z)\to 所有X是Z$
- 公理2：$(所有X是Y\land没有Y是Z)\to 没有X是Z$

- 规则1：$(p\land q)\to r$**推出**$(\neg r \land q)\to \neg p$
- 规则2：$(p\land q)\to r$**推出**$(q\land p)\to r$
- 规则3：没有X是Y**推出**没有Y是X
- 规则4：$(p\land q)\to 没有X是Y$**推出**$(p\land q)\to 一些X不是Y$

$$
(所有P是M\land没有S是M)\to 一些S不是P
$$

- 公理2：$(所有P是M\land没有M是S)\to 没有P是S$
- 规则3：$(所有P是M\land没有S是M)\to 没有S是P$​
- 规则4：$(所有P是M\land没有S是M)\to 一些S不是P$

### 斯多葛(Stoics)学派（公元前3世纪）

- 使用符号$\neg,\land,\lor,\to$表达命题
- 命题推理规则：
  - 前提推理(Modus Ponens)：$p,p\to q\models q$
  - 否定推理(Modus Tollens)：$\neg q,p \to q \models \neg p$
  - 选言推理(Disjunctive Syllogism)：$\neg q,p\lor q\models p$​
  - 假设推理(Hypothetical Syllogism)：$p\to q,q\to r\models p\to r$​

## 过渡时期

中世纪：

- 更复杂的语法
- 集合论的雏形
- 使用图形理解逻辑

文艺复兴：莱布尼兹，引入“部分”符号$\preceq$，“实加法”符号$\oplus$和“恒等式”符号$=$

- 幂等律：$t\oplus t=t$
- 交换律：$t\oplus t'=t'\oplus t$
- 结合律：$t\oplus(t'\oplus t'')=(t\oplus t')\oplus t''$
- 部分：$t\preceq t'$当且仅当$t\oplus t'=t'$

## 乔治布尔和布尔代数（19世纪）

布尔代数：$\langle B,\lor,\land,\neg,0,1 \rangle$

- $\lor,\land$是二元运算符，$\neg$是一元运算符，B是封闭的，$0,1 \in B$​

- $$
  \begin{align}
  x\land y &= y\land x & 1\land x &= x \\
  x\lor y &= y\lor x & 0\lor x &= x \\
  x\lor(y\land z) &= (x\lor y)\land(x\lor z) & x\land \neg x &= 0 \\
  x\land(y\lor z) &= (x\land y)\lor(x\land z) & x\lor \neg x &= 1
  \end{align}
  $$

- 集合运算符：补集：$-$，并集：$+$，交集：$\cdot$

韦恩(Venn)图：一种与布尔代数完美同构的图解方法。

### 三段论、布尔代数、集合

| 三段论     | 布尔代数                | 集合                         |
| ---------- | ----------------------- | ---------------------------- |
| 所有x是y   | $x=x\cdot y$            | $x \subseteq y$              |
| 没有x是y   | $0=x\cdot y$            | $x \subseteq y$              |
| 一些x是y   | $V=V\cdot x\cdot y$     | $x \cap y\neq\emptyset$      |
| 一些x不是y | $V=V\cdot x\cdot (1-y)$ | $x \cap \bar y\neq\emptyset$ |

V代表x和y相交形成的非空子集。

## 弗雷德和罗素悖论

弗雷德在《算数原理》中提出：可以使用由五个基本逻辑定律、几个量词定律和几个函数公理组成的“逻辑公理”推导出集合论，数论，分析中的所有定理。

罗素悖论：$x=\{y|y\notin x\}$

罗素与怀特海德在《数学原理》中提出类型理论：规定对象只能与相同或更高类型的对象进行关联，避免了集合的自我包含。

## 哥德尔不完备定理

逻辑主义（弗雷德、罗素、希尔伯特）：数学可以从有限的公理中推导出来（完备）

哥德尔不完备定理：能够制定算数定律的公理系统一定是不完备的

- 自指悖论（罗素悖论，哥德尔不完备定理，停机问题）

- 一阶逻辑的完备性

## 香农和逻辑电路

布尔函数的展开：
$$
f(x_1,x_2,...,x_n)=x_1\cdot f(1,x_2,...,x_n)+x_1'\cdot f(0,x_2,...,x_n)
$$

$$
\begin{align}
f(x_1,x_2,...,x_n)=&f(0,0,...,0)\cdot x_1' \cdot  x_2'\cdot ... \cdot x_n' + \\
&f(1,0,...,0)\cdot x_1 \cdot  x_2'\cdot ... \cdot x_n'+ \\
&f(0,1,...,0)\cdot x_1' \cdot  x_2\cdot ... \cdot x_n'+ \\
&... \\
&f(1,1,...,1)\cdot x_1 \cdot  x_2\cdot ... \cdot x_n
\end{align}
$$

简化操作：

- $x = x+x=x+x+x=...$
- $x+x*y=x$
- $x\cdot f(x) = x\cdot f(1)$​
- $x'\cdot f(x) = x'\cdot f(0)$
- $x\cdot y+x'\cdot z=x\cdot y+x'\cdot z+y\cdot z$

## 解析和DPLL算法

前提：

- 一阶逻辑具有完备性
- 逻辑电路-计算机

希望自动证明一阶逻辑中的定理

- Davis-Putman Procedure
  - $\{A,B,\neg C\},\{\neg A,D\} \to \{B,\neg C,D\}$
- Loveland和Logemann对DPP进行改进：DPLL
  - 赋值操作
- Tseitin扩展解析：将复杂公式化简
  - $z \Leftrightarrow a\lor b$
  - $(z\lor \neg a)\land(z\lor \neg b)\land(\neg z \lor a \lor b)$

## 解析的复杂性

- 树解析：从现有子句推出新子句
- 序列解析：从一个子句推出新子句

树解析效率低下，存在冗余

基于DPLL框架的SAT求解器存在类似的低效率和冗余

## 基于解析的SAT求解器改进

变量选择/赋值选择：

- 最短子句
- 选择正负文字差异最大的变量
- 贪婪启发
- 满足更多的子句（可满足友好）
- 正负值搜索空间大小类似（不可满足友好）
- 变量赋值活动（VSIDS）

前瞻（Lookahead）：同时尝试几个变量的赋值，选择最优的继续

深度优先前瞻：重启

**子句学习**

## 特殊SAT问题

- 2-SAT
- Horn：每个子句最多含有一个正文字
  - $(\neg v_1\lor \neg v_2\lor v)$​代表$(v_1 \land v_2 \to v)$或$(v_1\to v_2 \to v)$​
- 线性规划相关：CNF可以转化为$(0,\pm)$矩阵—整数规划—放宽为线性规划问题
- SLUR：包含Horn，Renameable Horn，Extended Horn，CC-balanced等结构的**算法**：可以在多项式时间内判断SAT问题是否符合其中包含的类

---

- q-Horn：通过列乘-1，行和列重新排列
  - 东北象限：全为0
  - 西北象限：Horn表达式
  - 东南象限：2-SAT表达式
  - 西南象限：没有正文字

- 匹配：二分图$G_\phi=(V_c,V_v,E)$，存在完全匹配
- k-BRLR：每个变量的正负文字出现总和不超过k次

## 二元决策图

<!-- _class: cols-2 -->

<div class=limg>

![#c h:300](./_Handbook%20of%20SAT_1_SAT%E7%9A%84%E5%8E%86%E5%8F%B2.assets/121186-20180807171805826-1825144986.png)

</div>

<div class=rimg>

![#c h:300](./_Handbook%20of%20SAT_1_SAT%E7%9A%84%E5%8E%86%E5%8F%B2.assets/121186-20180807171818053-952209766.png)

</div>

## 随机局部搜索(Stochastic Local Search)

SLS是解决困难组合问题最成功和最广泛使用的通用策略之一

- 模拟退火
- 进化算法

应用于SAT问题的SLS算法：

- GSAT算法（90年代比DPLL更快）
  - 遗传算法（适应度评估—选择—交叉—变异）
- GSAT+子句权重
- GSAT+随机游走
- SAPS算法：基于子问题分割求解
- DDFW算法：变量之间的动态依赖

## MAX-SAT

MAX-SAT：满足最多的子句数量

- 分支定界法
- 转化为SAT问题

MAX-2-SAT：每个子句最多两个文字

## 总结

没讲的部分：

- 基于概率的SAT分析（1.21，1.22）
- 非线性公式（1.25）
- 伪布尔形式（1.26）
- 量化布尔公式（1.27）
