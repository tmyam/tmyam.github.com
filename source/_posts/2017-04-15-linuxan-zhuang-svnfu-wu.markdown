---
layout: post
title: "Linux安装SVN服务"
date: 2017-04-15 15:49:26 +0800
comments: true
disqus: false
categories: Linux 树莓派 SVN
---

## 安装SVN软件

``` sh
sudo apt-get install subversion
```

## 创建目录

* 新建仓库目录
<!--more-->
``` sh
sudo mkdir -p /home/svn/projects
```
一般来说，可以创建在任何位置

* 生成svn仓库

``` sh
sudo svnadmin create /home/svn/projects
```

## 配置
* 修改仓库配置

``` sh
cd /home/svn/projects/conf/ # 进入配置文件夹
```

* svnserve.conf 配置服务器文件

``` sh
sudo nano svnserve.conf
```

``` sh
[general]
# 匿名访问的权限，可以是read,write,none,默认为read
anon-access = none
# 认证用户的权限，可以是read,write,none,默认为write
auth-access = write
# 密码数据库的路径，去掉前面的#
password-db = passwd
# 配置权限文件
authz-db = authz
# 仓库名 - 根据自己情况修改
realm = projects
```
**注意**：所有的行都必须顶格，否则报错。 

* authz 配置权限

``` sh
sudo nano authz
```

``` sh
[groups] 
# harry_and_sally = harry,sally
# harry_sally_and_joe = harry,sally,&joe
admin = tmyam # admin是用户组，tmyam是用户
test = one，two

[projects:/]
@admin = rw  # admin用户组的所有用户，对仓库有读写权限
@test = r    # test 用户组的所有用户，对仓库有只读权限

[projects:/test]
one = rw    # one用户对仓库下的test文件夹有读写权限

```

* passwd 配置密码

``` sh
sudo nano passwd
```

``` sh
[users]
# harry = harryssecret
# sally = sallyssecret
tmyam = 1234
one = 1234
two = 1234
```

## 启动服务

``` sh
killall svnserve
svnserve -d -r /home/svn
```

``` sh
# 查看启动情况
ps aux | grep svn

# 查看端口使用情况，SVN默认端口：3690
netstat -tln
```
## 设置开机自启动

* 查看svnserve位置

``` sh
whereis svnserve
```
**注意**：我们用终端操作的时候，可以直接使用svnserve命令，
但是在/etc/rc.local 文件中必须写上完整的路径！

``` sh
sudo nano /etc/rc.local
```
在`exit 0`之前添加：

>
/usr/bin/svnserve -d -r /home/svn

## 连接
你的电脑使用 `svn://你的域名/projects` 进行连接。


### 参考
<http://shumeipai.nxez.com/2013/08/27/building-svn-come-in-raspberry.html>   
<http://www.cnblogs.com/LusYoHo/p/6056377.html>   
<http://blog.csdn.net/testcs_dn/article/details/45395645>


