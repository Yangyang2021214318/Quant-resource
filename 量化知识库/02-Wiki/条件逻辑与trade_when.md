# 条件逻辑与 trade_when

> **一句话定义**：条件逻辑是给 Alpha 装上"开关"的机制——`if_else` 是每天重判的瞬时开关，`trade_when` 是有记忆的状态机，解决"何时进、何时出、中间怎么持有"三个问题。

---

## 基础条件

支持常见逻辑运算符（`>`, `<`, `>=`, `<=`, `==` 等）：

```
volume > adv20              成交量超过 20 日均量
close >= ts_max(high, 50)   收盘价创 50 日新高
returns < -0.05             单日跌幅超过 5%
```

这些条件返回布尔值（true / false），本身不产生 Alpha，而是用来**控制** Alpha 的开关。

> ⚠️ 过度限制条件会大幅减少有效数据点，增加过拟合风险——条件越多，回测越好看，实盘越惨。

---

## `if_else` —— 瞬时开关

两种写法等价：

```
if_else(volume > adv20, alpha_value, 0)
volume > adv20 ? alpha_value : 0
```

| 场景 | 写法 | 语义 |
|------|------|------|
| 条件不满足时空仓 | `if_else(cond, alpha, 0)` | 不放量就不持仓 |
| 条件不满足时反转 | `if_else(cond, alpha, -alpha)` | 不放量时反向操作 |
| 嵌套多条件 | `if_else(cond1, a1, if_else(cond2, a2, 0))` | 多层优先级 |

**痛点**：每天重新计算，没有记忆——今天放量就持仓，明天缩量立刻清零。无法"持有"仓位，不适合中长期策略。

---

## `trade_when` —— 有记忆的状态机

```
trade_when(entry_condition, Alpha_expression, exit_condition)
            └── 开仓 ──┘   └── Alpha ──┘   └── 平仓 ──┘
```

| entry | exit | 行为 |
|-------|------|------|
| **true** | false | 用当天 Alpha 值**更新**仓位 |
| false | false | **维持**上次 entry=true 时的仓位不动 |
| 任意 | **true** | **清仓**（返回 NaN） |
| 任意 | **−1** | **永不清仓**——持有到最后 |

### 与 if_else 的本质区别

```
if_else(放量, Alpha, 0)
  → 今天放量持仓，明天缩量立刻清零 → 持仓最多 1 天

trade_when(放量, Alpha, -1)
  → 放量的那一天开仓，之后即使缩量也一直持有 → 持仓可延续数周
```

`if_else` 是瞬时开关，`trade_when` 是有记忆的状态机。

---

## 实战：动量 Alpha + trade_when

```
trade_when(
    volume > adv20,           ← 开仓：成交量突破 20 日均量时入场
    positive_days,            ← Alpha：过去一年上涨天数
    -1                        ← 离场：-1 = 永不清仓
)
```

**逻辑链**：放量那一天按动量信号开仓 → 之后每天持有不动 → 让中长期趋势充分展开。

为什么 `exit = −1`？[[动量效应]] 是 3~12 个月的中长期现象，频繁进出只会增加交易成本和打断趋势累积。

### 三种开仓条件对比

| 写法 | 含义 | 特点 |
|------|------|------|
| `volume > adv20` | 成交量超 20 日均量 | 中等敏感，捕捉温和放量 |
| `volume > ts_mean(volume, N)` | 超 N 日均量 | N 越小越敏感，可调节 |
| `volume * vwap > ts_mean(volume * vwap, N)` | 成交金额超 N 日均值 | 最稳健——统一量价维度 |

---

## 条件强度谱系

```
if_else              trade_when(..., -1)        trade_when(..., exit)
    │                      │                           │
    ▼                      ▼                           ▼
 每天重判             入场后永不离场              入场后有条件离场
 最灵活 / 最碎         最坚定 / 最低成本          有始有终 / 最完整
 适合反转策略          适合中长期动量             适合有明确止盈止损的策略
```

---

## 相关链接

- [[动量效应]]
- [[WorldQuant Brain平台]]
- [[技术分析]]
- [[组合构建]]

---

## 来源

- [[01-Raw/Worldquant brain/Day2]]
