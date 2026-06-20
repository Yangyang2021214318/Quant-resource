
# Brain 平台算子全览

> 来源：[[01-Raw/文章/-Join WorldQuant BRAIN 1]]
> 难度等级越高（Expert → Master → Grandmaster），解锁的算子越复杂强大。

Brain 平台的算子分为七大类别，分别作用于不同维度的数据。理解每一类算子的"输入输出形状"是构建 Alpha 的第一步。

---

## 一、算术算子（Arithmetic）

最基础的数学运算，是所有 Alpha 表达式的砖瓦。

| 算子 | 写法 | 说明 | 实战直觉 |
|------|------|------|---------|
| `abs(x)` | `abs(close - open)` | 取绝对值，去掉负号 | 只关心变化幅度，不关心方向——比如"日内波动有多大" |
| `add(x, y, filter=false)` | `x + y` | 逐元素相加。`filter=true` 时把 NaN 当成 0 再加 | **强烈建议用 `filter=true`**——避免一个 NaN 污染整列 |
| `divide(x, y)` | `x / y` | 逐元素相除 | 常用于做比率，如 `close / vwap` |
| `inverse(x)` | `1 / x` | 取倒数 | 越小 → 越大，常用于把"便宜"变成"高分" |
| `log(x)` | `log(market_cap)` | 自然对数 | **压缩右偏分布**——市值从 10 亿到 10000 亿跨 3 个量级，log 后变成 1 到 4 |
| `max(x, y, ...)` | `max(a, b, c)` | 取最大值（至少 2 个输入） | 常用于"至少满足一个条件"，如最低收益门槛 |
| `min(x, y, ...)` | `min(a, b, c)` | 取最小值（至少 2 个输入） | 取保守估计 |
| `multiply(x, y, filter=false)` | `x * y` | 逐元素相乘。`filter=true` 把 NaN 当 0 | 做权重乘法——信号 × 置信度 |
| `power(x, y)` | `x ^ y` | x 的 y 次方 | 平方放大极端信号，开方压缩极端信号 |
| `reverse(x)` | `-x` | 取相反数（负号） | **翻转信号方向**：正向 Alpha 变反向，是反转策略的核心操作 |
| `sign(x)` | `sign(x)` | 正数 → +1，负数 → −1，零 → 0，NaN → NaN | 只要方向不要幅度，纯粹的趋势判断 |
| `signed_power(x, y)` | `signed_power(x, 2)` | x 的 y 次方，**保留符号** | 相比 `power(x,2)` 会丢掉负号，`signed_power` 保留了"涨还是跌"的信息 |
| `sqrt(x)` | `sqrt(x)` | 非负平方根，等同于 `power(x, 0.5)` | 压缩极端值但保持符号？不——只接受非负数。带符号开根用 `signed_power(x, 0.5)` |
| `subtract(x, y, filter=false)` | `x - y` | 左减右，支持多个输入 | 最常用：`close - ts_mean(close, 20)`（价格偏离均线） |
| `densify(x)` | `densify(group)` | 将分组字段压缩为只含实际存在值的桶 | 分组字段有很多理论桶但实际只用到少数几个时，提高计算效率 |

### add/multiply 的 filter 参数详解

这是最容易被忽视但**最实用**的参数：

```
x = [1, NaN, 3]
y = [4, 5, NaN]

add(x, y)             → [5, NaN, NaN]   ← NaN 会"传染"
add(x, y, filter=true) → [5, 5, 3]       ← NaN 被当 0，不传染
multiply(x, y)         → [4, NaN, NaN]
multiply(x, y, filter=true) → [4, 0, 0]
```

**一句话**：数据缺得多的场景，总是加 `filter=true`——宁可当它是 0，也不要让一个缺失值毁掉整行。

---

## 二、逻辑算子（Logical）

产生 true/false 判断，用作条件开关或信号过滤。运算结果在 Brain 中是 **1（true）或 0（false）**。

