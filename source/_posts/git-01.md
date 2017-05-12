---
title: git_01
date: 2017-05-12 10:20:39
tags: 个人整理
categories： 个人整理
---
### Clone
从远程仓库下载代码到本地
```
git clone https://github.com/jys0909/ysblog.git

```
### 更新代码
从远程仓库更新代码
```
git pull

```


### 分支
```
git branch // 分支列表
git branch devlopment  // 添加分支

```

### 切换分支
```
git checkout devlopment

```

### 提交代码
```
git add .
git commit -m 'decription'
git push
```

### 提交本地分支到远程仓库
```
git push origin devlopment:devlopment
```
这个操作如果远程仓库已存在devlopment分支， 则为更新
### 全局设置
```
git config --global user.name "xxx"
git config --global user.email "xxx"

```


****



### git pull失败,提示：==fatal: refusing to merge unrelated histories==

git pull 时添加参数
```
git pull origin master --allow-unrelated-histories
```

> 可以参考[网址](http://stackoverflow.com/questions/37937984/git-refusing-to-merge-unrelated-histories)


### git clone 时提示 Could not read from remote repository

有可能是使用的是 ssh 方式，在提示yes的时候没写正确, 使用https
