---
title: GLM using aster
date: 2017-01-24 13:57:25
tags: [aster,glm]
---

# 分析方法
本文中的分析流程及方法詳如參考資料1，其實本文只有分析工具改成aster而已。

# 下載資料集
下載train.csv。
https://www.kaggle.com/c/titanic/data
下載之後把分隔符號改成'\t'，新檔名為train_new.txt

# 在aster內建立資料表
``` sql
create table titanic (
	PassengerId varchar,
	Survived varchar,
	Pclass varchar,
	Name varchar,
	Sex varchar,
	Age varchar,
	SibSp varchar,
	Parch varchar,
	Ticket varchar,
	Fare varchar,
	Cabin varchar,
	Embarked varchar
) distribute by hash(PassengerId);
```

# 上傳資料到aster
ncluster_loader -h hostname -U username -w password -d database -D '\t' --el-enabled --truncate --skip-row 1 titanic train.txt

# 建立titanic_fmt

``` sql
drop table titanic_fmt;
create table titanic_fmt (
	PassengerId int,
	Survived int,
	Pclass int,
	Name varchar,
	Sex varchar,
	Age double,
	SibSp int,
	Parch int,
	Ticket varchar,
	Fare double,
	Cabin varchar,
	Embarked varchar
) distribute by hash(PassengerId);
```
# 資料前處理

``` sql

/* 
 * coalesce(cast(nullif(Age, '') as double), NULL)的解釋：
 * 1. 如果Age的值不為空字串('')：那就轉成double。
 * 2. 如果Age的值等於空字串('')：那就回傳NULL。
 * 2.1 nullif(Age,'') 表示如果Age的值是空字串('')，那就傳會NULL，而NULL經過cast之後還是NULL，
 * 2.2 coalesce會傳回第一個非NULL的值，如果每個值都是NULL，那就回傳NULL。
 * 2.3 詳見參考資料2
 */
insert into titanic_fmt
select cast(PASSENGERID as int), 
cast(Survived as int), 
coalesce(cast(nullif(Pclass,'') as float), NULL), 
Name, 
Sex, 
coalesce(cast(nullif(Age,'') as double), NULL),  
coalesce(cast(nullif(SibSp,'') as int), NULL),
coalesce(cast(nullif(Parch,'') as int), NULL),
Ticket, 
coalesce(cast(nullif(Fare,'') as double), NULL),
Cabin, 
Embarked from titanic;
```

``` sql
select count(*) from titanic_fmt where Age IS NULL; --共有177筆資料沒有年齡資訊
select sum(Age) as ss , count(*) as mm from titanic_fmt where Age IS NOT NULL; -- 計算年齡的平均值
select avg(Age) from titanic_fmt; --平均年齡是29.69912
update titanic_fmt set Age = 29.69912 where Age IS NULL; --177資料受到影響

select count(*) from titanic_fmt; -- 會有891筆資料
select * from titanic_fmt where embarked = ''; --會有兩筆資料
delete from titanic_fmt where embarked = ''; --刪除這兩筆資料
select count(*) from titanic_fmt; -- 會有889筆資料
```

# 切割訓練集、測試集
``` sql

-- 訓練集
drop table titanic_fmt_train;
create table titanic_fmt_train (
	PassengerId int,
	Survived int,
	Pclass int,
	Name varchar,
	Sex varchar,
	Age double,
	SibSp int,
	Parch int,
	Ticket varchar,
	Fare double,
	Cabin varchar,
	Embarked varchar
) distribute by hash(PassengerId);
insert into titanic_fmt_train select * from titanic_fmt order by PassengerId LIMIT 800;

-- 測試集
drop table titanic_fmt_test;
create table titanic_fmt_test (
	PassengerId int,
	Survived int,
	Pclass int,
	Name varchar,
	Sex varchar,
	Age double,
	SibSp int,
	Parch int,
	Ticket varchar,
	Fare double,
	Cabin varchar,
	Embarked varchar
) distribute by hash(PassengerId);
insert into titanic_fmt_test select * from titanic_fmt where passengerid > 801 ;

```

# 執行GLM

