---
title: gulp学习笔记总结
date: 2018-07-09 07:35:30 
categories: 构建工具
tags: gulp
---

今天学习了一下gulp的基本用法，特将学习的相关知识点梳理总结如下，便于以后温故复习。

昨天学习了一下gulp基本用法，今天一早醒来就想着应该总结一下，便于以后复习查看，现总结成如下gulp学习笔记。

## 初识gulp

### 什么是gulp

- Gulp是一个Node模块    
- Gulp是个任务管理工具    
- Gulp主要用在构建流程中    

### 主要作用    

- 压缩css，js，图片等静态资源    
- 打包合并必要的资源文件    
- 处理浏览器长缓存    

### 插件机制    

- Gulp的实际功能基本都是靠插件完成    
- 可以非常方便得实现自己的插件    

### 相对Grunt的优势    

- 速度快    
- 配置简单    
- 基于流进行构建    

## 安装和介绍Gulp    

使用gulp的主要步骤：首先是安装 nodejs，通过 nodejs 的 npm 全局安装和项目安装 gulp，其次在项目里安装 所需要的 gulp 插件，然后新建 gulp 的配置文件 gulpfile.js 并写好配置信息（定义 gulp 任务），最后通过命令提示符运行 gulp 任务即可。即：    

安装 nodejs -> 全局安装 gulp -> 项目安装 gulp 以及 gulp 插件 -> 配置 gulpfile.js -> 运行任务    

### 安装Gulp    

1. 安装Node环境；    
2. npm install gulp-cli -g     全局安装gulp-cli，全局安装是为了通过它执行gulp任务；
3. npm install gulp -D     本地项目安装gulp，为了调用gulp插件功能。

### 创建gulp任务  

- 创建gulpfile.js文件   说明：gulpfile.js 是 gulp 项目的配置文件，是位于项目根目录的普通 js 文件（其实 将 gulpfile.js 放入其他文件夹下亦可）。       
- 使用gulp.task来创建任务    

## Gulp常用指令和任务嵌套 

### 常用指令    

常用指令包括：gulp.task，gulp.src，gulp.dest ，gulp.watch，具体介绍如下：

####  `gulp.task(name[, deps][, fn])    `

gulp.task用来创建一个任务，接收两个参数，name代表任务的名 称，deps是该任务依赖于别的任务，fn是该任务需要执行的内容 。运行任务是在命令行输入gulp+任务名后回车即可。

使用例子：

~~~
gulp.task('test1', ['test'], function() {
	console.log('index.js has been copied')
})
~~~

#### gulp.src(globs[, options])    

gulp.src用来指定任务执行的对象，即目标文件。该指令接收两个参 数，globs是文件路径，options是一些配置参数 。

使用例子：

~~~
gulp.task('test', function() {
	gulp.src('index.js') //gulp.src查找文件的时候是通过gulpfile作为相对路径来查找的
	    .pipe(gulp.dest('dist')) //gulp是基于文件流的形式去操作文件的，文件流就是在管道中可以流动的，读入文件之后将文件内容通过管道传递到下一个任务中
	//src是输入文件，dest是输出文件
})
~~~

#### gulp.dest(path[, options])    

gulp.dest用来指定任务执行完成后的输出位置。该指令接收两个参数，path是存放路径，options是一项配置参数。

#### gulp.watch(glob [, opts], tasks | cb)    

gulp.watch用来监视对象的变化，一旦有变化会执行相应的任务。 该指令接收三个参数，glob是需要监视的目标文件地址；opts是一 个对象，包含一些配置参数；最后一个参数可以是之前指定好的任 务，也可以是一个方法。

使用例子：

一种写法

~~~
gulp.task('watch', function() {
	gulp.watch('index.js', ['test1'])
})
~~~

另一种写法

~~~
gulp.task('watch', function() {
	gulp.watch('index.js', function() {
		console.log('index.js has changed')
	})
})
~~~

#### 其他配置项

![2gulp配置项](初识gulp/2gulp配置项.png)

![3 gulp配置项](初识gulp/3 gulp配置项.png)

![4](初识gulp/4.png)

![5](初识gulp/5.png)

## 压缩和打包js    

### Uglify js    

