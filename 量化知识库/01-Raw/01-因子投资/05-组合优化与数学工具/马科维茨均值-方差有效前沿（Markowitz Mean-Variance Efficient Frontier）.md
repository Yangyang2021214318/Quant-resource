# 有效前沿的完整数学推导

我们从零开始，一步步推导这条双曲线（子弹头曲线）到底是怎么来的。这个推导只需要基本的拉格朗日乘数法，但每一步的"为什么这样做"比公式本身更重要，我们会把两者都讲清楚。

## 第一步：把问题写成一个精确的数学优化问题

**投资者面临的决策**：市场上有 $n$ 种风险资产，收益率是随机变量，均值向量为

$$\mu = (\mu_1, \mu_2, \ldots, \mu_n)'$$

协方差矩阵为 $\Sigma$（$n\times n$，正定，意味着不存在无风险的"免费组合"，也保证了它可逆）。

投资者选择权重 $w = (w_1,\ldots,w_n)'$（可以为负，代表卖空），组合的均值和方差分别是：

$$\mu_p = w'\mu, \qquad \sigma_p^2 = w'\Sigma w$$

**投资者想做的事**：在"达到某个目标预期回报率 $\mu_p$"的前提下，让组合的方差（风险）尽可能小。这是有效前沿定义的直接数学翻译——"给定回报，风险最小"。

写成优化问题：

$$\min_{w} \ \frac{1}{2}w'\Sigma w$$ $$\text{s.t.} \quad w'\mu = \mu_p \quad \text{(达到目标回报)}$$ $$\quad\quad\ \ w'\iota = 1 \quad \text{(权重加总为1，}\iota\text{是全1向量)}$$

（目标函数前面乘 $\frac{1}{2}$ 纯粹是为了求导后系数好看，不影响最优解。）

## 第二步：用拉格朗日乘数法处理约束优化

**为什么用拉格朗日**：这是一个"在线性约束下求二次型最小值"的经典问题，二次型的最小值问题求导后是线性方程组，可以精确求解——这也是为什么马科维茨的理论能给出**封闭解（closed-form solution）**，而不需要数值近似。

构造拉格朗日函数（$\lambda,\gamma$ 是两个约束各自的乘数）：

