---
title: Advanced R Week 2
date: 2017-02-11 08:04:26
tags: [R, coursera]
---

# Advanced R Week 2
這是[Advanced R](https://www.coursera.org/learn/advanced-r/)第二周的課程內容整理，很有趣。

## Functional Programming
Functional Programming專注在四件事情
- Data(numbers, strings etc)
- Variables(function arguments)
- Functions
- Function Applications(evaluating functions given arguments and/or data)

範例，Function傳回Function

```R
add_maker <- function(n) {
	function(x) {
    	n + x
    }
}

add2 <- add_maker(2)
add3 <- add_maker(3)

> add2(5)
[1] 7
> add3(5)
[1] 8
```
### Map

這類型得函數會套用到資料結構中每個元素，例如vector/list中每個元素，然後回傳每個元素套用後的結果。

例如 `purrr`套件中的
`map_lgl()` 回傳vecotors of logical values
`map_chr()` 回傳strings
`map_dbl()` 回傳numbers
```R
library(purrr)

map_chr(c(5,4,3,2,1), function(x) {
	c("one","two","three","four","five")[x]
})
[1] "five"  "four"  "three" "two"   "one" 

map_lgl(c(5,4,3,2,1), function(x){
	x>3
})
[1]  TRUE  TRUE FALSE FALSE FALSE
```
`map_lgl(c(1,2,3,4,5), gt, b=3)` 其中 `gt`吃a,b兩個參數，如果 a > b則回傳TRUE，反之FALSE。在這個例子中，b=3是gt的參數。

`map_if` 滿足第二個函數演算結果得就執行第三個函數
```R
map_if(1:5, function(x) {
	x%%2 == 0
	},
    function(y) {
    	y^2
    }) %>% unlist()
[1] 1 4 3 16 5
```
```R
map_at(seq(100,500,100), c(1,3,5), function(x){x-10}) %>% unlist()
[1] 90 200 290 400 490
```
`map2 family` 對兩組傳入的資料作`map`，這兩組資料長度要一樣長。
```R
map2_chr(letters, 1:26, paste)
[1] "a 1" "b 2" ... 不想寫了反正26個XD
```
```R
pmap_chr(list(
			list(1,2,3),
			list("one", "two", "three"),
			list("uno", "dos", "tres")
         ), paste)
[1] "1 one uno" "2 two dos" "3 three tres"
```

### Reduce

```R
reduce(c(1,3,5,7), function(x,y) {x+y})
[1] 16
```
```R
reduce(letters[1:4], function(x,y){paste0(x,y)})
[1] "abcd"

reduce_right(letters[1:4], function(x,y){paste0(x,y)})
[1] "dcba"
```
### search
找東西
```R
contains(letters, 'a')
[1] TRUE

# detect 只會傳回第一個滿足條件得數字
detect(20:40, function(x){ x>22 && x %% 2 ==0 })
[1] 24

detect_index(20:40, function(x) { x>22 && x %% 2 ==0})
[1] 5
```

### Filter
`keep` , `discard`,  `every` and `some`
```R
keep(1:20, function(x){x%%2==0})
[1] 2  4  6  8 10 12 14 16 18 20
discard(1:20, function(x){x%%2==0})
[1] 1  3  5  7  9 11 13 15 17 19
＃ 是不是所有得元素都滿足條件
every(1:20, function(x){x%%2==0})
[1] FALSE
# 有沒有哪個元素滿足條件得？
some(1:20, function(x){x%%2==0})
[1] TRUE
```

### Compose
```R
n_unique <- compose(length, unique)
# n_unique(x) 得效果跟 length(unique(x))一樣

# 以下三者效果相同
n_unique(rep(1:5,1:5))
[1] 5
rep(1:5,1:5) %>% unique %>% length
[1] 5
unique(length(rep(1:5, 1:5)))
```
### Partial Application
`partial(function(arg1,arg2,arg3))`
可以讓function指定特定參數得值，partial製作一個新的函數，吃未被指定值的參數

```R
multi_three_n <- function(x,y,x) {x*y*z}

multi_by_15 <- partial(multi_three_n, x=3,y=5)

multi_by_15(z=4)
[1] 60
```
### Side Effects
`walk`會呼叫f引起side effect，然後回傳input。
```
walk(c("Friends, Romans, countrymen,",
       "lend me your ears;",
       "I come to bury Caesar,", 
       "not to praise him."), message)
Friends, Romans, countrymen,
lend me your ears;
I come to bury Caesar,
not to praise him.R
```
### Recursion

```R
# loop version
vector_sum_loop <- function(v) {
	result <- 0
    for( i in v ) {
    	result <- result + i
    }
    result
}
vector_sum_loop(c(5,40,91))
[1] 136

# recursive version
vector_sum_rec <- function(v) {
	if(length(v)==1) {
    	v
    } else {
    	v[1] + vector_sum_rec(v[-1])
    }
}
vector_sum_rec(c(5,40,91))
[1] 136

fib <- function(n) {
	stopifnot(n>0)
    if(n==1) {
    	0
    } else if(n==2) {
    	1
    } else {
    	fib(n-1) + fib(n-2)
    }
}
map_dbl(1:10, fib)
```
紀錄計算過程得資料，避免重複計算
```R
fib_tbl <- c(0,1,rep(NA,23))

fib_mem <- function(n) {
	stopifnot(n>0)
    if (!is.na(fib_tbl[n])) {
    	fib_tbl[n]
    } else {
    	fib_tbl[n-1] <<- fib_mem(n-1)
        fib_tbl[n-2] <<- fib_mem(n-2)
        fib_tbl[n-1] + fib_tbl[n-2]
    }
}
map_dbl(1:12, fib_mem)
```
計算不同的recursion方法的時間差異
```R
library(purrr)
library(microbenchmark)
library(tidyr)
library(magrittr)
library(dplyr)

fib_data <- map(1:10, function(x){microbenchmark(fib(x), times = 100)$time})
names(fib_data) <- paste0(letters[1:10], 1:10)
fib_data <- as.data.frame(fib_data)

fib_data %<>%
  gather(num, time) %>%
  group_by(num) %>%
  summarise(med_time = median(time))

memo_data <- map(1:10, function(x){microbenchmark(fib_mem(x))$time})
names(memo_data) <- paste0(letters[1:10], 1:10)
memo_data <- as.data.frame(memo_data)

memo_data %<>%
  gather(num, time) %>%
  group_by(num) %>%
  summarise(med_time = median(time))

plot(1:10, fib_data$med_time, xlab = "Fibonacci Number", ylab = "Median Time (Nanoseconds)", pch = 18, bty = "n", xaxt = "n", yaxt = "n")
axis(1, at = 1:10)
axis(2, at = seq(0, 350000, by = 50000))
points(1:10 + .1, memo_data$med_time, col = "blue", pch = 18)
legend(x="topleft", y=NULL, c("Not Memorized", "Memoized"), pch = 18, col = c("black", "blue"), bty = "n", cex = 1, y.intersp = 1.5)
```
## Expression & Environments

### Expressions
- 利用`quote`存算式
- `parse`解出字串儲存得算式
- 用`eval`計算算式的結果。
- `call` 建立算式

```R
two_plus_two <- quote(2+2)
two_plus_two # 這是一個call物件
[1] 2 + 2

eval(two_plus_two)
[1] 4

# 把存在字串中的算式變成算式
parse(text = '2+2') # 這是一個expression
[1] expression(2+2)
eval(parse(text='2+2'))
[1] 4
```
像是處理`list` 一樣地處理算式，替換算式的內容。
```R
sum_expr <- quote(sum(1,5))
sum_expr[[1]] # sum
sum_expr[[2]] # 1
sum_expr[[3]] # 5
sum_expr[[1]] <- quote(paste0)
sum_expr[[2]] <- 4
sum_expr[[3]] <- 6
eval(sum_expr)
[1] "46"

eval(call('sum', 40 ,50))
[1] 90
```

利用`match.call`在函數中擷取函數的輸入參數
```R
args_of_func <- function(...) {
	expr <- match.call()
    cat("Length of Expr:", length(expr), '\n')
    if(length(expr) > 1) {
    	eval(expr[[2]])
    } else {
    	expr
    }
}

> args_of_func()
Length of Expr: 1
args_of_func()
> args_of_func(2 * 2 + 100)
Length of Expr: 2
[1] 104
```

### Environments
`new.env()`產新的Environment
```R
my_new_env <- new.env()
# 指定Enrivonments的屬性值
my_new_env$x <- 4
assign("y", 9, envir = my_new_env)
# 取得Enrironment中得屬性值
get("y", envir = my_new_env)
my_new_env_$y
```
`search` 列出environment的串列

```R
search()
[1] ".GlobalEnv" "package:magrittr" "package:tidyr" ...
# 這表市.GlobalEnv的parents是package:magrittr，package:magrittr的parent是package:tidyr...以此類推
```
Environment跟函數息息相關，當執行函數時，會產生Execution Environment。我們可以在Execution Environment中存放變數，當變數找不到時，會往去parent Environment找，在以下的案例中，就是往Global Environment去找。

```R
Example 1
y <- 10
my_func() {
	# Execution Environment
	y <- 100
    return(y)
}

my_func()
[1] 100
---------------------
Example 2
y <- 10
my_func() {
	return(y)
}
# 會去parent environment找變數y的值
my_func()
[1] 10
---------------------
Example 3
# 設定Global Environment中得變數
x <- 100
my_func() {
	x <<- "wow"
}
my_func()
x
[1] "wow"
---------------------
Example 4
my_func() {
	x <<- "magic!"
}
exists("x")
[1] FALSE
my_func()
exists("x")
[1] TRUE
x
[1] "magic!"
```

## Error Handling and Generation
`error` 當程式遇到非預期的使用狀態時，就會產生`error`
`warning` 提醒你程式有錯，應該要檢查一下
`message` 印出訊息至R Console，產生`message`的機制與`error`, `warning`相似。

### Generating Errors

```R
a_func <- function() {
	stop("something bad~")
}
a_func()
[1] Error in a_func(): something bad~

b_func <- function() {
	stopifnot(n <=0)
}
b_func(5)
[1] Error: n <=0 is not TRUE

# 產生 warning, message
warning('creating warning')
message('this is a short love message')
```

### Try Catch

```R
beera <- function(expr) {
	tryCatch(expr,
    	error = function(e) {
        	message("An error occured\n", e)
        },
        warning = function(w) {
        	message("A warning occured\n", w)
        },
        finally = {
        	message("finally!")
        })
}

berra({2+2}) 
# 印出 expr 的演算結果，以及finally訊息
berra({2+"two"})
# 印出 error 訊息，以及finally訊息
berra({as.numeric(c(2,"two"))})
# 印出 warning 訊息，以及finally訊息
```

**不要產生太多的Error，會有效能問題**
案例
```R
# 好處是可以處理多種得資料型態，反正只要不是整數且%%2不為零，就回傳FALSE。
is_even_error <- function(n) {
	tryCatch(n%%2==0,
    	error = function(e){
        	FALSE
        })
}
is_even_error(222)
[1] TRUE
is_even_error("aaa")
[1] FALSE
```
但也可以不用`tryCatch`達到同樣的結果
利用 `&&` 達成 **short circuiting**
```R
# 好處是當is.numeric()回傳FALSE，後面的算式也不用做了，節省時間。
is_even_check <- function(n) {
	is.numeric(n) && n %% 2 == 0
}
is_even_check(222)
[1] TRUE
is_even_check("aaa")
[1] FALSE
```
比較兩者效能`tryCatch`慢非常多倍。
```R
library(microbenchmark)
microbenchmark(sapply(letters,is_even_check))
Unit: microseconds
                           expr    min      lq    mean  median
 sapply(letters, is_even_check) 45.721 48.2405 71.4882 75.2625
    uq     max neval
 82.19 179.777   100
microbenchmark(sapply(letters,is_even_error))
Unit: microseconds
                           expr     min       lq     mean
 sapply(letters, is_even_error) 635.915 651.0795 758.6887
  median       uq      max neval
 673.392 695.7405 5552.603   100
```
