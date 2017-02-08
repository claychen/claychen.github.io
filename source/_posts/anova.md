---
title: ANOVA 筆記
date: 2017-02-08 15:11:26
tags: [statistics, anova]
---

## ANOVA 簡介
- ANalysis Of VAriance
- 中文稱「**平均數的變異數分析**」
- 假設兩個組，沒吃藥與有吃藥的平均血壓值有差異，究竟只是抽樣的問題，或真有差異？
- F 檢定的應用

### 三大假設
- 常態性假設：每組反應變數的母體均為常態分配
註

$$ Y\_{i} \sim N(\mu_{i},\sigma), i = 1,2,...k $$

- 同質性假設：每組的母體變異數都相等 (通常用Levene同質性檢定)
$$ \sigma\_{i}^{2} = \sigma^2 $$

- 獨立性假設：抽自各母體的各組隨機樣本互為獨立。

### 公式
- 總變異
	總變異 = 組間變異 + 隨機變異
    $$ SST = SSF + SSE $$
- 因子變異(組間變異)

**符號**
- $k$ 指的是組數
- $n$ 指的是組內的樣本數

$$ SSF = \sum\_{i=1}^{k}\sum\_{j=1}^{n\_i}(\overline{Y\_{i}} - \overline{\overline{Y}})^2 $$
或
$$ SSF = \sum\_{i=1}^{k}n\_{i}(\overline{Y\_{i}} - \overline{\overline{Y}})^2 $$

- 隨機變異(組內變異)
$$ SSE = \sum\_{i=1}^{k}\sum\_{j=1}^{n\_i}(Y\_{ij} - \overline{Y\_{i}})^2 $$
或
$$ SSE = \sum\_{i=1}^{k}(n-1)S\_{i}^2 $$

舉例：SSF有服藥沒服藥造成的差異，SSE無法被服藥解釋的部分，均為隨機變異

- 平均因子變異

$$ MSF = \frac{SSF}{k-1}$$

- 平均隨機變
$$ MSE = \frac{SSE}{\sum{n\_{i}} - k}$$

- F 檢定統計量
$$ F = \frac{MSF}{MSE} \sim F\_{k-1,\sum{n\_{i}}-k} $$


**決策法則**

- 若 $ F > \frac{MSF}{MSE} \sim F\_{k-1,\sum{n\_{i}}-k,\alpha} $，則拒絕 $H_{0}$

- 若 $ F \le \frac{MSF}{MSE} \sim F\_{k-1,\sum{n\_{i}}-k,\alpha} $，則接受 $H_{0}$

### 範例
```R
Wid<- aov(Petal.Width~Species, data=iris)
summary(Wid)

# 殘差常態性檢定
shapiro.test(Wid$residuals)
	Shapiro-Wilk normality test

data:  Wid$residuals
W = 0.97217, p-value = 0.003866
# Null Hypothesis：資料是常態性分配
# 因為P值很小，所以要棄卻常態性假設

# 殘差均質性檢定 (Variance相同嗎？)
bartlett.test(Wid$residuals, Species) 
	Bartlett test of homogeneity of variances

data:  Wid$residuals and Species
Bartlett's K-squared = 39.213, df = 2, p-value = 3.055e-09
# Null hypothesis：每群資料的變異數是均質的
# 因為p值很小，所以殘差不均質 (non-homogeneous)
```

## 整體流程
- 參考自[[教學] [統計] ANOVA變異數分析 小筆記](http://belleaya.pixnet.net/blog/post/30754486)
![png](1388591242-2417805686.png)

**參考資料**
交通大學工業工程與管理學系王志軒教授講義
[[教學] [統計] ANOVA變異數分析 小筆記](http://belleaya.pixnet.net/blog/post/30754486)
[R統計分析與資料探勘入門—以鳶尾花資料集為例](http://www.cc.ntu.edu.tw/chinese/epaper/0031/20141220_3105.html)
[Performing Bartlett’s test in R](http://www.instantr.com/2012/12/12/performing-bartletts-test-in-r/)
[R筆記–(5)初聲試啼-簡單的資料分析(迴歸分析)](https://rpubs.com/skydome20/R-Note5-First_Practice)
[ANOVA assumption normality/normal distribution of residuals](http://stats.stackexchange.com/questions/6350/anova-assumption-normality-normal-distribution-of-residuals)