| 算子 | 写法 | 说明 | 实战用法 |
|------|------|------|---------|
| `and(x, y)` | `(a > 0) and (b > 0)` | 两者都为 true 才返回 1 | 多条件同时满足——"放量 **且** 创新高" |
| `or(x, y)` | `(a < -0.05) or (b < -0.05)` | 至少一个为 true 返回 1 | 任一条件触发——"跌破支撑 **或** 放量下跌" |
| `not(x)` | `not(is_nan(x))` | 取反：1 → 0，0 → 1 | 翻转条件——"不是 NaN 就持仓" |
| `if_else(a, b, c)` | `if_else(cond, 优, 劣)` | a 为 true 返回 b，否则返回 c | **Alpha 的条件分支核心**——"放量时用动量，缩量时空仓" |
| `x < y` | `close < vwap` | 小于 | 收盘低于均价 → 偏弱 |
| `x <= y` | `close <= low` | 小于或等于 | 跌破前低 |
| `x > y` | `volume > adv20` | 大于 | 放量突破均量 |
| `x >= y` | `close >= ts_max(high, 50)` | 大于或等于 | 创 N 日新高 |
| `x == y` | `x == 0` | 等于 | 精确匹配——判断是否正好在均线上 |
| `x != y` | `x != 0` | 不等于 | 排除特定值 |
| `is_nan(x)` | `is_nan(close)` | 是 NaN 返回 1 | 检测缺失数据，配合 `if_else` 做回填 |

### 比较算子的真实输出

```
close = [101, 99, 105, NaN]
vwap  = [100, 100, 100, 100]

close > vwap → [1, 0, 1, 0]    ← NaN 比较返回 0（不是 NaN！）
close < vwap → [0, 1, 0, 0]
close == vwap → [0, 0, 0, 0]
```

---

## 三、时间序列算子（Time Series）

**最重要的算子类别之一**。每个算子都带一个 `d` 参数——**回溯天数**。它在"股票 × 时间"矩阵中沿时间轴纵向计算。

### 基础移动窗口

| 算子 | 说明 | 用途 |
|------|------|------|
| `ts_delay(x, d)` | d 天前的 x 值 | 取历史数据点——最基础的时间操作 |
| `ts_delta(x, d)` | x − ts_delay(x, d) | d 天的变化量——衡量动量 |
| `ts_mean(x, d)` | 过去 d 天的简单均值 | 移动平均，平滑噪声 |
| `ts_sum(x, d)` | 过去 d 天的求和 | 累计值——如"过去一年上涨天数" |
| `ts_product(x, d)` | 过去 d 天的乘积 | 几何平均、复利计算 |
| `ts_std_dev(x, d)` | 过去 d 天的标准差 | 波动率——衡量信号的稳定程度 |
| `ts_corr(x, y, d)` | x 和 y 过去 d 天的 Pearson 相关系数 | 两个变量的联动程度——如"量价相关" |
| `ts_covariance(y, x, d)` | x 和 y 过去 d 天的协方差 | 两个变量的联动**幅度** |
| `ts_count_nans(x, d)` | 过去 d 天 NaN 的数量 | 数据质量检查——NaN 太多就别用这个信号 |

### 排名与标准化

| 算子 | 说明 | 直觉 |
|------|------|------|
| `ts_rank(x, d, constant=0)` | 对过去 d 天排名，返回当前值的秩 | 把原始值变成"在历史上排第几"——跨时间可比 |
| `ts_zscore(x, d)` | (x − ts_mean(x, d)) / ts_std_dev(x, d) | 当前值偏离历史均值**多少个标准差**——经典标准化 |
| `ts_scale(x, d, constant=0)` | 缩放至 0~1：`(x − min) / (max − min)` | 线性映射到固定区间，比 rank 更平滑 |
| `ts_quantile(x, d, driver="gaussian")` | rank 后做高斯分布映射 | 正态化——把任意分布压成钟形曲线，对极端值不敏感 |
| `ts_av_diff(x, d)` | x − ts_mean(x, d)（忽略 NaN） | 简化版偏离度——当前值离均值多远 |

### 高级时间序列

| 算子 | 说明 | 实战场景 |
|------|------|---------|
| `ts_arg_max(x, d)` | 过去 d 天最大值发生在**几天前**。0 = 今天，1 = 昨天... | "在创新高，还是只是在追高？" |
| `ts_arg_min(x, d)` | 过去 d 天最小值发生在几天前 | "在创新低，还是见底了？" |
| `ts_decay_linear(x, d, dense=false)` | 线性衰减加权平均——越近权重越大 | 比 `ts_mean` 更灵敏的平滑，EMA 的替代方案 |
| `ts_step(1)` | 每日 +1 的计数器 | 生成时间轴、计算持仓天数 |
| `days_from_last_change(x)` | 自上次数值变化以来的天数 | 判断信号是否"停滞"了 |
| `hump(x, hump=0.01)` | 限制输入的变化幅度——降低换手率 | **强制"慢下来"**，减少过度交易 |
| `last_diff_value(x, d)` | 过去 d 天内，最近一次**不同于当前值**的值 | 跳过重复值，找"真正的变化点" |

### 缺失值处理

