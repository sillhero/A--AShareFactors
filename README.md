# GitHub Repo Intro

## 仓库一句话描述

一个面向 A 股日频研究的组合因子实验仓库：把价量因子、收盘后选股、FTShare 舆情增强和可复现回测串成一条可迭代的研究流程。

## 仓库简介（短版）

这个仓库记录了一套正在持续迭代的 A 股日频策略研究流程。当前核心是一个 `Top5` 组合因子框架：先从价量行为里筛出若干候选因子，再通过固定权重组合生成收盘后观察池，最后叠加 FTShare 和外部资讯网站的舆情信息，辅助第二天的观察与交易决策。

公开它的目的，不是展示“稳定赚钱的黑盒策略”，而是把研究过程摊开给社区看：因子表达式、组合逻辑、权重设定、舆情层使用方式、以及当前还没解决完的中性化和样本外稳健性问题。欢迎大家一起挑毛病、提改法、做复核。

## 仓库简介（README 首页版）

QuantaAlpha is an open research workspace for A-share daily factor experiments.

This repository currently focuses on a practical workflow:

1. mine and curate price-volume factors,
2. combine a small set of factors into a post-close watchlist strategy,
3. overlay FTShare and external news sentiment for next-session ranking,
4. iterate in public with feedback from other researchers and traders.

The current public package centers on a `Top5` combo factor strategy for `CSI300`, along with its factor definitions, combination weights, and research notes. The goal is not to present a polished “alpha product”, but to open the research process itself: what we are testing, what seems to work, what is probably redundant, and where the strategy is still fragile.

If you are interested in A-share factor research, post-close portfolio construction, factor de-duplication, or how to use sentiment as a ranking overlay rather than a backtest illusion, feedback is very welcome.

## 建议放在 GitHub 仓库描述栏

可选版本 1：

```text
A-share daily factor research workspace: Top5 combo factors, post-close watchlists, FTShare sentiment overlay, and open iteration.
```

可选版本 2：

```text
Open A-share factor research repo for post-close watchlist strategies, price-volume combo factors, and sentiment-assisted ranking.
```

## 建议放在 README 开头的中文导语

```text
这是一个面向 A 股日频研究的开源实验仓库，当前重点探索“收盘后组合因子选股 + 次日舆情增强排序”的策略流程。仓库公开的不只是结果，更是研究过程本身：因子定义、组合权重、去重思路、数据口径、以及还没彻底解决的问题。欢迎社区一起复核、质疑和改进。
```

## 建议对外说明的边界

- 这是研究仓库，不是收益承诺
- 当前更接近“观察池策略”，不是全自动交易系统
- 舆情层主要用于实时排序增强，不等同于严格可回放的历史因子
- 当前公开版本仍缺少完整的行业/市值中性化处理
