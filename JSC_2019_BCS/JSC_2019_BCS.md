### 知识准备

$$
P = (x_4+x_1x_2x_3+x_1x_3)x_5+(x_2+1)x_3x_4+x_1x_2x_3+x_2+1
$$

- $lm(P)=x_1x_2x_3x_5$：单项式顺序的首项
- $tdeg(P)=4$​：$lm(P)$的次数
- $cls(P)=5$：最大的变量下标，类，$cls(constant)=0$
- $lvar(P)=x_{cls(P)}=x_5$：首项变量，主变元
- $init(P)=x_4+x_1x_2x_3+x_1x_3$​：首项系数，初式

$$
P=init(P)lvar(P)+U
$$

- $Monic(P)$：首一多项式，$init(P)=1$
- $Zero(P)$：方程$P=0$​的解集

### 三角集

$$
\mathcal{A}=\{A_1,A_2,...,A_r\}
$$

- $0<cls(A_1)<\cdots<cls(A_r)$或$r=1,A_1=1$
- $Monic(\mathcal{A})$：首一三角集，$A_1,...,A_r$是首一多项式
- $|Zero(\mathcal{A})|=2^{n-r}$：$\mathcal{A}$是首一三角集，$n$是变量数

### 零分解问题

输入：布尔多项式系统$\mathcal{P}$

输出：首一三角集集合$\{\mathcal{A_1},\mathcal{A_2},...,\mathcal{A_t}\}$，满足$Zero(\mathcal{P})=\bigcup_iZero(\mathcal{A_i})$，$Zero(\mathcal{A}_i)\cap Zero(\mathcal{A}_j)=\emptyset$

## BCS算法

```python
def BCS(P:多项式系统) -> 首一三角集集合:
    Ps, As = {P}, set()  # 多项式系统的集合
    while Ps:
        Q = Ps.pop()
        A, Qs = Triset(Q)  # 三角集和新多项式系统集合
        if A:
            As.add(A)
        Ps.update(Qs)
    return As
```

### Triset函数

```python
def Triset(P:多项式系统) -> 三角集, 多项式系统集合:
    Qs, A, M = set(), set(), set()
    while P:
        while 线性多项式 in P:
            smpA, smpP, smpM = Simplify(P, M)
            if not smpA and not smpP and not smpM:
                return set(), Qs
            A.update(newA)
            MP = {poly for poly in P if Monic(P)}
            P, M = newP - MP, newM | MP
            if M:
                rdcM, rdcP = AddReduce(M)
                if not rdcM:
                    return set(), Qs
                P.update(rdcP)
                M = rdcM
        if not P:
```

