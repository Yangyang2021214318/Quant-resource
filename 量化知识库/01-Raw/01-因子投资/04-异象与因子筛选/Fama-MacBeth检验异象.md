# Fama-MacBeth检验异象

Fama-MacBeth检验异象是在控制已知因子后，检验某个异象变量是否仍能解释未来收益。

## 回归形式

在每期 $t$ 做截面回归：

$$R^e_{it}=\hat\beta_i'\lambda_t+\lambda^\alpha_t Z_{it}+\alpha_{it}$$

- $Z_{it}$：异象变量，如BM、动量、盈利能力。
- $\lambda^\alpha_t$：异象变量在第 $t$ 期的截面收益补偿。

## 原假设

$$H_0:\lambda^\alpha=0$$

即控制已知因子后，异象没有额外预测能力。

## 标准误

对 $\{\hat\lambda^\alpha_t\}$ 取均值得到 $\hat\lambda^\alpha$，通常需要Newey-West调整，因为异象收益序列可能存在异方差和自相关。

## 来源

- Day3.md

