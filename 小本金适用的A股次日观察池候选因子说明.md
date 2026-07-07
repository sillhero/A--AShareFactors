# 小本金适用的A股次日观察池候选因子说明

## 这是什么

这是一个基于 A 股日频价量行为构建的候选因子说明文档，当前版本主要用于：

1. 展示单因子的定义与直觉
2. 展示单因子的历史评分
3. 接收外部反馈，迭代因子逻辑与适用场景

当前公开目标不是“展示高收益曲线”，而是请社区一起帮助检查：

- 因子逻辑是否有重复或隐含共线
- 是否存在更好的中性化方式
- 是否适合从 `CSI300` 扩展到更广股票池
- 这些因子是否真的适合小本金场景

这组因子的现实定位，更偏向 `小本金 / 小容量资金` 的使用场景，而不是追求大资金容量下的机构化部署。

## 当前口径

- 股票池：`CSI300`
- 频率：`日频`
- 用途：`收盘后研究 / 次日观察池候选`
- 展示方式：`逐个因子单独说明`

## 5 个底层因子

### 1. IntradayRangeComplexityDrift_5D

表达式：

```text
($return > 0) ? ((1 - 2 * RANK(TS_STD($high - $low, 5))) * (1 - ABS($return))) : 0
```

直觉：

- 在上涨日里，更偏好“日内波动收敛、涨幅不过热”的标的
- 更像是对短期稳定上行的偏好

### 2. Neg_RangeVol_20D

表达式：

```text
RANK(-TS_STD($high - $low, 20))
```

直觉：

- 偏好过去 20 日波动区间更稳定的股票
- 是一个明显的低波动/低扰动过滤器

### 3. Surprise_minus_Volume_Rank

表达式：

```text
RANK(TS_ZSCORE($return, 20)) - RANK($volume)
```

直觉：

- 偏好“收益异常度较高，但成交量并未同步爆炸”的标的
- 试图捕捉相对不拥挤的价格异动

### 4. Net_Buying_Accumulation_Signal_20D

表达式：

```text
TS_SUM(SIGN($close - $open) * $volume, 20) / (TS_SUM($volume, 20) + 1e-8)
```

直觉：

- 用 20 日的阳线/阴线成交量方向累积，近似衡量“净买入倾向”
- 更像是持续承接强弱

### 5. PosCumRet_Vol_Interaction_5D_20D

表达式：

```text
(TS_SUM($return, 5) > 0) ? (TS_SUM($return, 5) * TS_STD($return, 20)) : 0
```

直觉：

- 只在近 5 日累计收益为正时激活
- 想测“上涨背景下的波动放大”是否仍然有效

## 因子评分

### 单因子评分

这 5 个因子来自去重后的候选库，在去重报告中的核心评分如下：

| 因子名 | RankIC | With-cost IR | 备注 |
| --- | ---: | ---: | --- |
| IntradayRangeComplexityDrift_5D | 0.047728 | 1.041990 | 5 日日内波动收敛型 |
| Neg_RangeVol_20D | 0.043554 | 1.166517 | 20 日低波动过滤 |
| Surprise_minus_Volume_Rank | 0.043404 | 1.258276 | 异动但不过度拥挤 |
| Net_Buying_Accumulation_Signal_20D | 0.043014 | 1.176492 | 持续承接/净买入倾向 |
| PosCumRet_Vol_Interaction_5D_20D | 0.042370 | 1.274395 | 上涨背景下的波动交互 |

说明：

- `RankIC` 用来衡量因子排序能力，越高越好
- `With-cost IR` 是考虑成本后的信息比率，越高越稳
- 这组因子入选的原因，不只是单一分数高，还包括去重后保留下来的差异化信息

## 各因子逐项说明

### IntradayRangeComplexityDrift_5D

- RankIC：`0.047728`
- With-cost IR：`1.041990`
- 适用直觉：更偏向短期稳定上行、波动收敛、不过热的标的

### Neg_RangeVol_20D

- RankIC：`0.043554`
- With-cost IR：`1.166517`
- 适用直觉：更偏向过去 20 日波动稳定、扰动较小的标的

### Surprise_minus_Volume_Rank

- RankIC：`0.043404`
- With-cost IR：`1.258276`
- 适用直觉：更偏向价格异动明显、但成交量尚未极端拥挤的标的

### Net_Buying_Accumulation_Signal_20D

- RankIC：`0.043014`
- With-cost IR：`1.176492`
- 适用直觉：更偏向持续承接、净买入倾向更强的标的

### PosCumRet_Vol_Interaction_5D_20D

- RankIC：`0.042370`
- With-cost IR：`1.274395`
- 适用直觉：更偏向近 5 日已转强，同时波动交互仍有效的标的

## 已观察到的特征

- `IntradayRangeComplexityDrift_5D` 与 `Neg_RangeVol_20D` 相关性偏高，说明低波动信息有重叠
- 这些因子在弱市里更容易偏向银行、港口、电力、运营商这类防守资产
- 从单因子直觉看，它们整体更适合做“观察池候选因子”，不适合直接写成自动交易承诺

## 当前限制

### 1. 行业/市值中性化还不完整

当前本地主数据里没有完整行业和市值暴露字段，因此公开版本还没有做严格的行业/市值中性化。

### 2. 股票池还比较窄

目前主要在 `CSI300` 内验证，尚未证明在全 A 或中小盘池中同样稳健。