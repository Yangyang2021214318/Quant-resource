# Fama-MacBeth回归

Fama-MacBeth回归通过逐期截面回归估计因子风险溢价，再对估计值取时间序列平均。

## 为什么需要它

普通截面回归把收益在时间上先取均值，只做一次截面OLS，容易忽略定价误差的时序相关性。Fama-MacBeth把每期横截面结果保留下来，让标准误来自估计值自身的时间序列波动。

## 三步流程

第一步，对每个资产估计因子暴露：

$$R^e_{it}=a_i+\hat\beta_i'f_t+\varepsilon_{it}$$

第二步，每期做一次截面回归：

$$R^e_{it}=\hat\beta_i'\lambda_t+\alpha_{it}$$

第三步，对 $T$ 个 $\hat\lambda_t$ 取均值：

$$\hat\lambda=\frac{1}{T}\sum_{t=1}^{T}\hat\lambda_t$$

标准误可由 $\hat\lambda_t$ 的时间序列波动估计：

$$s.e.(\hat\lambda)=\frac{\operatorname{std}(\hat\lambda_t)}{\sqrt T}$$

## 来源

- Day2.md

