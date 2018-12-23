---
title: Hexo搭建博客总结
date: 2018-06-30 19:46:24
categories: 日常总结
tags: 搭建博客
---

## 前言

其实一直想要有个固定的地方供自己记录学习、思考、总结的过程，与大家分享自己的故事，类似可以提供这样功能的平台也有很多，比如微信公众号、简书、CSDN、博客园等，之前也分别尝试过在别的平台写东西，但是种种原因觉得好像不太适合，没能坚持下去。开始接触前端后发现了很多人都有坚持写博客的习惯，有的写的很好，比如很有名的阮一峰前辈，昨天进一步了解才知道他坚持写博客有15年了，一口气读了很多篇他的博客，写作风格自己很喜欢，也发现其实他最开始的博客写的也比较简单，后面才写的越来越好，这就是坚持的力量！自己也很想成为那样的人，可以坚持一件有意义的事情很多年，所以今天也终于行动起来，动手搭建了这个博客，比较清新简洁，自己也很喜欢，是自己付出这么多时间搭建起来的属于自己的网站，相信更有动力坚持去写博客！

第一篇博客自然就是记录一下自己利用Hero搭建博客的过程了。网上关于Hero搭建博客的教程真的好多，这里我就不写的那么详细了，主要记录一下自己遇到的问题，和自己觉得关键的一些点，会结合着放一些自己参考过的资料。

## 博客搭建步骤

主要需要安装nodejs，git，hexo，并进行相关配置，推荐我用到的两个简洁的博客搭建教程：

