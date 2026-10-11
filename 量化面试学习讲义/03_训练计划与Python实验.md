# 训练计划与 Python 实验

## 每日 60-90 分钟模板

- 15 分钟：复习公式和术语；
- 25 分钟：限时完成 2-3 道题；
- 20 分钟：写出完整口头解法；
- 20 分钟：Python 验证或实现；
- 10 分钟：记录错误类型。

## 8 周计划

| 周次 | 主题 | 目标 |
|---|---|---|
| 1 | 通用原则、估算、逻辑题 | 建立清晰表达和拆题习惯 |
| 2 | 微积分、线性代数 | 掌握导数、积分、矩阵和特征值 |
| 3 | 基础概率 | 条件概率、贝叶斯、组合计数 |
| 4 | 随机变量与统计 | 期望、方差、协方差、极限定理 |
| 5 | 回归与时间序列 | OLS、残差、过拟合、平稳性 |
| 6 | 随机过程 | 马尔可夫链、布朗运动、鞅 |
| 7 | 金融数学 | 无套利、二叉树、Black-Scholes |
| 8 | 编程和综合面试 | 数据结构、算法、模拟面试 |

## 训练记录模板

```text
日期：
题目：
限时：
第一次思路：
错误类型：概念 / 计算 / 表达 / 边界情况 / 编程
正确解法：
需要复习的知识点：
```

## 第一批 Python 实验

### 1. 几何分布与等待时间

```python
import numpy as np

rng = np.random.default_rng(2026)
p = 0.25
trials = 300_000
samples = rng.geometric(p, size=trials)

print(f"模拟均值 = {samples.mean():.4f}")
print(f"理论均值 = {1 / p:.4f}")
print(f"模拟方差 = {samples.var():.4f}")
print(f"理论方差 = {(1 - p) / p**2:.4f}")
```

### 2. 至少一次成功与补事件

```python
import numpy as np

rng = np.random.default_rng(2026)
p = 0.2
n = 5
trials = 200_000

outcomes = rng.random((trials, n)) < p
at_least_one = outcomes.any(axis=1).mean()
theory = 1 - (1 - p) ** n

print(f"模拟概率 = {at_least_one:.5f}")
print(f"理论概率 = {theory:.5f}")
```

### 3. 蒙特卡洛估算圆周率

```python
import numpy as np

rng = np.random.default_rng(2026)
n = 1_000_000
points = rng.uniform(-1, 1, size=(n, 2))
inside = (points[:, 0] ** 2 + points[:, 1] ** 2) <= 1
pi_estimate = 4 * inside.mean()

print(f"估计值 = {pi_estimate:.6f}")
print(f"误差 = {abs(pi_estimate - np.pi):.6f}")
```

### 实验要求

改变随机种子和样本量，观察：

- 样本量增加时误差通常如何变化；
- 模拟均值是否稳定接近理论均值；
- 极小概率事件为什么需要更多样本；
- 随机模拟结果为什么不能被误读为确定性证明。