使用gulp-uglify来在gulp中进行js代码压缩，其Github地址为：https://github.com/mishoo/UglifyJS    

使用cnpm install gulp-uglify安装ugligyjs，安装后在gulpfile.js中通过`var uglify = require('gulp-uglify')`形式引入，然后创建task任务后运行任务即可，task任务创建类似于以下例子：

~~~
gulp.task('compress', function() {
	gulp.src('index.js')
	    .pipe(uglify()) //将index.js中的文件压缩
	    .pipe(gulp.dest('dist')) //将压缩后的文件输出如dist文件中
})
~~~

### gulp-concat    

使用gulp-concat来执行js合并操作，其Github地址为：Github：https://github.com/contra/gulp-concat  ，需要注意js合并的先后顺序，一般是合并完后进行压缩。

同样使用cnpm install gulp-concat安装包，安装后在gulpfile.js中通过`var concat = require('gulp-concat')`形式引入，然后创建task任务后运行任务即可，task任务创建类似于以下例子：

~~~
gulp.task('concat', function() {
	gulp.src(['a.js', 'b.js'])  //读入a，b两个文件
	    .pipe(concat('dist/all.js')) //合并后命名为dist中的all.js
	    .pipe(gulp.dest('dist')) //将合并后的文件放入dist文件夹中，发现合并的文件在dist中的dist文件夹中，说明上面路径应该只写all.js，即只写合并后的文件名，输出的文件夹路径可以在dest中写
})
~~~

## css打包压缩、预处理方案及postcss    

### 打包压缩    

- 使用gulp-csso进行css压缩    

- 使用gulp-concat-css来进行css合并    

  使用例子：

  ~~~
  var csso = require('gulp-csso')
  var cssConcat = require('gulp-concat-css')
  gulp.task('csso', function() {
  	gulp.src('index.css')
  	    .pipe(csso())
  	    .pipe(gulp.dest('dist'))
  })
  
  gulp.task('css-concat', function() {
  	gulp.src(['index.css', 'index2.css'])
  	    .pipe(cssConcat('all.css')) //合并css文件
  	    .pipe(csso())  //合并完进行压缩
  	    .pipe(gulp.dest('dist'))
  })
  ~~~

### css预处理器    

- 预处理器是可编译成css的一种高级封装    
- 比较流行的预处理器有sass，less，cssnext等    
- css预处理器可以解决css缺少编程能力的缺点    

### 使用gulp执行less预处理任务   

-  gulp-less可以在gulp中执行less预处理的插件    
- 一般在开发中会监视less文件变化    

使用例子：

~~~
var less = require('gulp-less')

gulp.task('less', function() {
	gulp.src('less/index.less')
	    .pipe(less())
	    .pipe(csso())
	    .pipe(gulp.dest('dist/less'))
})
~~~

### Postcss    

- postcss提供了非常多优化css的工具 （在我们有css文件之后去处理css文件，对其进行优化）
- 使用postcss-autoprefixer自动加浏览器前缀    

先cnpm install gulp-postcss，后cnpm install autoprefixer，安装两个包。其中，gulp-postcss提供我们在gulp中执行postcss的能力。    

使用例子：

~~~
var postcss = require('gulp-postcss')
var autoprefixer = require('autoprefixer')

gulp.task('postcss', function() {
	gulp.src('index.css')
	    .pipe(postcss([autoprefixer()])) //因为postcss是使用插件去优化处理css的，用到哪些插件只需要在postcss的数组中传入就行
	    .pipe(gulp.dest('dist'))
})
~~~

### 为什么要做图片压缩    

图片的空间一般较大，很多图片可以在保证展示质量的情况下进行 一些压缩，这样可以降低访问流量同时加快访问速度。

### gulp-imagemin    

图片压缩采用gulp-imagemin，其github地址：https://github.com/sindresorhus/gulp-imagemin    

使用例子：

~~~
var imageMin = require('gulp-imagemin')

gulp.task('image-min', function() {
	gulp.src('images/*')
	    .pipe(imageMin()) //里面可以传入数组来定义某种特定格式图片的特殊压缩方式,如[imageMin.gifsicle({interlaced: true}), imageMinjpentran({})]具体细节可查阅这个包的官方文档
	    .pipe(gulp.dest('dist/images'))
})
~~~

