---
title: sublime中使less后缀文件产生高亮效果的方法
date: 2018-07-08 20:54:38
categories: sublime
tags: [less,语法高亮]
---

今天在学习gulp执行less预处理任务的时候发现我的sublime里面less文件的显示没有语法高亮，在网上查了一下sublime中使less文件显示语法高亮的方法，在此记录一下。

主要是借用用github上的开源项目，对于windows用户，安装了git后，操作步骤如下：

1. 打开sublime，点击菜单栏preferences选项中的Browse Packages，打开Packages文件夹；

2. 右键选择Git Bash Here，然后在命令行输入以下内容：

   ~~~
   git clone git://github.com/danro/LESS-sublime.git LESS
   ~~~

3. 再用sublime打开less文件就有语法高亮啦~~~



**参考文章：**

[sublime使less产生高亮效果](https://blog.csdn.net/zp1996323/article/details/49403517)