---
title: aster foreign server 查詢
date: 2017-01-26 11:00:00
tags:
---

## QueryGrid: Aster Hadoop Connectors

### 列出系統上有哪些aster-hadoop connector server的設定

```bash
# 連上queen node下指令
ncli sqlh showservers
```
### 列出aster-hadoop connector server上有哪些資料庫

```bash
beehive=> \extl host=conn_svr_name
 List of databases
       Name
-------------------
 database1
 database2
 database3
```

### 列出aster-hadoop connector server上的資料庫有哪些資料表

```bash
beehive=> \extd host=conn_svr_name database=database1 username=username1
    List of tables
         Name
-----------------------
 table1
 table2
 table3
```

### 找出系統上已經設有哪些foreign server

```bash
# 找出相關的表格
beehive=> \dv nc_system.nc_all_foreign_*
                      List of views
  Schema   |            Name             | Type |  Owner
-----------+-----------------------------+------+----------
 nc_system | nc_all_foreign_servers      | view | owner1
 nc_system | nc_all_foreign_server_privs | view | owner2

beehive=> select * from nc_system.nc_all_foreign_server;
 fsid  |  fsname  | fsowner |                           srvargs                           |
-------+----------+---------+-------------------------------------------------------------+---------
 id    | fs1      | beehive | server ('svr1') dbname ('database1') username ('username1') |

impfunc                 | impargs |     expfunc      | expargs | permissions
------------------------+---------+------------------+---------+-------------
load_from_hcatalog      |         | load_to_hcatalog |         |
```

## 查詢foreign server

### 假設foreign server沒有密碼
```bash
# 如果fs1是hadoop hive server，所以括號的部分打入hive的語法
beehive=> select * from foreign server ($$select * from database.table1 limit 10; $$)@fs1;
```

### 假設foreign server有密碼

```bash
beehive=> select * from foreign server($$select * from database.table1 sample 10;$$)@(fs2 using username('usr') password('pwd'));
```


