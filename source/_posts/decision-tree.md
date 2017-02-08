---
title: Decision Tree
date: 2017-02-08 10:30:26
tags: [maching learning, decision tree]
---

## Decision Tree
- 利用劃分節點達到降低不純度的做法。
- 重要議題
	- 決定要用哪個變數做測試條件
	- 決定甚麼是最佳的分割方式
	- 決定何時要停止分割
- 用Greedy方式，分割後的每個節點內，同質性越高越好
	- 選擇能降低最多 Gini 或 Entropy 的切割方式
- 衡量節點不純度的方式
	- Gini Index
	- Entropy
	- Misclassification error


### GINI Index
- 最大值0.5 最小值0

- 算各節點的GINI Index：
$$ GINI(t) = 1 - \sum\_{j}[p(j|t)]^2$$

- 算分割之後的GINI Index：母節點如果分成 $k$ 個子節點，整體的GINI Index就是各子節點乘以節點數佔整體總數的比率之和。

$$ GINI\_{split} = \sum\_{i=1}^{k} \frac{n\_i}{n}GINI(i) $$
$$where\  n\_{i}  = number\ of\ records\ at\ child\ i $$
$$ n = number\ of\ records\ at\ node\ p $$

- Gain：以分割一個節點為例，Gain就是該節點的GINI Index，減去分割後整體的GINI Index值


### Information Entropy

$$ Entropy(t) = \sum\_{j}p(j|t)log\_{2}p(j|t) $$

**Information Gain**
- 計算因為分割降低了多少熵值
- 選擇能降低最多熵值的分割方式
- 在ID3, C4.5使用
- 缺點：很容易產生出很多分割，而每個分割都很小但純度較高。

$$ GAIN\_{split} = Entropy(p) - \sum\_{i=1}^{k}\frac{n\_i}{n} Entropy(i) $$
$$ where\ Parent\ Node\ p\ is\ split\ into\ k\ partitions$$
$$ n\_{i}\ is\ number\ of\ records\ in\ parition\ i$$

** GAIN RATIO **
- 要懲罰information Gain的壞處，因為某些X變數沒有解釋力，只是因為被分成很多段，就被選進來當做分類的屬性
- 所以 SplitINFO 就是懲罰項
- C4.5中有採用

$$ GainRATIO\_{split} = \frac{GAIN\_{Split}}{SplitINFO} $$
$$ SplitINFO = -\sum\_{i=1}^{k}\frac{n\_{i}}{n}log\frac{n\_{i}}{n} $$
$$where\ Parent\ Node,\ p\ is\ split\ into\ k\ partitions;$$
$$n\_{i}\ is\ number\ of\ records\ in\ parition\ i$$

### 停止條件
- 當節點中所有的點都屬於同一個類別
- 當節點中所有的點都有相似的屬性
- Early termination，例如一個節點中，每個類別的數量超過80%

### C4.5
- depth-first construction
- 採用Information Gain or Gain Ratio
- 連續變數在每個節點中均會排序
- 小資料會很容易解釋
- 不適合用在大資料

### Rule Coverage and Accuary
決策樹的樹狀結構可以改變成為一串的規則

規則(rule)
- 規則左手邊→規則右手邊
- 例如 {狀態=單身} → {不會逃稅}。規則左手邊為{狀態=單身}，規則右手邊{不會逃稅}

**Covergage of a rule**
- 滿足規則左手邊的記錄數量，佔全部紀錄的比例

**Strength of a rule**
- 滿足規則左手邊的紀錄裡，有多少比例的紀錄同時滿足規則的左手邊及右手邊
