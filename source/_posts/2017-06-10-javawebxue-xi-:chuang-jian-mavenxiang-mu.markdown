---
layout: post
title: "Javaweb学习：创建Maven项目"
date: 2017-06-10 14:12:17 +0800
comments: true
disqus: false
categories: IDEA Java Javaweb web Maven
---

### 前言
做项目开发，前期最重要的就是规范和整体架构，**Maven**正好可以规范这些方面，它是一个项目管理工具，是做java开发的不二之选。
<!--more-->

### Maven的作用
1. 在开发中，为了保证编译通过，我们会到处去寻找jar包，当编译通过了，运行的时候，却发现"ClassNotFoundException"，我们想到的是，难道还差jar包？
2. 每个Java项目的目录结构都没有一个统一的标准，配置文件到处都是，单元测试代码到底应该放在那里也没有一个权威的规范。
3. Maven主要做了两件事：
    * 统一开发规范与工具
    * 统一管理jar包

### 搭建项目
Maven的具体目录结构，不是本文的重点，这里就不做介绍了。可以 [参考链接](http://www.cnblogs.com/hongwz/p/5456578.html)

这里介绍如何搭建项目：

1、 配置Maven，参考下图：
![](/images/2017/06/10/01.jpg)

* 如上图标注 1 所示，Import Maven projects automatically 表示 IntelliJ IDEA 会实时监控项目的 pom.xml 文件，进行项目变动设置。
* 如上图标注2 所示，在 Maven 导入依赖包的时候是否自动下载源码和文档。默认是没有勾选的，也不建议勾选，原因是这样可以加快项目从外网导入依赖包的速度，如果我们需要源码和文档的时候我们到时候再针对某个依赖包进行联网下载即可。IntelliJ IDEA 支持直接从公网下载源码和文档的。

2、 创建java web项目

创建**Project**，如下图：
![](/images/2017/06/10/02.jpg)

![](/images/2017/06/10/03.jpg)
**GroupId** 是项目组织唯一的标识符，实际对应JAVA的包的结构，是main目录里java的目录结构。

**ArtifactId** 就是项目的唯一的标识符，实际对应项目的名称，就是项目根目录的名称。一般GroupId就是填com.zhl.hellotest这样子。

**Version** 1.0.0-SNAPSHOT 表示还在开发中。

一步一步创建完成之后，会创建成功，如果缺少jar包则会去自动下载。
>[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 9.553 s
[INFO] Finished at: 2017-06-05T10:10:35+08:00
[INFO] Final Memory: 12M/126M
[INFO] ------------------------------------------------------------------------
[INFO] Maven execution finished

创建成功的项目目录如下：
![](/images/2017/06/10/04.jpg)

### Java web 启动配置

1、 配置 Facets

![](/images/2017/06/10/05.jpg)

![](/images/2017/06/10/06.jpg)

2、 配置 Artifacts

![](/images/2017/06/10/07.jpg)

![](/images/2017/06/10/08.jpg)

3、 配置 Edit Configurations

![](/images/2017/06/10/09.jpg)

![](/images/2017/06/10/10.jpg)

![](/images/2017/06/10/11.jpg)

### 运行项目
![](/images/2017/06/10/12.jpg)
之后会自动打开浏览器的 <http://localhost:8080> 看到 **Hello World!**。

### 创建java war发布包
war包放入Tomcat后会自动解压部署，非常适合于线上环境的部署。

1、 配置 war包的Artifacts

选择从已经存在的项目打包。
![](/images/2017/06/10/13.jpg)

单独为war包配置Tomcat也是可以的，这里配置在已经存在的运行环境里。
在运行 **hellotest:war exploded**里，配置build **hellotest:war**。
![](/images/2017/06/10/14.jpg)

这样在运行Tomcat之后，会在 **out** 目录内生成 **hellotest_war.war** 的发布包。

### 参考
<http://www.cnblogs.com/hongwz/p/5456578.html>
<http://blog.csdn.net/myarrow/article/details/50824793>