## gulp处理文件永久缓存    

### 什么是永久缓存    

浏览器根据HTTP头Cache-Control来判断是否要缓存文件，通过这 个机制，我们可以让浏览器缓存css，js，图片等耗流量的静态文件    

如果改变了文件，就给文件名上加一段哈希内容，就是如果你的文件改变了，hash内容就会改变，这样从浏览器上发出的资源请求链接就会改变，因此来达到浏览器更新缓存的功能，这就要求我们每次发布整个网站时，都要对静态文件进行一次文件名的处理，这就非常适合在gulp中去使用这个流程，我们只需要给gulp创建一个任务，然后在每次发布的时候去执行这个任务，就可以把这不步做好

### 如何刷新缓存    

- 更新文件名以强制浏览器加载新文件    
- 在静态资源url后面加上版本号    

使用cnpm install gulp-rev安装包，用这个包来进行文件名的修改，他可以读取文件的内容，然后对文件内容进行计算，然后得到一个哈希值，把他加到文件名上去。安装完这个插件后，首先在gulpfile文件引入，然后创建task任务使用。

使用例子：

~~~
var rev = require('gulp-rev')
gulp.task('rev', function() {
	gulp.src('dist/**/*') //这里两个*代表dist目录下的所有层级，一个星号*代表目录下的任何文件都要读取
	    .pipe(rev())
	    .pipe(gulp.dest('build'))
	    .pipe(rev.manifest()) //输出一个manifest.json文件，里面包含整个文件的对应关系（也就是build文件夹中和源文件夹中文件的对应关系）  这样我们就可以有很多种方式去告诉我们项目中要使用已经进行hash编码方式的文件来作为我们的资源，一arva种是使用静态替换的方法，动态分析html文件，使用路径对应关系来把新的文件名给替换进去，另外一种是比如在前端开发过程中，页面使用模板生成的，这个模板都有一定的编程能力，可以使用模板去读取json文件，动态的去生成文件对应关系
	    .pipe(gulp.dest('build'))
})
~~~

可以看到处理后的文件名上都带有一段哈希值，如下：

![hash](初识gulp/hash.png)

## gulp-babel    

### babel    

babel的功能是将最新的js语法转义成主流浏览器普遍支持的es5语 法，以此来提供项目在开发阶段可以使用最新js语法的功能。

先使用cnpm install gulp-babel，安装gulp-babe包，再cnpm install babel-preset-es2015安装另一个babel-preset-es2015包。

### gulp-babel    

- gulp-babel提供gulp工作流操作babel的功能    
- 使用babelrc文件来配置配置babel    

使用例子：

~~~
var babel = require('gulp-babel')

gulp.task('babel', function() {
	gulp.src('es6.js')
	    .pipe(babel({
	    	presets: ['es2015'] //告诉他要编译到浏览器支持的es2015js格式
	    }))
	    .pipe(gulp.dest('dist'))
})
~~~

## livereload    

### 作用    

在开发阶段我们需要经常修改静态资源文件，每次更新文件要查看 效果时都需要刷新浏览器。lievereload让我们在更新资源文件之后 浏览器自动执行刷新    

### 需要工具    

- gulp-connect
- LiveReload chrome插件    

gulp-connect可以在项目里面启动一个http server，同时让浏览器里面的插件去监听启动的server，以此来通知浏览器什么时候刷新。

使用步骤：

第一步：cnpm install gulp-connect；

第二步：打开chrome网上应用店，搜索livereload，添加到扩展程序；

第三步：在gulpfile中引入这个包后创建任务即可。

chrome 插件网址：https://www.npmjs.com/package/gulp-livereload    

使用例子：

~~~
var connect = require('gulp-connect')
gulp.task('livereload', function() {
	connect.server({
		livereload: true,
		port: '1222'  //默认监听的8080端口，可通过port更改端口号
	})
	gulp.watch(['index.html', 'index.js'], function() {
		gulp.src('index.html')
		    .pipe(connect.reload())
	})
})
~~~

## gulp workflow

**知识点：**

- 目录结构
- 代码处理，http优化
- 错误排查

### 目录结构

通过一个http的url去映射到项目资源的存放位置，以此来加载css、js以及其他一些静态资源的内容。

