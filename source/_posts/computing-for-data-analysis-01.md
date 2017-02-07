---
title: Computing for data analysis (01) 的筆記
date: 2017-02-07 09:18:50
tags: R, class
---

本文是computing for data analysis課程的摘要。也有一些上課的心得。

## Data types and Basic Oprations

### basic or 'atomic' classes
- character
- numeric
- integer
- complex
- logical(True/False)

**The most basic object is a vector**

### Numbers
- as numeric objects
- 1 is a numeric object; 1L is an integer
- NaN represent undefined value: 0/0
- Inf (infinity): 1/0 = Inf and 1/Inf = 0 

### Attributes R objects
- names, dimnames
- dimensions
- class
- length
- other user-definded attributes

### Matrices
- vectors with a dimension attribute. dimension attribute is an ieteger vetor
- 

```R
> m<-matrix(nrow=2,ncol=3)
> dim(m)
[1] 2 3
> attributes(m)
$dim
[1] 2 3
```

### List

```R
> x <- list(1,"a", TRUE, 1 + 4i)
> x
[[1]]
[1] 1

[[2]]
[1] "a"

[[3]]
[1] TRUE

[[4]]
[1] 1 + 4i
```

### NA
```R
> is.na(NAN)
[1] TRUE
> is.nan(NA)
[1] FALSE
```

### Data Frames
- 創建data frame 通常用read.table(), read.csv()
- 或者由matrix來創建 data.matrix()

```R
> x <- data.frame(foo=1:4, bar=c(T,T,F,F))
> nrow(x)
[1] 4
> ncol(x)
[1] 2
```

## Data Type and Basic Operations

### subsetting list
- [ 永遠會傳回跟原本物件一樣class的物件
- [[ 用來截取list或data frame的元素，回傳的物件不一定是list或data frame
- $ 用name來截取list或data frame的元素，很像是[[

```R
> x <- list(foo=1:4, bar=0.6)
> x[1] # 回傳一個list
$foo
[1] 1 2 3 4
> x[[1]] # 回傳vector
[1] 1 2 3 4
> x$bar
[1] 0.6
> x[["bar"]]
[1] 0.6
> x["bar"]
[1] 0.6
```

```R
> x <- list(foo = 1:4, bar = 0.6, baz = "hello")
> x[c(1,3)]
$foo
[1] 1 2 3 4

$baz
[1] "hello"
```

**Subsetting Nestined Elements of a List**
- **[[ take an integer sequnece**

```R
> x <- list(a = list(10,12,14), b = c(3.14, 2.81))
> x[[c(1,3)]]
[1] 14
> x[[1]][[3]]
[1] 14
> x[[c(2,1)]]
[1] 3.14
> x[[2]][[1]]
[1] 3.14
```

```R
> x1 = 1:4; x2 = 1:4 + 10; x3 = 1:4 + 20; x4 = 1:4 + 30
> y <- list( ya = list(x1,x2) , yb = list(x3,x4) )
> y
$ya
$ya[[1]]
[1] 1 2 3 4

$ya[[2]]
[1] 11 12 13 14

$yb
$yb[[1]]
[1] 21 22 23 24

$yb[[2]]
[1] 31 32 33 34

> y[[1]][[2]][[3]]
[1] 13

> y[[c(1,2,3)]]
[1] 13

> y$ya[[2]]
[1] 11 12 13 14
> y$ya[[2]][3]
[1] 13
```

### Removing NA values

```R
> airquality[1:6,]
> good <- complete.case(airquality)
> airquality[good,][1:6,]
```

## Vectorized Operations

```R
> x <- matrix(1:4, 2, 2); y <- matrix(rep(10,4), 2, 2)
> x * y
> x / y
# true matrix multiplication
> x %*% y 
```

## Reading and Writing Data

reading
- read.table, read.csv
- readLines, 從文字檔案讀進來
- source, 讀R code files
- dget, 讀R code files
- load, 讀入廚吋的workspaecs
- unserialize, 從binary form讀入R物件

writing
- write.table,
- writeLines
- dump
- dput
- save
- serialize

使用 read.tables讀大檔時，稍微計算記憶體會不會爆炸掉，指定`colClasses`會讀得比較快，如果檔案裡面沒有註解，那就把`comment.char=""`，這樣也會比較快

```R
# 讀前100行看看型態
> initial <- read.table("datatable.txt", nrows = 100)
> classes <- sapply(initial, class)
> tabAll <- read.table("datatable.txt", colClasses = classes)
```
