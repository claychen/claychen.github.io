---
title: hexo 設定
date: 2017-01-23 17:45:16
tags:
---

# 環境設置
### 安裝git
http://git-scm.com/download/

### 設定github
連上username.github.com，新建repository，名稱為username.github.io即產生後續放blog的地方。

### 安裝node.js
https://nodejs.org/en/

### 安裝hexo
```
cd hexo
npm install hexo-cli -g
hexo init blog
cd blog
npm install
```
### hexo 指令

```
hexo g # 由source檔，即markdown檔案，產生html檔案
hexo s # 開啟hexo server http://localhost:4000/
hexo d # hexo把blog佈署到指定的地方(本例中昰github)
hexo new 'new-article' #產生新文章
hexo new page 'new-page-name' #產生新頁面
hexo -v # 看環境配置
```
### 套用版型
```
1. 在blog目錄執行
git clone https://github.com/klugjo/hexo-theme-clean-blog.git themes/clean-blog
2. 修改 _config.yml：
theme: clean-blog
3. hexo g
```

### 部署上GitHub
第一次部署要
1. 執行 npm install hexo-deployer-git --save
2. 修改_config.yml中的type、repo
```
deploy:
	type: git
    repo: https://github.com/username/username.github.io.git
    branch: master
    message:
```

### 參考資料
1. http://jiji262.github.io/2016/04/15/2016-04-15-hexo-github-pages-blog/