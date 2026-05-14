# Fama-MacBeth回归法

实证资产定价中用于估计因子风险溢价的两步截面回归方法，是[[因子投资]]研究最核心的统计工具之一。

## 方法步骤

### 第一步：时序回归（估计因子暴露 $\beta$）

对每个资产 $i$，用历史数据做时间序列回归：

$$R_{it}^e = \alpha_i + \beta_i' f_t + \varepsilon_{it}$$

得到每个资产在各因子上的载荷估计 $\hat{\beta}_i$。

### 第二步：逐期截面回归（估计因子溢价 $\lambda$）

对每一期 $t$，用所有资产的实际超额收益对其因子暴露做截面回归：

$$R_{it}^e = \gamma_{0t} + \hat{\beta}_i' \lambda_t + \eta_{it}$$

得到每期的因子溢价估计 $\hat{\lambda}_t$。

### 汇总：取时序均值

$$\hat{\lambda} = \frac{1}{T}\sum_{t=1}^T \hat{\lambda}_t, \quad \text{标准误} = \frac{\text{std}(\hat{\lambda}_t)}{\sqrt{T}}$$

## 优势

- **回避横截面相关**：先时序后截面，不受同期收益率截面相关性干扰
- **直接估计风险溢价**：$\hat{\lambda}$ 即为因子的风险溢价
- **结合 Newey-West 调整**：可处理误差项的序列相关和异方差

## 在因子研究中的应用

| 应用场景 | 说明 |
|---------|------|
| 检验因子显著性 | $\hat{\lambda}$ 的 $t$ 值是否显著不为零 |
| 比较多因子模型 | 加入新因子后，原有异象 $\alpha$ 是否消失 |
| 估计 Barra 风险因子的收益率 | 每期截面回归的收益率即为风险因子收益率 |

## 历史背景

- Fama & MacBeth（1973）为检验 [[CAPM]] 而提出，但方法本身的影响远超 CAPM 检验本身
- 现已成为实证资产定价的标准工具
- Newey-West（1987）修正解决了标准误低估问题

## 局限

- 第一步估计的 $\hat{\beta}$ 存在测量误差（errors-in-variables），会导致 $\lambda$ 估计有偏
- 通常需要用投资组合代替个股来降低噪声
- 不能直接估计 $\alpha_i$（需配合 GRS 检验）

## 相关链接

- [[因子投资]]
- [[多因子模型]]
- [[CAPM]]
- [[异象]]

## 来源引用

- 石川、刘洋溢、连祥斌《因子投资：方法与实践》电子工业出版社，2020（第2章）
- Fama, E.F. & MacBeth, J.D. (1973). Risk, return, and equilibrium: Empirical tests. *Journal of Political Economy*.
