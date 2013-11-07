---
layout: post
title: "mac下编写blog"
date: 2013-10-09 10:52
comments: true
disqus: false
categories: Octopresss
---

##1.编写

* 下载markdown的编辑工具,[Mou]

* 在终端的octopress目录下，输入

``` sh
rake new_post['article name']
```
然后可以在source/_post/目录下找到一个.markdown文件，使用[Mou]打开它，写入你想写的内容。
<!-- more -->

* 图片  
如果要在文章中使用图片，直接copy到**/source/images**目录下即可。在文章中可以直接使用`![temp](/images/temp.png)`。也可以选一些大的图床站点，使用链接的方式。  
例如：![temp](/images/2013/10/09/temp.png)

* 首页缩略显示  
在首页展示文章的一部分内容，而不是显示全部。  
只需要在文章的markdown文件中，在需要显示在首页的文字后面添加`<!-- more -->`就行了。

如果想将Read on 修改为中文，可以修改_config.yml文件  
excerpt_link: "Read on &rarr;"  # "Continue reading" link text at the bottom of excerpted articles 中的`Read on` 改为 `阅读全文`。

##2.代码高亮
octopress原生支持代码高亮，[参考链接](http://octopress.org/docs/blogging/code/)   
使用`Backtick`来实现语法高亮，[参考链接](http://m.blog.csdn.net/blog/trochiluses/12906265)   

* 语法   
>\` \` \` [language] [title] [url] [link text] [linenos:false] [start:#] [mark:#,#-#]   
code snippet   
\` \` \`   

language: 指明你所使用的语言   
title: 代码标题   
url: 代码链接，点击这个可以下载你的代码   
link text: 对链接的解释文本，默认是link   
start:#: 行号从哪里开始   
mark:#,#-#: 标记某一行代码   
linenos:false: 对高亮代码块不添加行号   

例如：   
>\` \` \` sh   
rake new_post['article name']   
\` \` \`   

显示为
``` sh
rake new_post['article name']
```   
由于一旦设置了高亮格式，之后所有的代码高亮都会使用这种格式，有好处也有坏处。可以在下一个代码高亮的地方将`language`设置成`text`来取消对下面的影响。

* 常用的高亮格式，[参考链接](http://vvstyle.com/blog/2013/08/13/backtick-code/)   
text: 无格式   
sh: shell脚本   
objective-c: Objective-C   


##3.生成
* 生成静态文件

``` text
rake generate 
```
* 预览  
地址 <http://localhost:4000> ,时时的修改markdown文件，可以刷新网页看到刚刚修改的效果。

```
rake preview
```

* 部署到GitHub上

```
rake deploy
```
##4.备份
将文件更新备份到github上。

``` sh
git add -A
git commit -m 'your message'
git push origin source
```
如果遇到**warning**
>warning: push.default is unset; its implicit value is changing in Git 2.0 from 'matching' to 'simple'. To squelch this messageand maintain the current behavior after the default changes, use:...

推荐使用下面命令设置。

```
git config --global push.default simple
```

<br/>
###参考
<http://blog.devtang.com/blog/2012/02/10/setup-blog-based-on-github/>





[Mou]:http://mouapp.com