$$\mathcal{L}(w,\lambda,\gamma) = \frac{1}{2}w'\Sigma w - \lambda(w'\mu - \mu_p) - \gamma(w'\iota - 1)$$

对 $w$ 求一阶偏导并令其为零（这是求极值的必要条件）：

$$\frac{\partial \mathcal{L}}{\partial w} = \Sigma w - \lambda\mu - \gamma\iota = 0$$

$$\Rightarrow \quad \Sigma w = \lambda\mu + \gamma\iota$$

由于 $\Sigma$ 正定可逆，两边左乘 $\Sigma^{-1}$：

$$\boxed{w = \lambda\Sigma^{-1}\mu + \gamma\Sigma^{-1}\iota}$$

**这一步的直觉**：最优权重 $w$ 一定能写成两个固定向量（$\Sigma^{-1}\mu$ 和 $\Sigma^{-1}\iota$）的线性组合，线性组合的系数 $\lambda,\gamma$ 由你想要达到的目标回报 $\mu_p$ 决定。这已经在暗示"两基金分离定理"——不管 $\mu_p$ 取什么值，最优组合永远只是这两个固定向量的某种混合。

## 第三步：把约束条件代入，解出 $\lambda,\gamma$ 关于 $\mu_p$ 的表达式

现在把第二步得到的 $w$ 代回两个约束条件里，消去 $w$，只留下 $\lambda,\gamma,\mu_p$ 的方程。

**代入第一个约束** $w'\mu=\mu_p$：

$$w'\mu = (\lambda\Sigma^{-1}\mu+\gamma\Sigma^{-1}\iota)'\mu = \lambda(\mu'\Sigma^{-1}\mu) + \gamma(\iota'\Sigma^{-1}\mu) = \lambda C + \gamma B = \mu_p$$

（这里用到 $\Sigma^{-1}$ 对称，所以 $\mu'\Sigma^{-1}\mu = \mu'(\Sigma^{-1})'\mu$，标量转置等于自身；并定义了 $B=\iota'\Sigma^{-1}\mu$，$C=\mu'\Sigma^{-1}\mu$）

**代入第二个约束** $w'\iota=1$：

$$w'\iota = \lambda(\mu'\Sigma^{-1}\iota) + \gamma(\iota'\Sigma^{-1}\iota) = \lambda B + \gamma A = 1$$

（这里定义了 $A = \iota'\Sigma^{-1}\iota$，注意 $\mu'\Sigma^{-1}\iota = \iota'\Sigma^{-1}\mu = B$，因为标量转置不变）

现在得到一个关于 $\lambda,\gamma$ 的**二元一次方程组**：

$$\begin{cases} C\lambda + B\gamma = \mu_p \ B\lambda + A\gamma = 1 \end{cases}$$

写成矩阵形式：

$$\begin{pmatrix} C & B \ B & A \end{pmatrix}\begin{pmatrix}\lambda\\gamma\end{pmatrix} = \begin{pmatrix}\mu_p\1\end{pmatrix}$$

用克莱姆法则（Cramer's Rule）求解。系数矩阵的行列式是：

$$\det = AC - B^2 \equiv D$$

（这正是之前定义的 $D$，由柯西-施瓦茨不等式保证 $D>0$，除非所有资产预期收益完全相同这种退化情形）

解出：

$$\lambda = \frac{\mu_p A - B}{D}, \qquad \gamma = \frac{C - B\mu_p}{D}$$

## 第四步：计算最优组合的方差 $\sigma_p^2$——这是推导的核心

现在我们有了 $w(\mu_p)$ 的完整表达式，代入目标函数 $\sigma_p^2 = w'\Sigma w$。这里有一个非常优雅的简化技巧：

$$\sigma_p^2 = w'\Sigma w = w'(\lambda\mu+\gamma\iota) = \lambda(w'\mu) + \gamma(w'\iota) = \lambda\mu_p + \gamma \cdot 1$$

**这一步为什么这么简单**：因为最优 $w$ 满足 $\Sigma w = \lambda\mu+\gamma\iota$（第二步的一阶条件），所以 $w'\Sigma w$ 可以直接换成 $w'(\lambda\mu+\gamma\iota)$，而 $w'\mu=\mu_p$、$w'\iota=1$ 都是已知的约束条件——不需要真的把 $w$ 的完整表达式代入再算一遍矩阵乘法，大大简化了计算。

把第三步求出的 $\lambda,\gamma$ 代入：

$$\sigma_p^2 = \frac{\mu_p A - B}{D}\mu_p + \frac{C-B\mu_p}{D} = \frac{A\mu_p^2 - B\mu_p + C - B\mu_p}{D}$$

$$\boxed{\sigma_p^2 = \frac{A\mu_p^2 - 2B\mu_p + C}{D}}$$

**这就是有效前沿的方程**——一个关于 $\mu_p$ 的二次函数（抛物线），这也是之前提到的关键公式的完整来源。

## 第五步：为什么这条曲线在"标准差-均值"平面上是双曲线

上面的方程是 $\sigma_p^2$（方差）关于 $\mu_p$ 的二次函数——如果画在"**方差**-均值"平面上，就是一条标准的**抛物线**。

但金融学教科书里画的图，横轴通常是**标准差** $\sigma_p$（不是方差），这时候需要开根号：

$$\sigma_p = \sqrt{\frac{A\mu_p^2-2B\mu_p+C}{D}}$$

对这个方程做一点代数整理（配方），可以把它化成标准双曲线方程的形式。先对二次项配方：

$$A\mu_p^2 - 2B\mu_p + C = A\left(\mu_p - \frac{B}{A}\right)^2 + \left(C - \frac{B^2}{A}\right) = A\left(\mu_p-\frac{B}{A}\right)^2 + \frac{D}{A}$$

代入：

$$\sigma_p^2 = \frac{A}{D}\left(\mu_p-\frac{B}{A}\right)^2 + \frac{1}{A}$$

移项整理：

$$\sigma_p^2 - \frac{A}{D}\left(\mu_p - \frac{B}{A}\right)^2 = \frac{1}{A}$$

两边同除以 $\dfrac{1}{A}$：

$$\frac{\sigma_p^2}{1/A} - \frac{\left(\mu_p-B/A\right)^2}{D/A^2} = 1$$

**这正是标准双曲线方程的形式** $\dfrac{x^2}{a^2} - \dfrac{y^2}{b^2}=1$（这里 $x=\sigma_p$，$y=\mu_p - B/A$）！

- **中心点**：$(\sigma_p,\mu_p) = (0, B/A)$——也就是"假想中风险为零时"对应的均值 $B/A$，这正是全局最小方差组合（MVP）的均值
- **实半轴**：$a = 1/\sqrt{A}$，对应 MVP 本身的标准差 $\sigma_{MVP}=1/\sqrt{A}$（在 $\mu_p=B/A$ 处代入原方程可验证）
- **虚半轴**：$b=\sqrt{D}/A$，决定了双曲线张开的"陡峭程度"（也就是渐近线的斜率）

由于风险 $\sigma_p \geq 0$（标准差不能为负），我们只取双曲线的**右半支**——这就是教科书上那个经典的"子弹头"形状的完整数学来源。

## 第六步：为什么上半支才是"有效"的

双曲线的右半支关于 $\mu_p = B/A$ 这条水平线**上下对称**（因为方程中 $\mu_p$ 只以 $(\mu_p-B/A)^2$ 的形式出现，$\mu_p$ 偏离 $B/A$ 越远，无论往上还是往下，$\sigma_p$ 都同样增大）。

**关键观察**：给定同一个风险水平 $\sigma_p$，方程 $\sigma_p^2=\frac{A\mu_p^2-2B\mu_p+C}{D}$（这是关于 $\mu_p$ 的二次方程）会解出**两个** $\mu_p$ 值——一个在 $B/A$ 之上，一个在 $B/A$ 之下,两者关于 $B/A$ 对称。理性投资者当然会选择**均值更高的那一个**（同样的风险，为什么要选回报更低的组合？）,这就是为什么只有 $\mu_p > B/A$ 的**上半支**才被称为"有效前沿"，下半支虽然在数学上同样满足"给定均值、方差最小"这个优化条件，但从来不会被理性投资者选中。

## 完整推导逻辑链条回顾

1. **写出"给定回报、方差最小"的约束优化问题**（第一步）
2. **用拉格朗日乘数法求出最优权重的通式** $w=\lambda\Sigma^{-1}\mu+\gamma\Sigma^{-1}\iota$（第二步）——顺带得到两基金分离定理
3. **把约束条件代回，解出 $\lambda,\gamma$ 关于目标回报 $\mu_p$ 的表达式**（第三步）——纯粹是解一个2×2线性方程组
4. **利用一阶条件的结构，巧妙地把 $\sigma_p^2=w'\Sigma w$ 简化成 $\lambda\mu_p+\gamma$，避免了繁琐的矩阵运算**（第四步）——代入后得到二次函数 $\sigma_p^2$ 关于 $\mu_p$ 的显式公式
5. **对这个二次函数配方，化成标准双曲线方程**，得到中心点、实虚半轴等几何参数（第五步）
6. **利用双曲线关于中心对称的性质，说明只有上半支才是有效前沿**（第六步）

这条逻辑链条完全没有涉及任何"市场均衡"或"投资者效用函数"的假设——它纯粹是**给定 $\mu$ 和 $\Sigma$ 这两个统计量之后的一个几何/代数事实**。这也是为什么之前那个 Black CAPM 证明里,能够在完全不引入无风险资产的情况下,先单纯利用这条双曲线的几何性质,推出"任意有效组合都存在唯一零贝塔搭档"这个结论——因为这个结论本质上只是双曲线本身的代数性质，市场均衡只是在这条已经推导好的曲线上"选择落脚点"的问题。

---
![[Pasted image 20260707124213.png]]

















