---
title: asterR 設定
date: 2017/01/26 15:00:00
tags: [aster,r]
---

# 安裝R環境

## 檢視AsterR的版本

建議client與server要安裝一樣版本
``` shell
ncli apm show R --localconfig
R installation status
+------------+-----------+-----------+
| Node Ip    | Node Type | R Version |
+------------+-----------+-----------+
| IP1        | Queen     | 3.1.x     |
| IP2        | Worker    | 3.1.x     |
+------------+-----------+-----------+
3 rows
```
## 安裝R & RStudio
- 因為client端運行的環境在windows，所以以下的軟體都抓windows版本的，其他的OS當然就抓其他OS的版本囉。
- 下載安裝R軟體：https://cran.r-project.org/
- 下載安裝R Studio (Desktop)： https://www.rstudio.com/products/rstudio/download/

## 安裝TeradataAsterR
- 下載Aster Client Tools for Windows：http://downloads.teradata.com/download/aster/aster-client-tools-for-windows
- 抓了AsterClients__windows_x8664.06.22.01.00.zip，解壓縮之後裡面有TeradataAsterR_06.22.00.00.zip。
- 開啟RStdio，選擇Tools-> Intall Packages->Install from選擇Package Archive File(.zip;.tar.gz)，路徑指向TeradataAsterR_06.22.00.00.zip。

# 設定ODBC

## 前置作業
### 注意 
- 安裝ODBC之前一定要先裝Microsoft Visual C\++ Redistributable Pacakges 而且要指定的版本，不然可能會踩雷。這個部分應該要去讀該版本的Aster Client Guide。 
- 以Aster 6.20為例，要安裝的是Microsoft Visual C\++ 2008 Redistributable Packages以及Microsoft Visual C\++ 2012 Redistributable Packages，如果要裝在x86的伺服器上，就抓x86的版本。如果要裝在x64的伺服器上，就抓x64的版本。

### 下載位置
- Microsoft Visual C\++ 2008 Redistributable Packages (x64)：https://www.microsoft.com/zh-tw/download/details.aspx?id=15336
- Microsoft Visual C\++ 2012 Redistributable Packages：https://www.microsoft.com/zh-tw/download/details.aspx?id=30679

## 安裝 Aster ODBC Driver
從Aster Client Tools for Windows中抓出nClusterODBCInstaller_x64.msi。安裝nClusterODBCInstaller_x64。

## 設定ODBC
在控制台→系統管理工具→資料來源，選擇使用這資料來源名稱，新增Aster ODBC Driver，輸入基本資訊即可。

設定資訊
- DSN ：例如AsterDSN
- port：預設是2406

# 連接測試

```R
library(RODBC)
library(TeradataAsterR)
ta.connect('AsterDSN')
ta.data.frame('select * from database1.titanic LIMIT 1', sourceType='query')
# 如果正常列出表格中的資訊，那就成功囉！
```