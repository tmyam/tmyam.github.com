---
layout: post
title: "为博客添加分类和评论功能"
date: 2013-10-09 15:03
comments: true
disqus: false
categories: Octopress
---
###1.右侧边栏分类
* [参考链接](http://codemacro.com/2012/07/18/add-category-list-to-octopress/)  
如果想使用中文名分类，可以修改 *category_list.html* 中的`<h1>Categories</h1>`为
  `<h1>文章分类</h1>`。
<!-- more -->

###2.添加关于页面
[参考链接](<http://www.cnblogs.com/oec2003/archive/2013/05/31/3109577.html>)   

打开文件source\\_includes\custom\navigation.html，默认情况下如下所示：

``` html
<ul class="main-navigation"> 
  <li><a href="{{ root_url }}/">Blog</a></li> 
  <li><a href="{{ root_url }}/blog/archives">Archives</a></li> 
</ul>
```
可以在此添加一个标签页，使用命令：

``` sh
rake new_page['about']
```
创建一个页面，页面路径为source\about\index.markdown;

修改上面的navigation.html为：

``` html
<ul class="main-navigation"> 
  <li><a href="{{ root_url }}/">首页</a></li> 
  <li><a href="{{ root_url }}/blog/archives">归档</a></li> 
  <li><a href="{{ root_url }}/about">关于</a></li> 
</ul>
```

###~~3.添加disqus评论~~
* 使用第三方的评论系统，在[disqus](http://www.disqus.com)注册一个账号，并登录。

* 参考下图，点击`Dashboard`  
 
![temp](/images/2013/10/09/comment_dashboard.png)

* 然后点击下图中的`add`按钮  

![temp](/images/2013/10/09/comment_add.png)

* 之后根据提示添加信息，一步一步直到完成。

* 然后修改**_config.yml**文件

``` sh
# Disqus Comments 
disqus_short_name: tmyam   # tmyam为添加站点信息时的Site Shortname (注意前面必须有空格)
disqus_show_comment_count: true
```

###4.添加多说评论
disqus作为国外的评论系统，可用的登录基本都被墙了，所以选择国内的系统还是很有必要的。  

* 到 [多说](http://duoshuo.com) 关联个账号,然后点击首页的`我要安装`,填写信息。

* 具体安装 [参考链接](http://havee.me/internet/2013-02/add-duoshuo-commemt-system-into-octopress.html)  
**注意：** 为了让多说支持https，添加**source/_includes/post/duoshuo.html**文件时，需要将`ds.src = 'http://static.duoshuo.com/embed.js';`这句改为`ds.src = 'https://static.duoshuo.com/embed.js';`   
这样，多说就同时支持https了。

<br/>
### 参考  
<http://www.cnblogs.com/oec2003/archive/2013/05/31/3109577.html>

