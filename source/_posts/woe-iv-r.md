---
title: WOE and IV in R
date: 2017-02-07 16:28:50
tags: R, class
---

閱讀完參考資料的心得
- [[R] 相見太晚，回頭是岸，分析WOE和IV值的神器套件](http://blog.bryanbigdata.com/2014/10/r-woeiv.html)
- [ Weight of Evidence (WoE) Introductory Overview](http://documentation.statsoft.com/STATISTICAHelp.aspx?path=WeightofEvidence/WeightofEvidenceWoEIntroductoryOverview)



## WOE
- Weighted of Evidence
- 變項中各選項對於最後結果(好或壞)的影響
- WoE越高風險越低
- $ Distr\ Goods $ 為一比率，分母是所有的正例數，分子是該變量的正例數。

$$ WoE = [ln(\frac{Distr\ Goods}{Distr\ Bads} )] * 100 $$

## IV
- Information Value
- 例如性別變數有男女兩類，i就等於2
- IV代表的是單一變項對於結果的影響力
- 通常 IV > 0.3代表變項對結果有強烈的影響力, 0.1~0.3代表中度影響力

$$ IV = \sum\_{i=1}^{n}[(Distr\ Goods\_{i} - Distr\ Bads\_{i}) * ln(\frac{Distr\ Goods\_{i}}{Distr\ Bads\_{i}})] $$
