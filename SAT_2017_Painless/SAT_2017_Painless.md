---
marp: true
theme: am_yoimiya
paginate: true
headingDivider: [2,3]
footer: \ *石晋* *2024.05.26*
math: mathjax
---

<!-- _class: cover_a-->
<!-- _paginate: "" -->
<!-- _footer: "" -->

# PaInleSS: A Framework for Parallel SAT Solving

SAT 2017

## 并行SAT求解策略

- 基于竞争(Portfolio)
  - 多个顺序求解器同时求解完整问题

- 基于合作(Divide-and-Conquer)
  - 先将问题进行划分为子问题，再使用顺序求解器求解子问题

### 竞争(Portfolio)

关注：多样性(Diversification)

- 使用不同的启发式方法（决策策略、学习策略、随机种子等）

- 使用变量预赋值(Phase)进行软划分

- 使用变量块(Block Branching)进行硬划分：决策变量只能从所属的块中选择

### 合作(Divide-and-Conquer)

关注：搜索空间划分和线程负载均衡

- 搜索空间划分
  - 香农分解：又称引导路径(Guiding Path)，假设一系列变量的值，分解为互不相交的子问题。
  - 分解变量的选择是一个启发式的难题

- 线程负载均衡：任务窃取(Work Stealing)

### 子句共享

- 共享标准
  - 子句的活跃性、长度、LBD
  - 静态、动态阈值

- 共享范围
  - 所有线程、部分线程

## Painless并行框架

<!-- _class: fixedtitleA -->

![#c](./_SAT_2017_Painless.assets/image-20240525210007060.png)

### 顺序求解模块(Sequential Engine)

<!-- _class: cols-2 -->

<div class = ldiv>

求解主体部分：

- `SatResult solve(int* cube)`

- `void setInterrupt()`

- `void setPhase(int var, bool value)`

- `void bumpVariableActivity(int var, int factor)`

- `void diversify()`：调整求解器内部参数

</div>

<div class = rdiv>

子句共享部分：

- `void addClause(Clause cls)`

- `void addLearntClause(Clause cls)`

- `Clause getLearntClause()`

</div>

### 并行化模块(Parallelization)

<!-- _class: fixedtitleA -->

![#c h:400](./_SAT_2017_Painless.assets/image-20240525212020581.png)

- `void solve(int* cube)`

- `void join(SatResult res, int* model)`

### 共享模块(Sharing)

<!-- _class: fixedtitleA -->

![#c h:450](./_SAT_2017_Painless.assets/image-20240525213100684.png)

- `void doSharing(SolverInterface[*] producers, SolverInterface[*] consumers)`

## 并行SAT求解策略(2017)

- Glucose-syrup(SC15并行\#1)：基于顺序求解器Glucose，基于竞争
  - 共享所有学习子句

- Treengeling(SC16并行\#1)：基于顺序求解器Lingeling，基于合作
  - Cube-and-Conquer：基于前瞻

- Hordesat：基于顺序求解器Minisat或Lingeling，基于竞争
  - 每个线程每秒导出1500个文字（子句长度之和）

## 实验结果

<!-- _class: fixedtitleA -->

![#c](./_SAT_2017_Painless.assets/image-20240525221758271.png)
