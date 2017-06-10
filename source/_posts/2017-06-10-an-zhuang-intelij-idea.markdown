---
layout: post
title: "安装InteliJ IDEA"
date: 2017-06-10 14:05:50 +0800
comments: true
disqus: false
categories: IDEA Java 
---

### 前言
InteliJ IDEA作为java开发工具，有其优越性存在。作为一位java初学者，选择IDE非常重要，考虑到Android Studio是在社区版的IDEA上开发的，为了减少以后的学习成本，IDEA是一个不错的选择。

### 安装
1、 在官网下载软件安装包 [下载地址](http://www.jetbrains.com/idea/download)

 **Ultimate**（旗舰版），**Community**（社区版，开源版）
<!-- more -->

2、 安装
细节就不说了，很简单。

### 目录结构
网上关于IDEA创建的工程，目录结构总是和Eclipse作对比，对于没用过Eclipse的人来说，仍是一头雾水。
我的理解是**Project**是一个完整的项目，**Module**是项目中的模块，模块可以是包，可以是分层等。

### 配置
* 安装最新的JDK [下载地址](http://www.oracle.com/technetwork/java/javase/downloads/index.html)

注意是安装 **Java SE Development Kit**，不是 **Java SE Runtime Environment**。

终端输入命令:

``` sh
java -version
```
输出：
>java version "1.8.0_131"
Java(TM) SE Runtime Environment (build 1.8.0_131-b11)
Java HotSpot(TM) 64-Bit Server VM (build 25.131-b11, mixed mode)

* 安装Tomcat

1、 下载最新的Tomcat [下载地址](http://tomcat.apache.org)

将下载的Tomcat压缩包解压缩，得到Tomcat文件夹，文件夹可以修改名称。
然后将文件夹放入一个目录当中，这个目录可以由自己选择。这里我将内容放在：

``` sh
~/Library/Tomcat/8.5.15/
```
2、 修改权限
启动终端：输入下面代码，以使shell文件可以正常运行。

``` sh
sudo chmod 755 ~/Library/Tomcat/8.5.15/bin/*.sh
```
3、 验证
在终端输入

``` sh
~/Library/Tomcat/8.5.15/bin/startup.sh
```
输出结果没有错误，表示安装成功，打开 [http://localhost:8080/](http://localhost:8080/) 可以看到主页。

4、 关闭

``` sh
~/Library/Tomcat/8.5.15/bin/shutdown.sh
```
* 配置Tomcat

启动IDEA，打开**Build,Execution,Deployment**，参考下图设置：
![temp](/images/2017/06/10/idea_01.jpg)


### 参考
<http://blog.csdn.net/u013673242/article/details/72243066>
<http://blog.csdn.net/qq_15096707/article/details/51142509>
<http://www.oracle.com/technetwork/java/javase/downloads/index.html>
<http://www.jianshu.com/p/bd5b3ece8158>
<http://blog.csdn.net/huyisu/article/details/38372663>


