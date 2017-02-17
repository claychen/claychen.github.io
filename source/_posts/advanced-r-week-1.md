---
title: Advanced R Week 1
date: 2017-02-11 08:03:26
tags: [R, coursera]
---

# Advanced R Week 1

這是[Advanced R](https://www.coursera.org/learn/advanced-r/)第一周的課程內容整理，很有趣。


## FUNCTION


>**To understand computations in R, two slogans are helpful:
Everything that exists is an object.
Everything that happens is a function call.
— John Chambers**

- 你可以把function當作參數傳到另一個fucntion

## A Great Function Example!!!!!!!

```R
library(readr)
# read_csv
library(dplyr)

if(!file.exists("data/2016-07-20.csv.gz")) {
	download.file("http://cran-logs.rstudio.com/2016/2016-07-20.csv.gz", "data/2016-07-20.csv.gz")
}
cran <- read_csv("data/2016-07-20.csv.gz",col_types="ccicccccci")
# filter 是dplyr的函數
cran %>% filter(package == "filehash") %>% nrow
```

### Function with default value
課程中改寫上例，滿漂亮的

```R
library(dplyr)
library(readr)

## pkgname: package name (character)
## date: YYYY-MM-DD format (character)
num_download <- function(pkgname, date='2016-07-20') {
        ## Construct web URL
        year <- substr(date, 1, 4)
        src <- sprintf("http://cran-logs.rstudio.com/%s/%s.csv.gz",
                       year, date)

        ## Construct path for storing local file
        dest <- file.path("data", basename(src))

        ## Don't download if the file is already there!
        if(!file.exists(dest))
                download.file(src, dest, quiet = TRUE)

        cran <- read_csv(dest, col_types = "ccicccccci", progress = FALSE)
        cran %>% filter(package == pkgname) %>% nrow
}
```

### Refactoring!
而且不用下載檔案兩次了

```R
check_for_logfile <- function(date) {
        year <- substr(date, 1, 4)
        src <- sprintf("http://cran-logs.rstudio.com/%s/%s.csv.gz",
                       year, date)
        dest <- file.path("data", basename(src))
        if(!file.exists(dest)) {
                val <- download.file(src, dest, quiet = TRUE)
                if(!val)
                        stop("unable to download file ", src)
        }
        dest
}

num_download <- function(pkgname, date = "2016-07-20") {
        dest <- check_for_logfile(date)
        cran <- read_csv(dest, col_types = "ccicccccci", progress = FALSE)
        cran %>% filter(package == pkgname) %>% nrow
}
```
### Dependency Checking
```R
check_pkg_deps <- function() {
	if(!requare(readr)) {
    	message("installing the 'readr' package")
        install.packages("readr")
    }
    if(!require(dplyr)) {
    	stop("the 'dplyr' package needs to be installed first")
    }
}
```
### Vectorization
通常R常見得使用模式要讓FUNCTION吃Vector，這邊告訴我們如何讓R能吃Vector及，最重要的，處理Vector。R這樣得特性，在這個例子中，可以讓我們一次傳入多個package，可以一次傳回多個package的資訊。
```R
num_download <- function(pkgname, date = "2016-07-20") {
	check_pkg_deps()
    dest <- check_for_logfile(date)
    cran <- read_csv(dest, col_types="ccicccccci", progress=FALSE)
    cran %>% filter(package %in% pkgname) %>%
    	group_by(package) %>%
        summarize(n=n())
}
# num_download 原本傳回integer，現在傳回data.frame
num_download(c("filehash", "weathermetrics"))
# A tibble: 2 × 2
         package     n
           <chr> <int>
1       filehash   179
2 weathermetrics     7
```

### Argument Checking

hmmm, this is argument checking using `is.character` and `length`
```R
num_download <- function(pkgname, date = "2016-07-20") {
        check_pkg_deps()

        ## Check arguments
        if(!is.character(pkgname))
                stop("'pkgname' should be character")
        if(!is.character(date))
                stop("'date' should be character")
        if(length(date) != 1)
                stop("'date' should be length 1")

        dest <- check_for_logfile(date)
        cran <- read_csv(dest, col_types = "ccicccccci",
                         progress = FALSE)
        cran %>% filter(package %in% pkgname) %>%
                group_by(package) %>%
                summarize(n = n())
}
```

### When Sould I Write a Function
這個心法滿不錯的，先用`code`解決當前問題，再用`function`進行模組化以利處理廣泛一些的問題，過程中會持續重構，最後改用`package`做統整。

>Developing functions is a key aspect of programming in R and typically involves a bottom-up process.

- Code is written to accomplish a specific task or a specific instance of a task.
- The code is examined to identify key aspects that may be modified by other users; these aspects are abstracted out of the code and made arguments of a function.
- Functions are written to accomplish more general versions of a task; specific instances of the task are indicated by setting values of function arguments.
- Function code can be re-factored to provide better modularity and to divide functions into specific sub-tasks.
- Functions can be assembled and organized into R packages.

## 實作部份的摘要

```R
evalute <- function(func,args) {
	func(args)
}
evalute(sd, c(1.1,2.2,3.3))
```

- `paste (..., sep = " ", collapse = NULL)` 在 ellipsis之後出現得argument都必需要有default value.

#### ellipses
```R
telegram <- function(...) {
	paste("START", ..., "STOP")
}
```
```R
mad_lib <- function(...) {
	args <- list(...)
    place <- args[["place"]]
    adjective <- args[["adjective"]]
    noun <- args[["noun"]]
    pates("News from" , place , "today where", adjective, "students took to the streets in protest of the new", noun, "being installed on campus)
}
```

#### self-maded binary operator
```R
"%mult_add_one%" <- function(left, right){
   left * right + 1
}
4 %mult_add_one% 5
# 21

"%p%" <- function(left, right){ # Remember to add arguments!
  paste(left, right)
}
'I' %p% 'love' %p% 'R!'
```
