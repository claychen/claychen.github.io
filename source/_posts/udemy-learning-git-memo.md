---
title: Git 課程學習筆記
date: 2017-02-04 21:30:47
tags: git
---



# 掌握Git & Github 程式時光機
[udemy課程網址](https://www.udemy.com/learninggit/learn/v4/overview)以下為課程筆記，內容版權為原課程所有。

[TOC]
<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents** 

- [掌握Git & Github 程式時光機](#%E6%8E%8C%E6%8F%A1git--github-%E7%A8%8B%E5%BC%8F%E6%99%82%E5%85%89%E6%A9%9F)
  - [整體流程圖](#%E6%95%B4%E9%AB%94%E6%B5%81%E7%A8%8B%E5%9C%96)
  - [上傳流程](#%E4%B8%8A%E5%82%B3%E6%B5%81%E7%A8%8B)
    - [產生本地端數據庫 (Local Repository)](#%E7%94%A2%E7%94%9F%E6%9C%AC%E5%9C%B0%E7%AB%AF%E6%95%B8%E6%93%9A%E5%BA%AB-local-repository)
    - [在工作目錄內新增檔案](#%E5%9C%A8%E5%B7%A5%E4%BD%9C%E7%9B%AE%E9%8C%84%E5%85%A7%E6%96%B0%E5%A2%9E%E6%AA%94%E6%A1%88)
    - [從工作目錄傳到索引](#%E5%BE%9E%E5%B7%A5%E4%BD%9C%E7%9B%AE%E9%8C%84%E5%82%B3%E5%88%B0%E7%B4%A2%E5%BC%95)
    - [把變更加入到本地儲存庫](#%E6%8A%8A%E8%AE%8A%E6%9B%B4%E5%8A%A0%E5%85%A5%E5%88%B0%E6%9C%AC%E5%9C%B0%E5%84%B2%E5%AD%98%E5%BA%AB)
  - [不想上傳變更了](#%E4%B8%8D%E6%83%B3%E4%B8%8A%E5%82%B3%E8%AE%8A%E6%9B%B4%E4%BA%86)
    - [忽略追蹤](#%E5%BF%BD%E7%95%A5%E8%BF%BD%E8%B9%A4)
    - [不追蹤原本在索引中的檔案](#%E4%B8%8D%E8%BF%BD%E8%B9%A4%E5%8E%9F%E6%9C%AC%E5%9C%A8%E7%B4%A2%E5%BC%95%E4%B8%AD%E7%9A%84%E6%AA%94%E6%A1%88)
    - [從本地儲存庫還原檔案至工作目錄](#%E5%BE%9E%E6%9C%AC%E5%9C%B0%E5%84%B2%E5%AD%98%E5%BA%AB%E9%82%84%E5%8E%9F%E6%AA%94%E6%A1%88%E8%87%B3%E5%B7%A5%E4%BD%9C%E7%9B%AE%E9%8C%84)
    - [還原工作目錄與索引](#%E9%82%84%E5%8E%9F%E5%B7%A5%E4%BD%9C%E7%9B%AE%E9%8C%84%E8%88%87%E7%B4%A2%E5%BC%95)
  - [本地及遠端儲存庫之間的操作](#%E6%9C%AC%E5%9C%B0%E5%8F%8A%E9%81%A0%E7%AB%AF%E5%84%B2%E5%AD%98%E5%BA%AB%E4%B9%8B%E9%96%93%E7%9A%84%E6%93%8D%E4%BD%9C)
    - [把本地儲存庫推到遠端儲存庫](#%E6%8A%8A%E6%9C%AC%E5%9C%B0%E5%84%B2%E5%AD%98%E5%BA%AB%E6%8E%A8%E5%88%B0%E9%81%A0%E7%AB%AF%E5%84%B2%E5%AD%98%E5%BA%AB)
    - [把遠端儲存庫複製到本地端儲存庫](#%E6%8A%8A%E9%81%A0%E7%AB%AF%E5%84%B2%E5%AD%98%E5%BA%AB%E8%A4%87%E8%A3%BD%E5%88%B0%E6%9C%AC%E5%9C%B0%E7%AB%AF%E5%84%B2%E5%AD%98%E5%BA%AB)
  - [分支分支分支](#%E5%88%86%E6%94%AF%E5%88%86%E6%94%AF%E5%88%86%E6%94%AF)
    - [查看目前有哪些分支](#%E6%9F%A5%E7%9C%8B%E7%9B%AE%E5%89%8D%E6%9C%89%E5%93%AA%E4%BA%9B%E5%88%86%E6%94%AF)
    - [回到特定的commit](#%E5%9B%9E%E5%88%B0%E7%89%B9%E5%AE%9A%E7%9A%84commit)
    - [創立分支](#%E5%89%B5%E7%AB%8B%E5%88%86%E6%94%AF)
  - [fast forward 合併分支](#fast-forward-%E5%90%88%E4%BD%B5%E5%88%86%E6%94%AF)
  - [當兩個分支產生衝突](#%E7%95%B6%E5%85%A9%E5%80%8B%E5%88%86%E6%94%AF%E7%94%A2%E7%94%9F%E8%A1%9D%E7%AA%81)
  - [標籤](#%E6%A8%99%E7%B1%A4)
  - [暫存](#%E6%9A%AB%E5%AD%98)
  - [github團隊協作](#github%E5%9C%98%E9%9A%8A%E5%8D%94%E4%BD%9C)
  - [拉回遠端儲存庫的變更 (git pull)](#%E6%8B%89%E5%9B%9E%E9%81%A0%E7%AB%AF%E5%84%B2%E5%AD%98%E5%BA%AB%E7%9A%84%E8%AE%8A%E6%9B%B4-git-pull)
  - [推變更到遠端數據庫發生失敗時(先PULL變更)](#%E6%8E%A8%E8%AE%8A%E6%9B%B4%E5%88%B0%E9%81%A0%E7%AB%AF%E6%95%B8%E6%93%9A%E5%BA%AB%E7%99%BC%E7%94%9F%E5%A4%B1%E6%95%97%E6%99%82%E5%85%88pull%E8%AE%8A%E6%9B%B4)
  - [Github pages](#github-pages)
  - [小型團隊分支協作篇](#%E5%B0%8F%E5%9E%8B%E5%9C%98%E9%9A%8A%E5%88%86%E6%94%AF%E5%8D%94%E4%BD%9C%E7%AF%87)
  - [git pull = git fetch + git merge](#git-pull--git-fetch--git-merge)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## 整體流程圖
![git](git.png)


## 上傳流程


### 產生本地端數據庫 (Local Repository)
`git init` 產生本地端數據庫(local repository)


### 在工作目錄內新增檔案
```bash
# 產生index.html, index2.html檔案
touch index.html
touch index2.html
mkdir css
touch css/all.css
touch index.html
```
### 從工作目錄傳到索引
- add changes from working directory to staging area

```bash
#把所有的檔案加到索引裡面，代表的是開始要追蹤這些檔案的變更 
git add .

#看看有沒有加到索引裡面
git status

#變更index2.html
touch index2.html

#因為index2.html變更了，所以要告訴git要追蹤這個變更
git add index2.html
```

### 把變更加入到本地儲存庫
- commit changes from staging area to local repository
- 確認這些變更

```bash
#commit之後會放到本地儲存庫
git commit -m 'update1'

#檢視過去git的紀錄
git log 
```

## 不想上傳變更了
### 忽略追蹤 
- `.gitignore`
- 不想追蹤的檔案，就放到.gitignore裡面
- 可以參考 [github的.gitignore](https://github.com/github/gitignore)

```bash
vim .gitignore
#在.gitignore可以加入下列幾行，表示不追蹤那些檔案
index.html
*.html
css/
```

### 不追蹤原本在索引中的檔案
reset head in staging area

`git reset HEAD` ：不追蹤目前在staging area的檔案
`git reset HEAD 檔案名稱` ：不追蹤staging area特定的檔案名稱
 
### 從本地儲存庫還原檔案至工作目錄
check out files from local repository
效果就是還原檔案

`git checkout 檔案名稱`

### 還原工作目錄與索引
檔案內容會跟最後一次commit保持一樣
`git reset --hard`


## 本地及遠端儲存庫之間的操作
### 把本地儲存庫推到遠端儲存庫
`git push`


### 把遠端儲存庫複製到本地端儲存庫
`git clone https://github.com/username/repo_name.git`

## 分支分支分支

### 查看目前有哪些分支
`git branch` 瀏覽目前有哪些分支

**HEAD：目前所在位置的指標**

### 回到特定的commit
```bash
git log  # 看看要回到哪個commit
git checkout git_log_的前四碼 # 可以回到特定的 commit
git checkout master # 復原
```

### 創立分支
`git branch branch_name`

```bash
# 創立分支
git branch branch_name
# 編輯程式
touch b.txt
git add b.txt
git commit -m 'create b.txt'

# 切換到 master
git checkout master

# 切換到 branch_name
git checkout branch_name
```

## fast forward 合併分支

```bash
cd desktop
mkdir project
cd project
git init
touch index.html
git branch feature1
echo "<head>this is title</head>" >> index.html

git add index.html
git commit -m 'add index.html'

git checkout master
git merge feature1
# now the content of index.html is the same between branch feature1 and master.
```


## 當兩個分支產生衝突
master, feature1 同時更新，卻發生衝突
```bash
cd desktop
mkdir project
touch index.html
vim index.html
<head>this is headline</head>

<body>
</body>
<h1> this is a test</h1>

git branch feature1
git checkout feature1
vim index.html
<head>this is headline</head>

<body>
        <link rel='sytlesheet' href='css/all.css'>
</body>
<h1> this is a test</h1>
git add index.html
git commit index.html -m 'add css to index.html in branch feature1'

git chechout master
cat index.html 
<head>this is headline</head>

<body>
</body>
<h1> this is a test</h1>
vim index.html
<head>this is headline</head>

<body>
        <h1> add a line</h1>
</body>
<h1> this is a test</h1>

git add index.html
git commit index.html -m 'add h1 to body in branch master'

# 開始合併
# 在branch master中合併branch feature1
# 因為兩個分支改了同一行，所以會有衝突
git merge feature1

# 發現衝突的檔名
vim index.html
<head>this is headline</head>

<body>
<<<<<<< HEAD
        <h1> add a line</h1>
=======
        <link rel='sytlesheet' href='css/all.css'>
>>>>>>> feature1
</body>
<h1> this is a test</h1>

# 修改成
<head>this is headline</head>

<body>
        <h1> add a line</h1>
        <link rel='sytlesheet' href='css/all.css'>
</body>
<h1> this is a test</h1>

git add index.html
git commit -m 'mearge index.html in branch master and feature1'

# master 有最後合併的結果
# feature1 則是原本feature1，不會因為merge而改變
```

## 標籤 
對commit做標籤，未來可以快速回到那個標籤。

`git tag` 查詢標籤 
`git tag -n`查詢詳細標籤 
`git tag -d tag_name`刪除標籤 
`git tag tag_name`新增標籤 
`git tag -am '備註內容' tag_name`新增有備註的標籤 

**`git checkout tag_name`** 切換到標籤的commit 

## 暫存

例如在工作目錄奮鬥了一陣子，卻臨時有需要先處理別的commit時，就先把目前的工作目錄的資料暫存起來。有需要將當前工作目錄中的修改帶到其他的分支時，也可以把當前工作目錄中的資料暫存起來，切換到其他的分支，然後還原暫存，如果檔案有衝突就處理衝突。
`git stash`   暫時儲存當前目錄
`git stash list`  瀏覽 git stash 列表
`git stash pop`  還原暫存
`git stash drop`  清除最新暫存
`git stash clear`  清除全部暫存

## github團隊協作
`git remote`  看看有那些遠端數據庫
`git remote rename 原名稱 修改名稱` 修改遠端數據庫的名稱
`git push origin master`  把commit送到遠端主機的某個分支。origin是預設的遠端主機名稱 master是分支名稱

```bash
# 先在github上建立一個repository
git clone https://github.com/usernamte/repository_name.git
cd repository_name
touch index.html
git add index.html
git commit -m 'add index.html'
git push

git branch issue1
touch a.html
git add a.html
git commit -m 'add a.html'
git remote
git remote origin github
git push github issue1
# 把issue1分支也送到github上了
```

## 拉回遠端儲存庫的變更 (git pull)

- 同事A開始要做專案時，先用`git clone`把專案抓下來到本地數據庫接下來`git push origin`把開發的index.html上傳到github。
- 同事B上班後先用`git pull`把github上面的資料抓下來，然後開始撰寫b.html，最後`git push`送上去github。
- 同事A上班後，使用`git pull`把異動抓下來，檢視目前專案最新的狀態。
- `git log`可以協助檢視`git pull`抓回哪些commit。

## 推變更到遠端數據庫發生失敗時(先PULL變更)

- 有些情況無法push，這時候需要pull再重新push

```bash
# 在github上新建立一個遠端儲存庫

# 同事A下載遠端數據庫
git clone https://github.com/username/reponame.git

touch index.html
git add index.html
git push origin master

# 同事B下載遠端數據庫
git clone https://github.com/username/reponame.git

# 同事A 晚上勤奮開發
touch index2.html
git add index2.html
git commin -m 'add index2.html'

# 同事B 隔天早上直接新增檔案
touch a.html
git add a.html
git push origin master

# 同事B 在push時出錯了，git告訴你應該要先做git pull
git pull
# 同事B 發現結果git pull時，git自動做了合併，之後同事B就可以push了
git push origin master

# 這個時候github上跟同事B的狀態就變成相同的

# 同事A 這個時候可以拉下來github上的遠端儲存庫
git pull origin master

# 這個時候同事A的狀態，就跟github上以及同事B相同了
```

## Github pages

首先在github上面開個demosite網址是https://github.com/username/demosite.git

```bash
git clone https://github.com/usernmae/demosite.git
touch index.html
git add index.html
git commit -m 'add index.html'
git push origin master
```
上去github，在settings裡面設定github pages

## 小型團隊分支協作篇
- 團隊開發branch的注意事項
- [參考網址](http://nvie.com/posts/a-successful-git-branching-model/)

## git pull = git fetch + git merge
`git fetch origin branch_name` 此時會多一個 FETCH_HEAD 的分支，這個就是遠端數據庫的分支，可以等到審視完沒問題後，再合併 FETCH_HEAD。