| 算子 | 说明 | 直觉 |
|------|------|------|
| `kth_element(x, d, k, ignore="NaN")` | 回溯 d 天，取第 k 个值，可选择忽略 NaN | 从历史中"掏出"第 k 个有效值——比 `ts_delay` 更灵活 |
| `ts_backfill(x, lookback=d, k=1)` | 用过去 d 天最近的第 k 个有效值填充 NaN | **数据回填核心工具**——"如果今天没数据，用最近的有效值代替" |

### 时间序列算子核心理解

```
ts_delta(close, 250) / ts_delay(close, 250)
    └── 过去一年的涨幅 ──┘   └── 一年前的价格 ──┘
    = 过去一年的收益率百分比

ts_zscore(returns, 20)
    = (今天收益率 − 过去20天平均收益率) / 过去20天收益率标准差
    → 今天的收益率在近期是否"异常"高或低？大于 +2 就是极端
```

**关键原则**：d 的选择 = 你在利用哪个时间频率的信号。小 d（5~20 天）捕捉反转，大 d（200~250 天）捕捉动量。

---

## 四、截面算子（Cross Sectional）

**第二重要的算子类别**。截面 = 同一天、所有股票。沿"股票轴"横向计算。

### 核心截面算子

| 算子 | 写法 | 说明 | 用途 |
|------|------|------|------|
| `rank(x, rate=2)` | `rank(returns)` | 按 x 值在所有股票中排名，映射到 **0~1** | **Alpha 构建第一步**——消除量纲，只看谁比谁好 |
| `zscore(x)` | `zscore(alpha)` | (x − 截面均值) / 截面标准差 | 标准化——"这只股票比市场均值高几个标准差" |
| `scale(x, scale=1, longscale=1, shortscale=1)` | `scale(alpha)` | 缩放使 ∑\|x\| = scale，可分别设置多空缩放系数 | **资金分配**——控制总敞口 |
| `normalize(x, useStd=false, limit=0.0)` | `normalize(alpha)` | 减去截面均值；可选除标准差；可选截断到 [−limit, +limit] | 最基本的中性化——所有股票信号减去当天全市场均值 |
| `quantile(x, driver="gaussian", sigma=1.0)` | `quantile(alpha)` | rank + 分布映射（高斯/Cauchy/均匀） | 比 rank 更灵活——sigma 控制输出分布的离散程度 |
| `winsorize(x, std=4)` | `winsorize(alpha)` | 把超出 ±std 个标准差的值拉回到边界 | **去极值**——大于 4σ 的值被截断到 4σ，不给极端值过度权重 |

### rank 的深入理解

`rank` 是构建 Alpha 最核心的截面算子：

```
原始 returns = [−3%, −1%, +0.5%, +2%, +8%]  （5 只股票）
                                  ↑ 极端值

rank(returns) → [0.0, 0.25, 0.5, 0.75, 1.0]

无论原始值差多少倍（8% vs −3%），rank 后永远均匀分布在 [0, 1]。
→ 按排名做多空：rank > 0.5 做多，rank < 0.5 做空。
```

**为什么不用原始值？**

| 原始值 | rank 后 |
|--------|--------|
| 受量纲影响（元、%、倍） | 无量纲，0~1 |
| 极端值扭曲全截面 | 均匀分布，不受极端值影响 |
| 跨时间不可比 | 分布始终一致 |
| A 比 B 大 100 倍 → 权重差 100 倍 | A 排第 1，B 排 100 → 权重差固定 |

### winsorize 的原理

```
原始值：[-8, −2, −1, −0.5, 0, 0.3, 0.5, 1, 2, 15]σ
                           均值 ≈ 0.73, σ ≈ 5.5

winsorize(x, std=4)：超出 ±4σ（即 ±22）的值被拉回。
15σ → 22 → 被截断
−8σ → 在范围内 → 不动

结果：极端值被"温和地"拉回，而非直接删除。
```

---

## 五、向量算子（Vector）

当数据字段本身是一组值（向量）而非单一数值时使用。向量算子把一组值**聚合为一个代表值**。（Day1 中已简要介绍，此处补充完整）

| 算子 | 说明 | 适用场景 |
|------|------|---------|
| `vec_avg(x)` | 向量所有元素的均值 | 最常用的聚合——平滑噪声 |
| `vec_sum(x)` | 向量所有元素求和 | 总量级信息——如某股票的"所有期权合约总成交额" |

> 注意：Brain 平台文档中只列出了 `vec_avg` 和 `vec_sum`，但 Day1 中还提到了 `vec_median`、`vec_max`/`vec_min`、`vec_stddev` 等实际可用算子。不同 Genius 级别解锁不同算子。

