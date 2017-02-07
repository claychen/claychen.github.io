---
title: Git分支模型
date: 2017-02-05 16:31:36
tags: git
---

本文為閱讀[A successful Git branching model](http://nvie.com/posts/a-successful-git-branching-model/)的摘要

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**

- [整體概況](#%E6%95%B4%E9%AB%94%E6%A6%82%E6%B3%81)
- [feature branch](#feature-branch)
  - [產生 feature 分支](#%E7%94%A2%E7%94%9F-feature-%E5%88%86%E6%94%AF)
  - [合併回去 develop](#%E5%90%88%E4%BD%B5%E5%9B%9E%E5%8E%BB-develop)
  - [結束 feature 分支](#%E7%B5%90%E6%9D%9F-feature-%E5%88%86%E6%94%AF)
- [release branch](#release-branch)
  - [產生 release 分支](#%E7%94%A2%E7%94%9F-release-%E5%88%86%E6%94%AF)
  - [結束 release 分支](#%E7%B5%90%E6%9D%9F-release-%E5%88%86%E6%94%AF)
- [hotfix 分支](#hotfix-%E5%88%86%E6%94%AF)
  - [產生 hotfix 分支](#%E7%94%A2%E7%94%9F-hotfix-%E5%88%86%E6%94%AF)
  - [結束 hotfix 分支](#%E7%B5%90%E6%9D%9F-hotfix-%E5%88%86%E6%94%AF)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## 整體概況

![git-model](git-model.png)

遠端儲存庫的分支，產品在就會一直在的分支
- master
- develop

![main-branches](main-branches.png)

origin/master
- HEAD reflects product ready state
- 永遠存在的分支
- HEAD指向最新產品發行版

origin/develop
- HEAD reflects a state with the latest delivered development changes for the next release.
- 永遠存在的分支
- HEAD指向下個正式版的最新開發版 

支持develpe, master的分支
- feature
- release
- hotfix

## feature branch
- 從develop開過來的分支
- 一定要合併回去develop，或者丟棄這個feature分支
- feature branch name不可以是master, develop, release-*, hotfix-*
- feature通常指存在於開發者的儲存庫，不會存放在origin儲存庫

![fb](fb.png)

### 產生 feature 分支
```bash

git checkout -b myfeature develop
# 這時會切換到新分支 "myfeature"
```
### 合併回去 develop 
``` bash
＃ 切換倒develop分支
git checkout develop

# feature分支合併至develop分支
# --no-ff 表就算可以用fast-forward合併，但該旗標會讓合併時永遠會產生一個新的commit
# 這樣做的好處是避免失去該feature分支的commit資訊。
# 當要回復feature時很方便。
git merge --no-ff myfeature
```
![merge-without-ff](merge-without-ff.png)

### 結束 feature 分支
```bash
# 砍掉 feature 分支
git branch -d myfeature

# 推送develop分支到origin
git push origin develop
```

## release branch
- 從develop開出來的分支
- 一定要合併回去develop以及master
- 名字得取法是release-xxx
- 執行產品打磨作業
- 當develop快要完成了，所有要發行的feature分支會合併到develop分支，這時再分出release分支
- 在產品發布前做修補用，提供minor bug fix，準備產品發行版的meta-data(版號, 建置日期等)
- 採用release branch的好處是可以讓develop較為乾淨。

### 產生 release 分支

```bash
# 確定下一個版本是1.2之後
git checkout -b release-1.2 develop

# 修改產品的版本
echo 'version 1.2' >> version.md

git commit -a -m "Bumped version number to 1.2"

# 在這之後不會在release中加入新的feature，所有得新feauter留待下一版
```

### 結束 release 分支

第一步，合併至master分支
```bash
# 切換到master分支，準備要用master合併release-1.2分支
git checkout master

# 合併release-1.2至master分支
git merge --no-ff release-1.2

# 給予一個版次
git tag -a 1.2 
```
第二步，合併至develop分支
```bash
# 切換至develop分支，準備要用develop合併relase-1.2分支
git checkout develop

# 合併relase-1.2至develop分支
git merge --no-ff release-1.2

# 如果有出現merge conflict再修改衝突得檔案就好
```

```bash
# 因為release-1.2的工作已經完成了，所以就可以刪除
git branch -d release-1.2
```

## hotfix 分支
- 從master開出來的分支
- 一定要合併回去develop以及master
- 名字的取法是hotfix-xxx
- 很像是release分支做得事情，只是不像release分支可以先做計畫
- 通常是因為發行版出了需要立即修復的問題時，創立出來以修補問題的分支
- 獨立分支的目的是讓救火隊可以產製hotfix，而開發組可以繼續開發

![hotfix](hotfix.png)

### 產生 hotfix 分支

```bash
# 收到要求需要進行hotfix，故修補1.2版的產品問題，故從master產出hotfix分支
git checkout -b hotfix-1.2.1 master

# 修改版本編號至1.2.1
echo 'version 1.2.1' >> version.md

# 提交
git commit -a -m "Bumped version number to 1.2.1"
```
修補完bug之後，提交修補的結果

```bash
git commit -m "Fixed severe production problem"
```
### 結束 hotfix 分支
完成 hotfix 之後，要把 hotfix 分支合併至 master 以及 develop 兩個分支。

先合併至 master
```bash
# 切換到 master
git checkout master
# 合併
git merge --no-ff hotfix-1.2.1
# 在master分支中建立個標籤
git tag -a 1.2.1
```
接下合併至 develop
```bash
# 切換到 develop 分支
git checkout develop
# 合併
git merge --no-ff hotfix-1.2.1
```
hotfix 但合併至 develop 時有個例外。當 release 分支存在時，應該是要合併至 release 分支，而不是 develop 分支。依照前述得規則，當 release 分支結束後，也是會合併到 develop 分支。

當然，如果有必要立即將 hotfix 合併至 develop ，也可以立即將 hotfix 合併至 develop 分支。

最後，移除 hotfix 分支
```bash
git branch -d hotfix-1.2.1
```


