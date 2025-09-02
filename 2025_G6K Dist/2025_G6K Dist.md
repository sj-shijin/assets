---
marp: false
theme: am_yoimiya
paginate: true
headingDivider: [2,3]
footer: \ *石晋* *2025.5.7*
math: mathjax
---

<!-- _class: cover_a -->
<!-- _paginate: "" -->
<!-- _footer: "" -->

# Scaling Lattice Sieves across Multiple Machines

Martin R. Albrecht, Joe Rowell

## 关键问题

- bucket如何实现？G6K保存索引显然不可取
- 去重如何实现？
- 分布式存储如何实现？

## logP

|                            |           |
| -------------------------- | --------- |
| 发送单字节消息的最大延迟   | $\lambda$ |
| 发送或接收单字节消息的开销 | $\phi$    |
| 两条连续消息之间的时间间隔 | $g$       |
| 网络中的节点数量           | $P$       |
| 最大中间跳转数             | $H$       |
| 每次跳转的转发时间         | $r$       |

- 发送单字节消息耗时：$2\cdot\phi+\lambda+H\cdot r$
- 发送 $M$ 字节的消息（分割为 $\sigma$ 份， $M\le \sigma \cdot \lceil \lambda/g\rceil $ ）：$\sigma \cdot (2\cdot\phi+M/\sigma\cdot(\lambda+g)+H\cdot r)$

## 非结构化bucketing(BGJ1)

<img src="./_2025_G6K%20Dist.assets/image-20250825123749114.png" alt="image-20250825123749114" style="zoom:33%;" />

|                          |      |
| ------------------------ | ---- |
| 总向量数量               | $N$  |
| 总节点数量               | $p$  |
| 每个节点的桶数量         | $q$  |
| 每个桶中向量数量         | $B$  |
| 每个向量的分量数（维数） | $n$  |
| 每个分量的字节数         | $c$  |

- 每个桶需要发送的向量数量：$B\cdot(p-1)/p$

- 通信时间：Line2每次循环传输$q\cdot n\cdot c$字节，分割为$\sigma_1$份；Line7每次循环传输$B/p\cdot q\cdot n\cdot c$字节，分割为$\sigma_2$份
  $$
  T_{comm}=(p-1)\cdot\big((\sigma_1+\sigma_2)\cdot(2\phi+H\cdot r)+(B/p+1)\cdot(q\cdot n\cdot c)\cdot(\lambda+g)\big)
  $$

- 计算时间：

  - 内积时间：$2\cdot n-1$
  - 分组时间：$N\cdot q\cdot (2\cdot n-1)$
  - 约化时间：$B^2\cdot q\cdot (2\cdot n-1)$

  $$
  T_{comp}=N\cdot q\cdot (2\cdot n-1)+B^2\cdot q\cdot (2\cdot n-1)
  $$

## 结构化bucketing(BDGJ)

$$
T_{comm}=(p-1)\cdot\big((1+\sigma_3)\cdot(2\phi+H\cdot r)+(N^{\frac{1}{t+1}}/p\cdot n\cdot c + d)\cdot(\lambda+g)\big)
$$

$$
T_{comp}=(2\cdot n-1)\cdot (N\cdot m^{\frac{1}{t}}+N^{\frac{2}{t+1}}\cdot m)
$$



## 去重





## 其他

- MPI
- logP 可扩展模型：分析网络活动成本（LogP: Towards a realistic model of parallel computation）
