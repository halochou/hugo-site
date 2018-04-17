---
date: "2014-01-13T16:25:00Z"
tags:
- jekyll
- blog
title: 折腾Jekyll的收获
---

还是忍不住又折腾了一次，花了不少时间把blog迁移到Jekyll了，不过收获还是不小的。

原本用hexo挺好的，忍不住研究了一下Jekyll的目录结构，比hexo简洁许多，虽然需要自己动手的地方多些，但就当学习Web前端技术了。

折腾期间按照Instapaper的风格fork出了一套主题，已传到[Github](https://github.com/halochou/Instapaper-Jekyll-theme)了。改得很粗糙，留着以后慢慢雕琢吧

现在Blog用的主题是[Minimal Mistakes](http://mademistakes.com)，自己做了一些符合天朝特色的[定制](https://github.com/halochou/halochou.github.com)。

此番对Web前端技术的认识有了不少提高，简单整理一下。

#Bootstrap
Twitter工程师开发的前端框架，我理解就是一组预定好的网页组件、布局的CSS，只包含前端视觉部分，可用于任何html设计环境。

基本模板：

    <!DOCTYPE html>
    <html>
      <head>
        <title>Bootstrap 101 Template</title>
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <!-- Bootstrap -->
        <link href="css/bootstrap.min.css" rel="stylesheet">
        <!-- HTML5 Shim and Respond.js IE8 support of HTML5 elements and media queries -->
        <!-- WARNING: Respond.js doesn't work if you view the page via file:// -->
        <!--[if lt IE 9]>
          <script src="https://oss.maxcdn.com/libs/html5shiv/3.7.0/html5shiv.js"></script>
          <script src="https://oss.maxcdn.com/libs/respond.js/1.3.0/respond.min.js"></script>
        <![endif]-->
      </head>
      <body>
        <h1>Hello, world!</h1>
        <!-- jQuery (necessary for Bootstrap's JavaScript plugins) -->
        <script src="https://code.jquery.com/jquery.js"></script>
        <!-- Include all compiled plugins (below), or include individual files as needed -->
        <script src="js/bootstrap.min.js"></script>
      </body>
    </html>

模板包含最基本CSS、JS文件的导入，之后只需在body部分添加自己的网页组件即可。采用12列形式布局，如果需要对CSS进行定制，则建立自己的CSS文件，在bootstrap.css之后导入覆盖即可。

#CSS
一直也没系统学习过CSS，不过通过定制Jekyll主题，倒是对CSS有了个大致了解。

CSS用于统一定义网页样式，将html内容数据与样式信息分离解耦。

##CSS基本语法：

    selector {
        declaration1; 
        declaration2; 
        ... 
        declarationN 
    }

selector（选择器）是需要定制的html元素，如`h1`，`h2`，`p`，`li`等。
declaration通常是相关属性的赋值。

##派生选择器：

    strong {
         color: red;
         }
    h2 {
         color: red;
         }
    h2 strong {
         color: blue;
         }

选择器通常有不同状态，比如一段文字<p></p>，可能有href超链接属性，超链接又存在默认、鼠标悬停、点击等不同的状态，派生选择器通过显式声明追加新的样式定义，类似程序语言中的函数重载。

##id选择器：
HTML中定义元素时，可以为特定元素添加id属性，如果此id在CSS中有定义，则该元素就按照CSS被渲染。`<div id="sidebar-wrap"></div>`就创建了一个侧边栏的容器。同一个HTML页面中，每个id都是唯一的，即一个实例。

CSS定义id选择器的样式：

    #sidebar {
        font-style: italic;
        text-align: right;
        margin-top: 0.5em;
        }

##class选择器：
相对于id，class指代一类元素的样式，同一页面中可以出现多次。

    .sidebar-item{
        color: #f60;
        background: #666;
        }

#LESS
手工编写CSS冗长切不灵活，于是产生了CSS预编译语言，LESS只是其中一种，Bootstrap默认采用LESS。LESS在CSS语法的基础上添加了许多程序语言元素。
##变量

    @color: #4D926F;
    #header {
      color: @color;
    }
    h2 {
      color: @color;
    }

##Mixins（带参函数）

    .rounded-corners (@radius: 5px) {
      -webkit-border-radius: @radius;
      -moz-border-radius: @radius;
      -ms-border-radius: @radius;
      -o-border-radius: @radius;
      border-radius: @radius;
    }
    #header {
      .rounded-corners;
    }
    #footer {
      .rounded-corners(10px);
    }

##嵌套

    #header {
      h1 {
        font-size: 26px;
        font-weight: bold;
      }
      p { font-size: 12px;
        a { text-decoration: none;
          &:hover { border-width: 1px }
        }
      }
    }

##运算

    @the-border: 1px;
    @base-color: #111;
    @red:        #842210;
    #header {
      color: (@base-color * 3);
      border-left: @the-border;
      border-right: (@the-border * 2);
    }
    #footer {
      color: (@base-color + #003300);
      border-color: desaturate(@red, 10%);
    }

LESS可以编译生成CSS，编译器基于node.js。也可以直接在HTML中加载，但性能会有折损。

    <link rel="stylesheet/less" type="text/css" href="styles.less" />
    <script src="less.js" type="text/javascript"></script>

目前掌握的大致这些，修改一下别人的主题基本够用，有时间再详细研究。
