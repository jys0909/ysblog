---
title: 利用Travis CI 让你的github项目持续构建
date: 2017-05-10 15:43:25
tags: 个人整理
categories: 个人整理
---

   Travis CI 是目前新兴的开源持续集成构建项目，它与jenkins，GO的很明显的特别在于采用yaml格式，简洁清新独树一帜。目前大多数的github项目都已经移入到Travis CI的构建队列中，据说Travis CI每天运行超过4000次完整构建
   
 ### 搭建 `Travis CI build`，需要 `github`账号和`github`项目
  
  1. 用github账号登陆[Travis CI](https://travis-ci.org/). 
  2. 在右上角你的账户名点击进入 ==account==，在==Repositories tab==页点击==Sync now==同步你的github项目
  3. 选中项目将默认的off改变为on开启项目的持续集成
  4. 在你项目的根目录建立一个.travis.yml文件，内容为
  ```
  language: node_js
  node_js: stable

  # S: Build Lifecycle
  install:
    - npm install

  # before_script:
    # -
  script:
    - hexo g

  after_script:
    - cd ./public
    - git init
    - git config user.name "jys0909"
    - git config user.email "276977683@qq.com"
    - git add .
    - git commit -m "Update docs"
    - git push --force --quiet "https://${GH_TOKEN}@${GH_REF}" master:master
  # E: Build LifeCycle

  branches:
    only:
      - blog-source
  env:
   global:
   - GH_REF: github.com/jys0909/ysblog.git
  ```
  

> 来源： http://www.cnblogs.com/whitewolf/archive/2013/04/14/3019838.html