# EIV测量误差

EIV是指回归自变量本身含有测量误差，在因子模型中常表现为估计暴露 $\hat\beta$ 不等于真实暴露 $\beta$。

## 问题来源

Fama-MacBeth第二步使用第一步估计出的 $\hat\beta_i$：

$$R^e_{it}=\hat\beta_i'\lambda_t+\alpha_{it}$$

但真实关系应使用 $\beta_i$。当：

$$\hat\beta_i=\beta_i+\text{估计误差}$$

自变量含噪声，OLS估计出的 $\lambda$ 会系统性偏向0。

## 衰减偏误

直觉上，带噪声的 $\hat\beta$ 会让横截面排序变得更混乱，使收益和暴露之间的真实关系看起来更弱，于是估计的因子溢价被低估。

## 来源

- Day2.md

