# CAPM与β

CAPM认为资产的预期超额收益只来自其对市场系统性风险的暴露。

## 核心公式

$$E[R_i]-R_f=\beta_i(E[R_M]-R_f)$$

- $E[R_i]-R_f$：资产 $i$ 的预期超额收益。
- $E[R_M]-R_f$：市场风险溢价。
- $\beta_i$：资产与市场共同波动的程度。

## β的统计含义

$$\beta_i=\frac{\operatorname{cov}(R_i,R_M)}{\operatorname{var}(R_M)}$$

把 $R_i-R_f$ 对 $R_M-R_f$ 做一元OLS回归：

$$R_i-R_f=\alpha_i+\beta_i(R_M-R_f)+\varepsilon_i$$

回归斜率就是 $\beta_i$。它衡量的不是资产自身总波动，而是资产收益中与市场共同波动的部分。

## 投资含义

- $\beta=1$：资产与市场风险暴露同步。
- $\beta=2$：资产承担约两倍市场系统性风险，因此要求约两倍市场风险补偿。
- $\beta=0$：资产不承担市场系统性风险，CAPM下不应获得超额收益。

## 来源

- Day1.md

