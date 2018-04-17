---
date: "2014-01-08T10:10:18Z"
tags:
- hexo
title: Hexo折腾笔记
---

第一次搭博客，发现这还真是个挺有意思的话题。

#平台选择
本人基本上患有选择困难症，之前用Linux的时候，每个月换一次发行版，这次挑选博客平台时又毫无意外的花了很长时间抉择。经过一般挖掘，大致有以下几个选项。

* Wordpress

优点：主流博客平台，资源众多，易上手。

缺点：作为老牌平台，有些臃肿，我也不计划对PHP深挖，不便未来折腾；Wordpress.com因某些原因无法访问（You Know），其他国内平台又不大想用，自己搭的话，主页空间自由度不高，VPS又太贵~

* Ghost

主页介绍看起来很诱人，可惜没有免费服务，只能自己在本地搭个玩玩，等以后有VPS了再折腾吧。

* Jekyll / Octopress

Github Pages默认引擎，好多大神貌似都在用。试用了一下，还是挺不错的，符合高度定制化的Geek风格，。不过就是太折腾，都搭好了估计早就懒得写博文了……另外据说文章多了性能是个问题。

* Hexo

最终选定Hexo，台湾同胞的作品，难望其项背啊。看看人家的功力，再看看自己…唉……

优点：Hexo是一个基于Node.js的博客框架，十分简约，默认主题很优雅，效率很好，而且可以生成静态网页后自动部署到Github上，一举两得。

缺点：还是有点小众，资源略少，出了Bug Google未必知道。

#Hexo安装
[Hexo官网](http://zespia.tw/hexo/docs/setup.html)有很详细的介绍，其实只要一个命令就足够了。

    npm install -g hexo

之后初始化自己的博客就齐了。

    hexo init blog

默认目录结构：

    .
    ├── _config.yml
    ├── package.json
    ├── scaffolds
    ├── scripts
    ├── source
    |   ├── _drafts
    |   └── _posts
    └── themes

#Hexo优化
Hexo的初始配置很简约，而且默认的评论分享模块不符合天朝国情（此处省略一万字……），还是要自己打理一下才好。做了修改之后，可以用这个命令测试一下：

    hexo server

或者

    hexo s

默认配置会在localhost:4000建立一个webserver，自行查看即可。

##主题
通过npm安装过来的hexo默认主题是landscape，不是传说中的light，原因不明（下一代？没light好看），有知道的朋友还望告诉一声。

安装light，在blog根目录下执行：

    git clone git://github.com/tommy351/hexo-theme-light.git themes/light

修改`/_config.yml`：

    theme: light

##图片文件夹
使用markdown写文章，插入图片的格式为`![图片名称](链接地址)`，其中链接地址是个url，这里针对hexo，可以创建`/source/img`目录，引用时用下面语句即可：

    ![lable](/img/hexo-github-deploy/01.png)  

##配置网站信息
`/_config.yml`文件存储了该blog的全局信息，修改其中Site字段：
{{< highlight yaml >}}
    # Site
    title: ZhouYang's Blog
    subtitle: Tech & Life Record
    description:
    author: Zhou Yang
    email:halo.chou@gmail.com
    language: zh-CN
    # URL
    url: http://halochou.github.com
{{< / highlight >}}
##替换评论模块
hexo默认使用国外比较流行的disqus评论模块。

说实话，之前没研究过，原来评论系统也可以是一个单独的提供商。disqus支持的登录帐号包括Google+、Facebook……你懂了。

国内提供类似服务的是[多说](http://duoshuo.com)，注册一个帐号后可以拿到针对自己的通用代码。

修改`/themes/light/layout/_partial/comment.ejs`，
删除原有的关于disqus和facebook的代码：

{{< highlight html >}}
    <% if (page.comments){ %>
    <section id="comment">
      <h1 class="title"><%= __('comment') %></h1>
      <! Your Code >
    </section>
    <% } %>
{{< / highlight >}}
##替换分享模块
原因同上……

在[百度分享](http://share.baidu.com/code)获取代码，并在`themes/light/layout/_partial/article.ejs`中，将`<%-partial('post/share')%>`替换掉即可。

##添加Feed
虽说什么都没写呢，也根本没人来看，但是折腾是无止境的╮(╯_╰)╭

安装feed插件

    npm install hexo-generator-feed

在`/_config.yml`中，添加以下代码：

    plugins:
    - hexo-generator-feed

在`themes/light/_config.yml`中，修改：

    rss: /atom.xml

在`themes/light/layout/_partial/header.ejs`中，`<ul>`和`</ul>`之间，添加代码：

    <li> <a href="/atom.xml">RSS</a> </li>

如果node.js正在运行，记得重启，npm安装了插件是需要重启生效的。

##添加sitemap模块
和添加RSS类似，执行下面命令：

    npm install hexo-generator-sitemap

在`/_config.yml`中，添加代码：

{{< highlight html >}}
    plugins:
    - hexo-generator-feed
    - hexo-generator-sitemap
{{< / highlight >}}

访问 http://halochou.github.com/sitemap.xml 即可看到网站地图。

#部署GitHub
Hexo部署到GitHub是通过先生成静态网站代码，再`git push`到GitHub上完成的。
##建立GitHub Repo
首先新建一个Repo，名称应是username.github.com，如果不想放在这个域名的根目录里另有他法，之后再补充。

##向Hexo添加部署信息
修改`/_config.yml`：

{{< highlight html >}}
    deploy:
        type: github
        repo: git@github.com:halochou/halochou.github.com.git
{{< / highlight >}}

##生成静态站点
只需要一条命令：

    hexo generate
或者

    hexo g

##部署站点
还是只有一个命令：

    hexo deploy
或者

    hexo d

#Bug
我在配置Hexo过程中，出现过`hexo s`正常，但是`hexo g`报错`EISDIR`的故障，与使用`hexo new page "about"`有关，new page在`/source`内创建了一个文件夹，但hexo g无法识别，暂未找到解决办法，建议在配置Hexo过程中多generate，节省Debug时间。
