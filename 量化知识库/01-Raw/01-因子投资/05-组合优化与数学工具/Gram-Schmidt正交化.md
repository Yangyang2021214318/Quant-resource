# Gram-Schmidt正交化

Gram-Schmidt正交化通过逐步剔除变量在已有方向上的投影，构造相互正交的变量。

## 一元回归中的去均值

若截距项为 $x_0=\mathbf 1$，解释变量为 $x_1$，则：

$$z_1=x_1-\frac{\langle \mathbf 1,x_1\rangle}{\langle \mathbf 1,\mathbf 1\rangle}\mathbf 1=x_1-\bar x\mathbf 1$$

也就是对 $x_1$ 去均值，使其与截距项正交。

## 多元推广

对第 $k$ 个变量：

$$z_k=x_k-\sum_{j=0}^{k-1}\frac{\langle z_j,x_k\rangle}{\langle z_j,z_j\rangle}z_j$$

## 回归含义

正交化后，可以用简单投影公式计算某个变量的边际贡献，清楚地区分各解释变量之间的重叠信息。

## 来源

- Day4.md

