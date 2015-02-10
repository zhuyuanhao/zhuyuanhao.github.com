---
layout: post
title: Jekyll 完全使用指南
category: notes
description: Jekyll是一个静态博客站点生成器，它将书写博客和发布博客分离，使作者可以专注于博客的内容而将生成网页的任务交给Jekyll完成。这篇博客的目的，在于介绍Jekyll的使用方法，作者会不断补充，尽力覆盖Jekyll的全部内容。
---

* 目录
{:toc}

#1. Jekyll简介
Jekyll是一个简单的静态站点生成器，特别适合于个人博客的场合。它能将基于文本的标记语言转换为网页，并生成一个完整的静态站点，可以发布在任何Web服务器上。特别是GitHub Page支持Jekyll站点的发布，使得Jekyll+GitHub成为了搭建个人免费博客的绝佳组合。

Jekyll支持的文本标记语言包括Markdown,Textile,Liquid。Markdown和Textile主要用于书写内容和简单的文章结构，Liquid用来控制博客的高级结构。

#2. 安装和运行
Jekyll依赖于ruby和javascript，作者的安装过程如下：


    # 安装Ruby及RubyGems
    $ sudo apt-cache search ruby 
    $ sudo apt-get install ruby1.9.1-full
    # 其他版本的ruby都会有错误，此版本ruby已经包含了rubygems
    
    # 安装JavaScript环境，比如nodejs
    $ sudo apt-get install nodejs
    
    # 使用RubyGems安装Jekyll
    $ sudo gem install jekyll
    $ sudo gem install rdiscount       # 安装markdown的rdiscount渲染器
    
安装完后，就可以建立自己的静态站点了。Jekyll不仅可以生成一个默认站点，还包含一个开发用Web服务器，可以在本地展示站点内容。

    $ jekyll new myblog     # 新建一个默认站点，放在myblog目录下
    $ cd myblog
    $ jekyll serve          # 使用内置服务器展示站点，地址为http://localhost:4000
    
Jekyll的运行命令如下

    $ jekyll build      # 当前文件夹中的内容将会生成到 ./_site 文件夹
    $ jekyll build --destination <destination>     
    $ jekyll build --source <source> --destination <destination>
    $ jekyll build --watch      # 生成站点，并自动更新
    
    $ jekyll serve      # 生成站点，并运行在开发服务器上
    $ jekyll serve --detach     # 服务器会在后台运行，如果你想关闭服务器，可以使用`kill -9`
    $ jekyll serve --watch      # 生成站点，并自动更新

#3. 目录结构
Jekyll的基本目录结构如下

    .
    ├── _config.yml     # 配置文件，和命令行设置等价
    ├── _drafts         # 草稿，这些文件的格式中都没有 title.MARKUP数据
    |   ├── begin-with-the-crazy-ideas.textile
    |   └── on-simplicity-in-technology.markdown
    ├── _includes       # 通用加载项。可以使用Liquid标签{ % include file.ext % }把文件_includes/file.ext包含进来。
    |   ├── footer.html
    |   └── header.html
    ├── _layouts        # 包裹在文章外部的布局模板。可以在YAML头信息中根据不同文章进行选择。 
    |   ├── default.html
    |   └── post.html
    ├── _posts          # 博客文章。文件名必须要符合YEAR-MONTH-DAY-title.MARKUP的格式。
    |   ├── 2007-10-29-where-is-home.textile
    |   └── 2009-04-26-make-your-life.md
    ├── _site           # 生成页面的默认文件夹。
    └── index.html      # 站点入口

其他的如 .html, .markdown, .md, 或者 .textile 等在站点根目录下或者不是以上提到的目录中的文件，如果这些文件中包含 YAML 头信息部分，Jekyll 就会自动将它们进行转换。

其他一些未被提及的目录和文件如 css 还有 images 文件夹，favicon.ico 等文件都将被完全拷贝到生成的 site 中。

