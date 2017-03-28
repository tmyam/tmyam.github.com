---
layout: post
title: "Linux下搭建SVN服务"
date: 2017-03-28 11:40:39 +0800
comments: true
disqus: false
categories: Linux SVN 树莓派
---
## 前言
最近打算迁移自己项目的SVN代码，这里对搭建SVN服务进行一下回顾。

## 相关软件安装
* 使用SSH远程登录服务器

``` sh
ssh pi@192.168.1.201 # 例如
```
<!-- more -->
* 安装软件

``` sh
sudo apt-get install subversion
sudo apt-get install apache2
sudo apt-get install libapache2-svn
```
其中subversion是SVN必须的，apache2和libapache2-svn是为了配置SVN服务器支持通过HTTP访问

## 配置

### 相关用户、组的设定

``` sh
sudo addgroup subversion
sudo usermod -G subversion -a www-data
```
### 创建项目的目录

``` sh
sudo mkdir /home/svn
```

### 配置 Subversion

* 配置dav_svn.conf文件

``` sh
sudo nano /etc/apache2/mods-available/dav_svn.conf
```
按照下面的步骤进行配置即可

1、删除<Location /svn>和DAV svn这两行前面的注释
![temp](/images/2017/03/28/svn_01.png)

2、删除`SVNParentPath ...`前面的注释，并且把后面的路径替换成创建的SVN的项目路径`/home/svn`
![temp](/images/2017/03/28/svn_02.png)

3、删除AuthType Basic、AuthName “Subversion Repository”前面的注释，去掉AuthUserFile所在行前面的注释，并且修改后面的路径为`/etc/subversion/dav_svn.passwd`，去掉AuthzSVNAccessFile所在行前面的注释，并且修改后面的路径为`/etc/subversion/dav_svn.authz`,删除Require valid-user和</Location>前面的注释，具体如下所示：
![temp](/images/2017/03/28/svn_03.png)

其中：

`/etc/subversion/dav_svn.passwd`文件是SVN用户名和密码的配置，指定基本用户验证的密码文件存放的位置   

`/etc/subversion/dav_svn.authz`文件是访问权限配置

* 重启Apache2 WEB服务器

``` sh
sudo /etc/init.d/apache2 restart
```

### 创建SVN文件仓库
1、创建指定的项目存放路径

``` sh
cd /home/svn
mkdir projects
```

2、更改版本库所示的用户、组

``` sh
sudo chown -R www-data:subversion projects
```

3、创建SVN仓库

``` sh
sudo svnadmin create /home/svn/projects
```

4、赋予组成员对所有新加入文件仓库的文件拥有相应的权限

``` sh
sudo chmod -R g+rws projects
```
### 用户和权限配置
1、创建dav_svn.passwd文件并添加用户admin001，执行命令后会提示要输入密码

``` sh
sudo htpasswd -c /etc/subversion/dav_svn.passwd admin001
```

继续添加新用户，去掉-c，否则会覆盖本文件


``` sh
sudo htpasswd /etc/subversion/dav_svn.passwd admin002
```

2、创建授权文件

``` sh
sudo nano /etc/subversion/dav_svn.authz
```
里面加入projects的权限配置，如

``` text
[groups]
administrator=admin001,admin002

[projects:/]
@administrator=rw
```

### 重启SVN服务器

``` sh
killall svnserve
svnserve -d -r /home/svn/
```

至此SVN服务器搭建完成，可以在终端执行来下面代码checkout项目了

``` sh
svn checkout http://hostname/svn/projects projects --username 用户名 --password 密码
```

### 参考
<http://blog.devzeng.com/blog/aliyun-ubuntu-svn-server.html>

