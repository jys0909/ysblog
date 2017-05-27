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


> 注： browserify 也可以打包并可以调试
```
browserify main.js --debug | exorcist bundle.js.map > bundle.js
```

也可以给gulp添加一个watch
```
gulp.task("watch",function(){
  gulp.watch("./static/src/main.js",["browserify"]);
});
```
这时候修改`main.js`可以看出效果了

gulp绑定多个文件

如果构建的js很多，添加如下插件
```
cnpm install gulp-rename --save
cnpm install event-stream --save
```
修改`gulpfile.js`文件
```
var rename = require('gulp-rename');
var browserify = require('browserify');
var es = require('event-stream');

gulp.task('browserify', function(){
    //定义多个入口文件
    var entityFiles = [
        './static/src/main.js',
        './static/src/log.js',
    ];

    //遍历映射这些入口文件
    var tasks = entityFiles.map(function(entity){
        return browserify({entries: [entity]})
            .bundle()
            .pipe(source(entity))
            .pipe(rename({
                extname: '.bundle.js',
                dirname: ''
            }))
            .pipe(gulp.dest('./static/dist'));
    });

    //创建一个合并流
    return es.merge.apply(null, tasks);
});
```
下面看另一种`glob `插件方法
```
cnpm install glob --save;
```
```
var gulp = require('gulp');
var source = require('vinyl-source-stream');
var rename = require('gulp-rename');
var browserify = require('browserify');
var es = require('event-stream');
var glob = require('glob');

gulp.task('browserify', function(done){
    glob('./static/src/*.js', function(err, files) {
        if(err) done(err);

        var tasks = files.map(function(entry) {
            return browserify({ entries: [entry] })
                .bundle()
                .pipe(source(entry))
                .pipe(rename({
                    extname: '.bundle.js',
                    dirname: ''
                }))
                .pipe(gulp.dest('./static/dist'));
            });
        es.merge(tasks).on('end', done);
    })
});

gulp.task("watch",function(){
  gulp.watch("./static/src/*.js",["browserify"]);
});
```

### 随便介绍下 合并压缩Css
用到的包
```
ar concat = require('gulp-concat'); //- 多个文件合并为一个；
var cssmin = require('gulp-minify-css'); //- 压缩CSS为一行；
var rev = require('gulp-rev');  //- 对文件名加MD5后缀
var revCollector = require('gulp-rev-collector'); //- 路径替换
var cssver = require('gulp-make-css-url-version'); 
```
在`gulpfile.js`中添加如下代码
```
gulp.task('Cssmin', function() {                              //- 创建一个名为 concat 的 task
    gulp.src('./static/css/*.css')    //- 需要处理的css文件
        .pipe(cssver()) 
		.pipe(concat('default.min.css')) // 合并成一个文件名
		.pipe(rev()) // 文件名加MD5后缀
		.pipe(cssmin()) // 压缩成一行
        .pipe(gulp.dest('./static/dist'))  //  输出文件本地
		.pipe(rev.manifest()) //- 生成一个rev-manifest.json
		.pipe(gulp.dest('./static/rev'));  //- 将 rev-manifest.json 保存到 rev 目录内
		
});

gulp.task('rev', function() {
    gulp.src(['./static/rev/*.json', './view/*.html'])   		//- 读取 rev-manifest.json 文件以及需要进行css名替换的文件
        .pipe(revCollector())                                   //- 执行文件内css名的替换
        .pipe(gulp.dest('./view/'));                     		//- 替换后的文件输出的目录
});

gulp.task('default', ['Cssmin', 'rev']);
```
  