# A-Share Top5 Combo Factor Strategy

一个面向 A 股日频研究的开源实验仓库，当前重点探索：

`收盘后组合因子选股 + 次日舆情增强排序`

这个仓库公开的不只是结果，更是研究过程本身：因子定义、组合权重、去重思路、数据口径，以及目前还没有彻底解决的问题。欢迎社区一起复核、质疑和改进。

## 项目在做什么

当前版本围绕一套 `Top5` 价量组合因子展开：

1. 从日频价量行为中筛选候选因子
2. 将 5 个因子按固定权重组合成收盘后信号
3. 生成次日观察池
4. 用 FTShare 和外部资讯网站做舆情增强排序

目标不是做一个“自动下单黑盒”，而是做一套可讨论、可复核、可持续迭代的 A 股日频研究流程。

## 当前策略框架

- 股票池：`CSI300`
- 频率：`日频`
- 使用场景：`收盘后生成次日观察池`
- 组合方式：`横截面 rank 后固定权重加总`
- 舆情层：`FTShare + 外部资讯网站人工核查`

## 5 个底层因子

### 1. IntradayRangeComplexityDrift_5D

```text
($return > 0) ? ((1 - 2 * RANK(TS_STD($high - $low, 5))) * (1 - ABS($return))) : 0
```

含义：
偏好上涨日里“日内波动收敛、涨幅不过热”的标的。

### 2. Neg_RangeVol_20D

```text
RANK(-TS_STD($high - $low, 20))
```

含义：
偏好过去 20 日波动区间更稳定的股票。

### 3. Surprise_minus_Volume_Rank

```text
RANK(TS_ZSCORE($return, 20)) - RANK($volume)
```

含义：
偏好“收益异常度较高，但成交量尚未明显拥挤”的标的。

### 4. Net_Buying_Accumulation_Signal_20D

```text
TS_SUM(SIGN($close - $open) * $volume, 20) / (TS_SUM($volume, 20) + 1e-8)
```

含义：
近似衡量 20 日持续承接和净买入倾向。

### 5. PosCumRet_Vol_Interaction_5D_20D

```text
(TS_SUM($return, 5) > 0) ? (TS_SUM($return, 5) * TS_STD($return, 20)) : 0
```

含义：
只在近 5 日累计收益为正时激活，衡量上涨背景下的波动交互。

## 当前组合权重

```text
IntradayRangeComplexityDrift_5D      0.30
Neg_RangeVol_20D                     0.20
Surprise_minus_Volume_Rank           0.20
Net_Buying_Accumulation_Signal_20D   0.15
PosCumRet_Vol_Interaction_5D_20D     0.15
```

组合定义：

```text
combo_score = 0.30*f1 + 0.20*f2 + 0.20*f3 + 0.15*f4 + 0.15*f5
```

在实际实现中，先做横截面 rank，再加权求和。

## 当前观察到的特点

- `IntradayRangeComplexityDrift_5D` 与 `Neg_RangeVol_20D` 有一定相关性，低波动信息可能存在重复
- 信号在弱市中更容易偏向银行、港口、电力、运营商等防守资产
- 舆情层加入后，更适合作为实时排序增强，而不是直接当作历史回测因子

## 当前限制
