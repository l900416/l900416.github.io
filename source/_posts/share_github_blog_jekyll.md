---
layout:     post
title:      Mac下使用Jekyll搭建GitHub博客
date:       2017-08-07 16:25:19
author:     liangtong
categories: 技术分享
tags: GitHub

---

[Jekyll](https://github.com/jekyll/jekyll)是一个静态网站和博客模版，本文介绍如何使用Jekyll进行个人GitHub博客的搭建。搭建前提是拥有个人[Github](https://github.com/)账号，且Mac上有安装 *git* 工具。

### 安装Jekyll  
  参照[Jekyll官方](https://jekyllrb.com/)介绍，打开终端，通过以下命令安装：    
```Shell
$ gem install jekyll bundler
```

注：gem的安装／更新可以自己去查，此处不再进行说明。Jekyll安装成功后，便可以进行个人博客／静态网站的创建了。


### 快速使用博客   

通过在终端输入以下命令执行初始化
```Shell
$ jekyll new my_blog
$ cd my_blog
$ bundle exec jekyll serve
```
参照[官方模版配置介绍](https://jekyllrb.com/docs/templates/),修改Jekyll配置文件_config.yml，如果没有特殊要求，建议直接GitHub找现成的[博客模板](http://jekyllthemes.org/)即可。  

### 快速使用博客  
  此处选择使用博客模板[jekyll-now](https://github.com/barryclark/jekyll-now)，使用Github直接fork到自己的博客仓库中，然后将代码下载到本地。
新建博客静态文件，格式:yyyy-MM-dd-articlename.md，使用markdown语法对编写博客内容。
编写之后，直接在博客路径下，运行即可：     
```Shell
$ jekyll serve
```

### 推荐博客   
  使用Jekyll搭建博客也可以添加很多自定制功能，比如统计、评论、标签等；可以根据自己的喜好，选择不同的主题。以下是个人曾经使用过的主题：      
+ [Hux Blog](https://github.com/Huxpro/huxpro.github.io) 第一个GitHub博客系统就是从他的博客fork过去的。
+ [Clean Blog](https://github.com/BlackrockDigital/startbootstrap-clean-blog-jekyll) 简单的Jekyll博客模板。



### Jekyll在GitHub上的维护    
直接将博客源码上传到GitHub博客仓库即可。


### 其他   
通过在GitHub上搭建Jekyll和Hexo博客，个人更倾向于使用Hexo博客。

 + Jekyll    
    - 配置简单。
    - 维护简单，只需要一个Branch即可。
    - 使用简单，运行只需要 *jekyll server* 即可。
    - 发布简单，只需要将代码push到对应仓库即可。
    - 主题切换，比较麻烦。


 + Hexo
    - 配置项相对较多，但是格式比较清晰。
    - 博客和源码需要分开维护。需要两个Branch对博客系统进行维护。
    - 使用相对简单。
        + hexo clean  // 清理
        + hexo g      // 生成
        + hexo s      // 运行
    - 发布简单。
        + hexo d      // 将博客部署(到github)
        + 将博客源码push到对应仓库单独保存
    - 主题切换简单。
        + 下载新主题到themes文件夹下；
        + 修改站点配置文件_config.yml中对应的主题选项。