[30分钟学会用Hexo搭建个人博客](https://www.jianshu.com/p/21be50c1cac3)

[hexo与github pages搭建博客教程](http://blog.zmcss.com/2015/10/13/hexo%E4%B8%8Egithub_pages%E6%90%AD%E5%BB%BA%E5%8D%9A%E5%AE%A2%E6%95%99%E7%A8%8B/)

记录几个常用命令：

- hexo init //初始化一个文件夹，它会为这个文件夹配置所有骨架
-  hexo g //即hexo generate，生成静态文件
-  hexo s //即hexo server，创建服务，后可通过地址`localhost:4000`查看 
-  hexo d //即hexo deploy,用于将本地文件发布到github上
-  hexo n //即hexo new,用于新建一篇文章

### 遇到的问题及解决办法

**问题1：**

出现npm ERR!

**解决办法：**

1、执行：

```
npm config get proxy
npm config get https-proxy
```

如果返回值不为null，继续执行：

```
npm config set proxy null
npm config set https-proxy null
```

2、执行：

```
npm config set registry http://registry.cnpmjs.org/
```

3、直接编辑c盘下的.npmrc文件，将registry的值修改为：

```
registry = http://registry.cnpmjs.org
```

 参考链接：[npm ERR!无法安装任何包的解决办法](https://blog.csdn.net/mygrilzhuyulin/article/details/52845938)

上面两个简单的教程不够用的话，再推荐一个更详细一些的：

[hexo你的博客](http://ibruce.info/2013/11/22/hexo-your-blog/)

### 主题的选择安装

简单的博客搭建起来了，接着就是主题的选择了，主题选择可以参考知乎回答：

[有哪些好看的 Hexo 主题？](https://www.zhihu.com/question/24422335)

个人比较喜欢star排名第一的hexo-theme-next主题，本博客就是采用的NexT中的mist主题。

[hexo-theme-next主题下载安装](https://github.com/iissnan/hexo-theme-next )

推荐一篇自己参考的比较全的关于NexT主题配置的文章：

[Hexo之NexT主题的配置及遇到的问题](https://juejin.im/post/5a71ab9f518825735300ee6c)

另推荐一个用切换mist主题的详细博文：

[Hexo博客主题从Jacman切换到NexT.Mist](http://codepub.cn/2016/03/20/Hexo-blog-theme-switching-from-Jacman-to-NexT-Mist/)

### 主题配置

另一些配置功能可参考NexT官网的主题配置和第三方服务：

[NexT官网主题配置](http://theme-next.iissnan.com/theme-settings.html)

下面介绍一些我所用到的配置及步骤。

注：我用的是目前NexT最新的版本v5.1.4。

#### favicon图标

添加favicon图标可用[比特虫](http://www.bitbug.net/)在线转换，也支持其他图片格式，我是在主题配置文件中修改favicon相关的如下代码块：

~~~
# For example, you put your favicons into `hexo-site/source/images` directory.
# Then need to rename & redefine they on any other names, otherwise icons from Next will rewrite your custom icons in Hexo.
favicon:
  small: favicon.ico
  medium: favicon.ico #/images/favicon-32x32-next.png
  apple_touch_icon:  favicon.ico #/images/apple-touch-icon-next.png
  safari_pinned_tab: favicon.ico #/images/logo.svg
~~~

要主题文件的路径，根据说明设置。

#### 首页不显示全文

设置NextT主题首页不显示全文参考：

[Hexo之next主题设置首页不显示全文(只显示预览)](https://www.jianshu.com/p/393d067dba8d)

#### **本地搜索**

添加百度/谷歌/本地 自定义站点内容搜索

1. 安装 `hexo-generator-searchdb`，在站点的根目录下执行以下命令：

   ```
   $ npm install hexo-generator-searchdb --save
   ```

2. 编辑 站点配置文件，新增以下内容到任意位置：

   ```
   search:
     path: search.xml
     field: post
     format: html
     limit: 10000
   ```

3. 编辑 主题配置文件，启用本地搜索功能：

   ```
   # Local search
   local_search:
     enable: true
   ```

#### 评论功能

使用的来必力，登陆 [来必力](https://livere.com/) 获取你的 LiveRe UID。 编辑 主题配置文件， 编辑 `livere_uid` 字段，设置如下：

```
livere_uid: #your livere_uid
```

#### 阅读次数统计

使用的LeanCloud，具体参考文章[为NexT主题添加文章阅读量统计功能](http://theme-next.iissnan.com/third-party-services.html)

#### 分享功能

要在每篇文章底部增加分享方式，可在主题配置文件中找到needmoreshare2相关字段，将地下的enable改为true，代码块如下所示：

```
needmoreshare2:
  enable: true
  postbottom:
    enable: true
    options:
      iconStyle: box
      boxForm: horizontal
      position: bottomCenter
      networks: Weibo,Wechat,Douban,QQZone,Twitter,Facebook
```

#### 添加字数统计、阅读时长、友情链接

参考文章链接：

[Hexo添加字数统计、阅读时长、友情链接](https://www.jianshu.com/p/6908ac34bbb2)

#### 开启打赏功能

在主题配置文件中填入微信和支付宝收款二维码图片地址 即可开启该功能。

打赏功能配置示例

```
reward_comment: 坚持原创技术分享，您的支持将鼓励我继续创作！
wechatpay: /path/to/wechat-reward-image
alipay: /path/to/alipay-reward-image
```

#### 添加版权信息

在主题配置文件设置`post_copyright`相关代码块如下：

~~~
# Declare license on posts
post_copyright:
  enable: true
  license: CC BY-NC-SA 3.0
  license_url: https://creativecommons.org/licenses/by-nc-sa/3.0/
~~~

####  设置标签、分类和归档

但是在 NexT 主题下的设置的步骤如下：

1. 先`hexo new page categories`新建 categorier 文件夹，其中会自动生成一个`index.md`文件，修改（即添加一行）为：

   ```
   ---
   title: categories
   date: 2018-01-23 17:14:51
   type: "categories"
   ---
   ```

   同理，「标签」也一样，`hexo new page tags`，生成 tags 文件夹，其中会自动生成一个`index.md`文件，修改为：

   ```
   ---
   title: tags
   date: 2018-01-23 17:14:51
   type: "tags"
   ---
   ```

2. 然后写的博客文章，**表明所属「分类」和拥有哪些「标签」**，官方文档所说的格式如下：

   ```
   categories:
   - Diary
   tags:
   - PS3
   - Games
   ```

   但是我亲测，如下也是可以的：

   ```
   categories: 技术
   tags: [编程,感悟]
   ```

**备注：** 对于 NexT 这个主题，对于 **「关于」** 这个菜单和上面新建分类和标签一样，也是需要手动创建文件夹的，`hexo new page about`，这样会生成 about 文件夹，同时自动生成`index.md`文件，然后可以在这个`.md`文件中写上自我个人介绍。（亲测过，`index.md`这个文件的名字不能修改，否则「关于」菜单出错）

另外说几句，关于这部分的设置，官方文档称其为「Front-matter」，「Front-matter」 是文件最上方以 `---` 分隔的区域，用于指定个别文件的变量，举例来说：

```
title: Hello World
date: 2013/7/13 20:46:25
---
```

以下是预先定义的参数，您可在模板中使用这些参数值并加以利用。

| 参数       | 描述                 | 默认值       |
| ---------- | -------------------- | ------------ |
| layout     | 布局                 |              |
| title      | 标题                 |              |
| date       | 建立日期             | 文件建立日期 |
| updated    | 更新日期             | 文件更新日期 |
| comments   | 开启文章的评论功能   | true         |
| tags       | 标签（不适用于分页） |              |
| categories | 分类（不适用于分页） |              |
| permalink  | 覆盖文章网址         |              |

参考链接：

[Hexo之NexT主题的配置及遇到的问题](https://juejin.im/post/5a71ab9f518825735300ee6c)

#### **修改首页、归档页、分类页、标签页显示文章数量** 

**开启关闭分页功能**

修改配置文件`hexo/_config.yml`，找到如下内容：

```
# Pagination
## Set per_page to 0 to disable pagination
per_page: 10 
pagination_dir: page
```

其中，`per_page`即为你想要设置的分页个数，如果不想分页的话，设置`per_page: 0`即可

**个性化页面分页**

> 一般希望首页展示文章的个数少一些，归档页等页面的文章数目多一些

需要安装一下插件：

```
hexo-generator-archive  //归档页
hexo-generator-category     //分类页
hexo-generator-index    //首页
hexo-generator-tag      //标签页
```

安装方法：
hexo3.0版本已安装
手动安装方法：

```
npm install hexo-generator-archive --save
npm install hexo-generator-category --save
npm install hexo-generator-index --save
npm install hexo-generator-tag --save
```

安装完成后，修改配置文件`hexo/_config.yml`,添加如下内容

```
# Plugins
index_generator:
  per_page: 5 ##首页默认5篇文章标题，如果值为0不分页

archive_generator:
  per_page: 20 ##归档页面默认20篇文章标题，如果值为0不分页
  yearly: true ##生成年视图
  monthly: true ##生成月视图

tag_generator:
  per_page: 20 ##标签页面默认20篇文章，如果值为0不分页

category_generator: 
  per_page: 20 ##分类页面默认20篇文章，如果值为0不分页
```

参考文章链接：

[分别设置hexo首页、标签页、归档页文章个数](http://jimxu.me/2016/07/06/set-hexo-page-size/)

#### 博客上传图片

主要有本地引用和cdn引用两种方法。

**本地引用**

方法一：

由于首页和主页相对图片位置不一致，网站首页和内页很可能仅有一处能正常加载。主要是主页和图片的相对位置和内页和图片的相对位置不一致。可以通过以下方式尝试。

- 1 调整程序配置文件_config.yml 里的post_asset_folder:这个选项设置为True
- 2 在source下创建/images文件夹，先把图片xx.jpg/png复制到这个文件夹
- 3 最后在xxxx.md中想引入图片时，只需要在xxxx.md中按照markdown的格式引入图片：

```
![想输入的提示名字，可不输入](/images/xx.jpg)
```

 方法二：

1、`cd`到博客等根目录下 查看`_config.yml`文件 查找 `post_asset_folder` 字段确定`post_asset_folder` 设置为`true` -> `post_asset_folder:true`

2、当您设置 `post_asset_folder`  参数后，在建立文件时，Hexo 会自动建立一个与文章同名的文件夹，您可以把与该文章相关的所有资源都放到那个文件夹，这样你就可以更方便的使用资源。

3、在博客的根目录执行 `npm install https://github.com/CodeFalling/hexo-asset-image --save` 命令来进行插件的安装。

4、然后创建一文章 `hexo new "test"` 然后查看博客的 `../source/_posts` 目录下的文件，会看到存在一个`test 文件夹` 和 `test.md` 文件

5、将所需要的图片资源放到`test 文件夹` 内 目录结构如下：

6、书写文章使用`test文件内` 的图片

7、使用`hexo s` 命令运行本地博客如图

8、使用`hexo clean` `hexo g` `hexo deploy` 将本地博客推送到远程。

**CDN引用**

除了在本地存储图片，还可以将图片上传到一些免费的CDN服务中。比如[Cloudinary](http://cloudinary.com/)提供的图片CDN服务，在Cloudinary中上传图片后，会生成对应的url地址，将地址直接拿来引用即可。

参考链接：

[Hexo博客搭建之在文章中插入图片](https://yanyinhong.github.io/2017/05/02/How-to-insert-image-in-hexo-post/)

 [hexo 搭建博客上传图片问题](https://www.jianshu.com/p/5f75a74b0f5e)

[Hexo怎样上传本地图片？](https://www.zhihu.com/question/263841693)

 

 

 