---

## 六、变换算子（Transformational）

作用于整个**矩阵（股票 × 时间）**，对每个值做数学变换，改变数值分布但不改变矩阵形状。（Day1 中已简述，此处补充完整算子列表）

### bucket —— 自定义分组

```
bucket(rank(x), range="0, 1, 0.1")
    → 按 rank 值每 0.1 分一组，共 10 组
    → 排名前 10% 的股票在同一个桶里

bucket(rank(x), buckets="2,5,6,7,10")
    → 手动指定 5 个桶边界
    → 排名在 0~2、2~5、5~6、6~7、7~10 各一组
```

`bucket` 的输出是一个**分组字段**，必须搭配 [[#七、分组算子（Group）|分组算子]] 使用：

```
group_neutralize(alpha, bucket(rank(market_cap), range="0, 1, 0.1"))
    → 在市值排名分组内做中性化
    → 剥离市值因子影响
```

| 参数 | 默认值 | 含义 |
|------|--------|------|
| `range` | — | 等间距分桶，格式 `"min, max, step"` |
| `buckets` | — | 手动指定桶边界 |
| `skipBoth` | `false` | 是否忽略最大和最小两个桶 |
| `NaNGroup` | `false` | 是否为 NaN 值单独创建一个桶 |

### trade_when —— 条件持仓

[[量化知识库/01-Raw/Worldquant brain/Day2#trade_when|Day2 中已详细讲解]]，此处做算子级补充：

```
trade_when(entry, Alpha, exit)
    entry: 开仓条件（true 时用当天 Alpha 更新仓位）
    Alpha:  持仓时的 Alpha 表达式
    exit:   平仓条件（true 时清仓，−1 表示永不清仓）
```

**状态机行为**：
- entry=true, exit=false → 用当天 Alpha 值**更新**仓位
- entry=false, exit=false → **维持**上次的仓位不动
- 任意, exit=true → **清仓**（NaN）

---

## 七、分组算子（Group）

依赖分组字段（行业、板块、国家、或 `bucket()` 自定义分组），在组内对 Alpha 做操作。（Day1 中已简介核心概念，此处补充完整算子列表）

| 算子 | 说明 | 实战场景 |
|------|------|---------|
| `group_neutralize(x, group)` | x − 组内均值 | **行业中性化**——变成"相对同行的超额信号" |
| `group_rank(x, group)` | 组内排名，输出 0~1 | 同行业内比谁更好，而非跨行业比 |
| `group_zscore(x, group)` | 组内 z-score | 组内标准化——"在同行中偏离了几个标准差" |
| `group_scale(x, group)` | 组内缩放至 0~1 | 组内归一化，跨组数据可比 |
| `group_mean(x, weight, group)` | 组内加权调和均值 | 带权重的组内平均——大市值股影响更大 |
| `group_backfill(x, group, d, std=4.0)` | 用同组过去 d 天的 winsorized 均值填充 NaN | 组内缺失值填充——"同行数据更靠谱" |

### 分组算子的应用层级

```
原始 Alpha → group_neutralize(alpha, sector)
           → group_rank(alpha, industry)
           → rank(alpha)  ← 跨行业截面排名
```

**典型流水线**：
1. 先组内中性化——剥离行业影响
2. 再组内排名——保留行业内选股能力
3. 最后截面 rank——统一跨行业比较

---

## 算子速查总表

| 类别 | 核心算子 | 一句话 |
|------|---------|--------|
| 算术 | `+, -, *, /, log, abs, sign` | 数据的数学砖瓦 |
| 逻辑 | `>, <, ==, and, or, if_else` | 条件开关和分支 |
| 时间序列 | `ts_delta, ts_mean, ts_rank, ts_zscore` | 沿时间轴看历史和趋势 |
| 截面 | `rank, zscore, normalize, winsorize` | 当天横向比较，消除量纲 |
| 向量 | `vec_avg, vec_sum` | 把一组值聚合为一个数 |
| 变换 | `bucket, trade_when` | 改变数据结构或持仓逻辑 |
| 分组 | `group_neutralize, group_rank` | 在行业内比较，剥离系统性偏差 |

**构建 Alpha 的典型流水线**：

```
原始数据 → 算术变换(log/sign) → 时间序列(ts_delta/ts_mean)
         → 截面标准化(rank/zscore) → 组内中性化(group_neutralize)
         → 权重缩放(scale) → 最终 Alpha 值
```

每一步都在解决一个问题：量纲、极端值、缺失值、行业偏差、换手率。理解了每个算子的"它解决什么问题"，就理解了如何组合它们。
