---
title: 用hexo写博客如何在不同电脑上同步管理
date: 2018-12-23 16:41:12
categories:日常总结
tags:hexo
---

之前在宿舍电脑上用hexo搭建的博客，今天在办公室电脑上想写，直接git clone远程仓库后发现里面的文件都是静态文件，无法直接在此仓库上编写博客，然后查找资料寻找解决办法。现将解决步骤记录如下。

内容主要参考自[如何在不同电脑上同时写hexo博客？](http://chown-jane-y.coding.me/2017/03/15/%E5%A6%82%E4%BD%95%E5%9C%A8%E4%B8%8D%E5%90%8C%E7%94%B5%E8%84%91%E4%B8%8A%E5%90%8C%E6%97%B6%E5%86%99hexo%E5%8D%9A%E5%AE%A2%EF%BC%9F/)

主要原理是利用**利用github的不同分支来分别保存网站静态文件与hexo源码（md原始文件及主题等）**，实现在不同电脑上都可以自由写博客。

本文是建立在你已有hexo博客的情况下，教你如何把它迁移到另外一台电脑上，使两台电脑同时可以进行博客部署。下面就具体讲解一下如何实现吧！

*注：假设你有工作PC和个人PC，你原有的博客搭建在个人PC上，以下首先介绍个人PC上的操作步骤。*

## 个人PC

### 在github上新建远程仓库

将原来的page项目删除，新建一个和原来名字一样的空项目。不用初始README.md
此时只有一个空的master分支。

本地的目录不要动，到时候需要用你原来博客的配置和文章替换该项目中的文件夹。你可以把你本地的博客目录复制一份作为备份，以免误操作将原来的内容进行改动。

### 本地初始化一个Hexo项目

新建一个空目录，作为你的博客目录。进入该目录，右击`Git bash here`，初始化一个Hexo项目：

```
hexo init
npm install
npm install hexo-deployer-git --save
```

然后用自己原来博客里的文件替换掉这里的`source\`, `scaffolds\`, `themes\`, `_config.yml`替换成自己原来博客里的。注意，这里把`themes/jacman`中的`.git/`目录删除，否则下面推送到hexo分支后jacman为空。这也是众多坑中的一个，由于水平有限具体原因不是很清楚，只能瞎猫碰上死耗子啦。

### 将整个目录推送到master

要推送到master分支，首先要将该目录初始化为本地Git仓库：

```
git init
 
//把博客目录下所有文件推送到master分支
git remote add origin [your repository url]
git add .
git commit -m "first add hexo source code"
git push origin master
```

### 在github上新建一个分支

新建一个分支hexo(名字可以自定义)，这时候hexo分支和master分支的内容一样，都是hexo的源文件。

并把hexo设为默认分支，这样的话在另外一台机器上克隆下来就直接进入hexo分支，并且以后所有操作都是在hexo分支下完成。

为什么需要这个额外的分支呢？

因为`hexo d`只把静态网页文件部署到master分支上，所以你换了另外一台电脑，就无法pull下来继续写博客了。有了hexo分支的话，就可以把hexo分支中的源文件(配置文件、主题样式等)pull下来，再`hexo g`的话就可以生成一模一样的静态文件了。

记得将hexo分支设置为默认分支，在仓库`->Settings->Branches->Default branch`中将默认分支设为`hexo`，`save`保存。

此步骤参考[Hexo博客多台电脑设备同步管理](https://juejin.im/post/5acf22e6f265da23994eeac9)

### 部署博客

还是和以前一样：

```
hexo g -d
```

博客已经成功部署到master分支，这时候到github查看两个分支的内容，hexo分支里是源文件，master里是静态文件。

**注意：根目录下的_config.yml配置文件中branch一定要填master，否则hexo d就会部署到hexo分支下。**

### 关联到远程hexo分支

在本地新建一个hexo分支并与远程hexo分支关联：

```
git checkout -b hexo
git pull origin hexo
```

另外别忘了，如果有修改的话，要推送到hexo分支上去：

```
git add .
git commit -m  ""
git push origin hexo
```

这样才能在另外的机器上pull下来，保持同步。



## 工作PC

个人PC上的工作已经完成了，下面讲一下如果你换到了另外一台电脑上，应该如何操作。

### 将博客项目克隆下来

```
git clone [your repository url]
```

克隆下来的仓库和你在个人PC中的目录是一模一样的，所以可以在这基础上继续写博客了。但是由于`.gitignore`文件中过滤了`node_modules\`，所以克隆下来的目录里没有`node_modules\`，这是hexo所需要的组件，所以要在该目录中重新安装hexo，**但不需要hexo init**。

```
npm install hexo
npm install
npm install hexo-deployer-git --save
```

### 新建一篇文章测试

```
hexo new "work PC test"
```

推送到hexo分支

```
git add .
git commit -m "add work PC test"
git push origin hexo
```



部署到master分支

```
hexo g -d
```



## 日常操作

如果上面的过程都操作无误的话，你就可以在任何能联网的电脑上写博客啦。一般写博客的流程是下面这样。

### 写博客前

不管你本地的仓库是否是最新的，都先pull一下，以防万一：

```
git pull origin hexo
```



把最新的pull下来，再开始撰写新的博客。

### 写博客

```
hexo new "title"
```

然后打开`source/_posts/title.md`，撰写博文。

### 写完博客

先推送到hexo分支上：

```
git add .
git commit -m "add article xxx"
git push origin hexo
```

最后部署到master分支上：

```
hexo g -d
```

整个流程大概就是这样。