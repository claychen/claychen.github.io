---
title: git memo
date: 2017-01-26 15:58:26
tags:
---


```
# 設定 git commit 的作者訊息
$ git config --global user.name 'username'
$ git config --global user.email 'username@com'

# 看設定的內容
$ git config --list

# 看git的設定檔
$ cat ~/.gitconfig

# 設定別名
$ git config --global alias.st status
```

```
# 建立新的 Repository
$ git init 
Initialized empty Git repository in D:/work/git/git-test/.git/
```

```
# 複製遠端的 Repository
$ git clone https://github.com/claychen/python-misc.git
Cloning into 'python-misc'...
...

# 複製遠端的 Repository，而且改變資料夾的名稱
$ git clone https://github.com/claychen/python-misc.git python-test

```

# 加檔案進 Stage
`git status`中有untracked file，表示過去在這個git repository中從未有這個檔案，要用`git add`讓他加入追蹤。`git add`之後會變成是changes to be committed。
```
# 檢視分支的狀態
$ git status 

# 加入檔案
$ git add filename

# 把修改過或新增加的檔案一次加入Stage
$ git add .

# 但是 git add . 很有可能放入不該加入的檔案，所以改用互動模式加入
$ git add -i
```

# 提交Stage中的檔案
`commit`是Git中的節點，未來回朔時的參考。
```
$ git commit -m 'commit message'
```

# 記錄

```
# 檢視記錄
$ git log
# 看詳細的資訊
$ git log --stat
```

# Git Ignore
git忽略在`.gitignore`中的檔案。如過之前git已經有追蹤，那就要先將這個檔案刪除，再重新commit。

# Git Branch

# 參考資料
- [連猴子也能懂的Git入門指南](https://backlogtool.com/git-guide/tw/)
- 