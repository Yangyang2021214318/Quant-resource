# Newey-West调整

Newey-West调整通过加入滞后残差乘积，修正异方差和自相关导致的标准误偏误。

## 核心思想

简单标准误忽略跨期相关性。Newey-West不仅使用同期残差平方，还把相隔 $1,\cdots,L$ 期的残差乘积纳入方差估计。

## Bartlett权重

滞后 $k$ 期的权重为：

$$1-\frac{k}{L+1}$$

滞后越远，权重越小；超过截断窗口 $L$ 后直接忽略。

## 在异象检验中的作用

Fama-MacBeth检验异象时，每期得到 $\lambda^\alpha_t$。若这个序列有自相关，简单的 $\operatorname{std}(\lambda^\alpha_t)/\sqrt T$ 会低估标准误。Newey-West使t值更保守，减少虚假显著。

## 来源

- Day3.md