```sql 
drop table titanic_fmt_glm_maxiter10_LOGIT;
SELECT * FROM GLM (
	ON (SELECT 1)
	PARTITION BY 1
	InputTable('titanic_fmt_train')
	OutputTable('titanic_fmt_glm_maxiter10_LOGIT')
	ColumnNames('Survived','Pclass','Sex','Age','SibSp','Parch', 'Fare','Embarked')
	CategoricalColumns('Sex','Embarked')
	Family('LOGISTIC')
	Link('LOGIT')
	Weight('1')
	Threshold('0.01')
	MaxIterNum('10')
);

 predictor               estimate   std_error  z_score  p_value     significance
 ----------------------- ---------- ---------- -------- ----------- ---------------------------------------
 (Intercept)                2.06202   0.515927  3.99673  6.42237E-5 ***
 pclass                    -1.08716   0.151168 -7.19171 6.39933E-13 ***
 sex.female                 2.75682   0.212026  13.0023         0.0 ***
 age                     -0.0372674 0.00819547 -4.54732  5.43341E-6 ***
 sibsp                     -0.29292   0.114642 -2.55509    0.010616 *
 parch                    -0.116576   0.128127 -0.90985    0.362902  
 fare                    0.00152848 0.00235344 0.649467    0.516036  
 embarked.C                0.318786    0.25296  1.26022    0.207588  
 embarked.Q                 0.31613   0.341988 0.924388    0.355285  
 ITERATIONS #                   5.0        0.0      0.0         0.0  
 ROWS #                       800.0        0.0      0.0         0.0 Number of rows
 Residual deviance           709.39        0.0      0.0         0.0 on 791 degrees of freedom
 Pearson goodness of fit    813.471        0.0      0.0         0.0 on 791 degrees of freedom
 AIC                         727.39        0.0      0.0         0.0 Akaike information criterion
 BIC                        769.552        0.0      0.0         0.0 Bayesian information criterion
 Wald Test                   235.81        0.0      0.0         0.0 ***
 Dispersion parameter           1.0        0.0      0.0         0.0 Taken to be 1 for BINOMIAL and POISSON.
 
 
drop table titanic_fmt_glm_maxiter10_CANONICAL;
SELECT * FROM GLM (
	ON (SELECT 1)
	PARTITION BY 1
	InputTable('titanic_fmt_train')
	OutputTable('titanic_fmt_glm_maxiter10_CANONICAL')
	ColumnNames('Survived','Pclass','Sex','Age','SibSp','Parch', 'Fare','Embarked')
	CategoricalColumns('Sex','Embarked')
	Family('LOGISTIC')
	Link('CANONICAL')
	Weight('1')
	Threshold('0.01')
	MaxIterNum('10')
);

predictor               estimate   std_error  z_score  p_value     significance
 ----------------------- ---------- ---------- -------- ----------- ---------------------------------------
 (Intercept)                2.06202   0.515927  3.99673  6.42237E-5 ***
 pclass                    -1.08716   0.151168 -7.19171 6.39933E-13 ***
 sex.female                 2.75682   0.212026  13.0023         0.0 ***
 age                     -0.0372674 0.00819547 -4.54732  5.43341E-6 ***
 sibsp                     -0.29292   0.114642 -2.55509    0.010616 *
 parch                    -0.116576   0.128127 -0.90985    0.362902  
 fare                    0.00152848 0.00235344 0.649467    0.516036  
 embarked.C                0.318786    0.25296  1.26022    0.207588  
 embarked.Q                 0.31613   0.341988 0.924388    0.355285  
 ITERATIONS #                   5.0        0.0      0.0         0.0 Number of Fisher Scoring iterations
 ROWS #                       800.0        0.0      0.0         0.0 Number of rows
 Residual deviance           709.39        0.0      0.0         0.0 on 791 degrees of freedom
 Pearson goodness of fit    813.471        0.0      0.0         0.0 on 791 degrees of freedom
 AIC                         727.39        0.0      0.0         0.0 Akaike information criterion
 BIC                        769.552        0.0      0.0         0.0 Bayesian information criterion
 Wald Test                   235.81        0.0      0.0         0.0 ***
 Dispersion parameter           1.0        0.0      0.0         0.0 Taken to be 1 for BINOMIAL and POISSON.
 
-- LOGISTIC REGRESSION中，LINK參數採用LOGIT跟CANONICAL昰一樣的
```
# 預測

```sql
SELECT * FROM GLMPredict (
	ON titanic_fmt_test
	MODELTABLE('titanic_fmt_glm_maxiter10_CANONICAL')
	ACCUMULATE('PassengerID','Survived','Pclass','Sex','Age','SibSp','Parch', 'Fare','Embarked')
	FAMILY('LOGISTIC')
	LINK('LOGIT')
);

 passengerid survived pclass sex    age      sibsp parch fare     embarked fitted_value
 ----------- -------- ------ ------ -------- ----- ----- -------- -------- -------------------
         810        1      1 female     33.0     1     0     53.1 S          0.908053639982344
         812        0      3 male       39.0     0     0    24.15 S          0.068117295005812
         814        0      3 female      6.0     4     2   31.275 S          0.494181965604929
         818        0      2 male       31.0     1     1  37.0042 C          0.213872454530157
         832        1      2 male       0.83     1     1    18.75 S          0.371901272257794
         834        0      3 male       23.0     0     0   7.8542 S          0.114598630573453
         836        1      1 female     39.0     1     1  83.1583 C          0.910082870446927
         852        0      3 male       74.0     0     0    7.775 S         0.0189770840959977
         854        1      1 female     16.0     0     1     39.4 S          0.956020162455781
         856        1      3 female     18.0     0     1     9.35 S          0.686597963722023
         858        1      1 male       51.0     0     0    26.55 S          0.292102145402033
         870        1      3 male        4.0     1     1  11.1333 S          0.149184872245865
         872        1      1 female     47.0     1     1  52.5542 S          0.839018626811447
         874        0      3 male       47.0     0     0      9.0 S         0.0503415563955056
         876        1      3 female     15.0     0     0    7.225 C          0.790539139294955
         878        0      3 male       19.0     0     0   7.8958 S          0.130621810339474
         890        1      1 male       26.0     0     0     30.0 C          0.591591387913052
略
```

# 參考資料
1. https://datascienceplus.com/perform-logistic-regression-in-r/
2. http://stackoverflow.com/questions/18950951/cast-string-to-number-interpreting-null-or-empty-string-as-0