项目部署到线上需要考虑很多内容，比如http加载优化、文件的合并打包，这些内容在开发时如何处理，以及在开发时的目录结构和上线时目录结构的对应关系，这些如何通过gulp去处理。

- 开发时资源存放位置
- html中引用资源时的相对位置
- 编译文件的路径（编译文件路径的存放和引用）

### 代码处理，http优化

- 预处理，合并，压缩
- http优化

### 错误排查

gulp基于流的方式导致中途出错难以定位

使用pump打印正确的报错信息

可以使用pump这个插件定位错误，开发的时候在命令行输入gulp watch启动watch任务，然后启动web server，让网站可访问。此处模版引擎采用的hbs。

给一个开发完整的gulpflejs文件代码例子：

~~~
var gulp = require('gulp')
var babel = require('gulp-babel')
var less = require('gulp-less')
var postcss = require('gulp-postcss')
var autoprefixer = require('autoprefixer')
var imagemin = require('gulp-imagemin')
var uglify = require('gulp-uglify')
var csso = require('gulp-csso')
var rev = require('gulp-rev')
var pump = require('pump')

// develop tasks
gulp.task('babel', function () {
  // turn babel to es2015
  pump([
      gulp.src('src/js/**/*.js'),
      babel({
        presets: ['es2015']
      }),
      gulp.dest('public/javascripts')
    ])
})

gulp.task('css', function () {
  // turn less to css, and use postcss
  pump([
      gulp.src('src/less/**/*.less'),
      less(),
      postcss([autoprefixer()]),
      gulp.dest('public/stylesheets')
    ])
})

gulp.task('watch', ['babel', 'css'], function() {  //开发的时候监听
  // watch es6 and less files, invoke babel and css task
  gulp.watch('src/js/**/*.js', ['babel'])
  gulp.watch('src/less/**/*.less', ['less'])
})

// end develop tasks

gulp.task('imagemin', function () { //项目上线的时候压缩图片，css，js
  // minify the images
  pump([
      gulp.src('public/images/**/*'),
      imagemin(),
      gulp.dest('dist/images')
    ])
})

gulp.task('minify-js', ['babel'], function() {
  // minify the js
  pump([
      gulp.src('public/javascripts/**/*.js'),
      uglify(),
      gulp.dest('dist/javascripts')
    ])
})

gulp.task('minify-css', ['css'], function() {
  // minify the css
  pump([
      gulp.src('public/stylesheets/**/*.css'),
      csso(),
      gulp.dest('dist/stylesheets')
    ])
})

gulp.task('rev', ['imagemin', 'minify-js', 'minify-css'], function () {
  // add hash to all static files and generate manifest file 将静态文件加上hash信息，以让浏览器可以去缓存我们的文件
  pump([
      gulp.src('dist/**/*'),
      rev(),
      gulp.dest('build'),
      rev.manifest(),
      gulp.dest('build')
    ])
})

gulp.task('default', ['rev']) //最后给gulp设置default任务，这样在命令行中输入gulp就可以直接执行这个任务，这样调用更方便
~~~

## 使用webstorm运行gulp任务

我们可以使用 webstorm来 可视化运行 gulp 任务；    

具体使用方法：

将项目导入 webstorm，右键 gulpfile.js 选择”Show Gulp Tasks”打 开 Gulp 窗口，若出现”No task found”，选择右键”Reload tasks”，双击运行即可。    

![webstorm gulp](初识gulp/webstorm gulp.png)

## 相关学习文档链接

为了方便以后资源链接汇总，最后附上相关文档链接：

gulp官网：

[gulp中文网](https://www.gulpjs.com.cn/)

[gulp英文官网](https://gulpjs.com/)

相关包的github网址：

[Uglify js  github](https://github.com/mishoo/UglifyJS    )

[gulp-concat github](https://github.com/contra/gulp-concat )

[gulp-imagemin github](https://github.com/sindresorhus/gulp-imagemin )

因为用的包都是npm上的，都可以在npm包搜索处搜索后查看官方文档，npm包搜索网址：

[npm packages](https://www.npmjs.com/package/package)



**注：**以上内容主要是自己的整理慕课网课程学习笔记，方便以后复习查看使用。



 