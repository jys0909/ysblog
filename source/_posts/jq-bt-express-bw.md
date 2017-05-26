---
title: JQuery + Gulp + Bootstrap + browserify + Express 搭建前端框架
date: 2017-05-26 15:10:39
tags: 个人整理
categories: 个人整理
---

# 初始化项目
新建项目文件夹 web
```
> cd web 
> express 
```
修改app.js
```
app.set('views', path.join(__dirname, 'views'));
// setting view engine
app.set('view engine', 'html');
app.engine('html', require('ejs-mate'));
```
这样我们就可以使用`.html`文件来编写代码了, 将view下面自动生成的`.ejs`改成`.html`

# 安装 browserify, JQuery, Bootstrap ,browserify-shim
```
npm install browserify --save 
npm install browserify-shim --save // 可以使用似如jquery的插件变
npm install jquery --save
npm install bootstrap --save

```

browserify-shim 如何使用？
在package.json下面添加如下内容
```
"browserify": {
    "transform": [
      "browserify-shim"
    ]
  },
  "browser": {
    // 添加jquery.pep 插件
    "jquery.pep": "./node_modules/jquery.pep.js/src/jquery.pep.js",
    // 添加bootstrap插件
    "bootstrap": "./node_modules/bootstrap/dist/js/bootstrap.min.js"
  },
  "browserify-shim": {
  // 添加jquery.pep 插件
    "jquery.pep": {
      "depends": [
        "jquery:jQuery"
      ]
    },
    // 添加bootstrap插件
    "bootstrap": {
      "depends": [
        "jquery:jQuery"
      ]
    }
  }
  ```
  # 安装 Gulp 
  ```
  npm install gulp -g
  ```
  根目录添加`gulpfile.js`内容如下
  ```
  var gulp = require("gulp");
var browserify = require("browserify");
var sourcemaps = require("gulp-sourcemaps");
var source = require('vinyl-source-stream');
var buffer = require('vinyl-buffer');
 
gulp.task("browserify", function () {
 var b = browserify({
  entries: "./static/src/main.js",
  debug: false
 });
 
 return b.bundle()
  .pipe(source("bundle.js"))
  .pipe(buffer())
  .pipe(sourcemaps.init({loadMaps: true}))
  .pipe(sourcemaps.write("."))
  .pipe(gulp.dest("./static/dist"));
});
```
意思是创建`browserify`任务， 执行`browserify`编译
```
> gulp browserify
```
会将`./static/src/main.js`文件及所有依赖打包成`.static/dist/bundle.js`

main.js
```
var $ = require("jquery");
require("bootstrap"); 
require("jquery.pep");
$(".move-box").pep({
  useCSSTranslation: false,
  constrainTo: 'parent'
});

```

然后index.html 中引用`.static/dist/bundle.js`就可以了
  