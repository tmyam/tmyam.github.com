---
layout: post
title: "apt-get小记"
date: 2017-03-27 15:20:08 +0800
comments: true
disqus: false
categories: Linux 树莓派
---

### 前言
&emsp;&emsp;入门Linux的同志，刚开始最迫切想知道的，大概一个是中文输入法，另一个就是怎么安装软件。    
  
&emsp;&emsp;在Windows下安装软件，我们只需要有exe文件，然后双击，下一步直接OK就可以了。但在Linux下，不是这样的。每个Linux的发行版，比如Debian，都会维护一个自己的软件仓库，我们常用的几乎所有软件都在这里面。这里面的软件绝对安全，而且绝对的能正常安装。   
      
&emsp;&emsp;那我们要怎么安装呢？在Debian下，我们维护一个源列表，源列表里面都是一些网址信息，这每一条网址就是一个源，这个地址指向的数据标识着这台源服务器上有哪些软件可以安装使用。
<!-- more -->
### 更新软件源

``` sh
sudo apt-get update
```
这个命令，会同步源服务器的源列表（APT服务器的RPM索引清单置于base资料夹内，使用者端电脑取得base资料夹内的bz2 RPM索引清单压缩档后，会将其解压置放于/var/state/apt/lists/）。
   
使用者使用apt-get install 或apt-get dist-upgrade 指令的时候，就会将这个资料夹内的资料和使用者端电脑内的RPM资料库比对，如此一来就可以知道那些RPM已安装、未安装、或是可以升级的。


### 更新软件(upgrade)

``` sh
sudo apt-get upgrade
```
这个命令，会把本地已安装的软件，与刚下载的软件源里对应软件的版本进行对比，如果发现已安装的软件版本太低，就会提示你更新。

### 更新软件(dist-upgrade)
``` sh
sudo apt-get dist-upgrade
```
如果这个包没有发布更新，就不管它；如果发布了更新，就把包下载到电脑上，并安装。

### upgrade与dist-upgrade的关系   
由于包与包之间存在各种依赖关系。upgrade只是简单的更新包，不管这些依赖，它不添加包，或是删除包。而dist-upgrade可以根据依赖关系的变化，添加包，删除包。

* upgrade:系统将现有的Package升级,如果有相依性的问题,而此相依性需要安装其它新的Package或影响到其它Package的相依性时,此Package就不会被升级,会保留下来。

* dist-upgrade:可以聪明的解决相依性的问题,如果有相依性问题,需要安装/移除新的Package,就会试着去安装/移除它. (所以通常这个会被认为是有点风险的升级) 。

例如软件包 a 原先依赖 b c d，但是在源里面可能已经升级了，现在是 a 依赖 b c e。这种情况下，dist-upgrade 会删除 d 安装 e，并把 a 软件包升级，而 upgrade 会认为依赖关系改变而拒绝升级 a 软件包。

### 常用命令
* 安装软件

``` sh
sudo apt-get install package1 package2 package3……
```

* 卸载软件

``` sh
sudo apt-get remove package1 package2 package3……
```

* 卸载并清除配置

``` sh
sudo apt-get remove --purge package1
```

* 删除包及其依赖的软件包+配置文件等

``` sh
sudo apt-get autoremove --purge package1
```

* 搜索软件包

``` sh
sudo apt-cache search package1 package2 package3……
```

* 使用 dselect 升级

``` sh
sudo apt-get dselect-upgrade 
```

* 了解使用依赖

``` sh
sudo apt-cache depends package1
```

* 查看该包被哪些包依赖

``` sh
sudo apt-cache rdepends package1
```

* 下载该包的源代码

``` sh
sudo apt-get source package1
```

* 清理下载文件的存档

``` sh
sudo apt-get clean 
```

* 清理过时的包

``` sh
sudo apt-get autoclean
```

* 检查是否有损坏的依赖

``` sh
sudo apt-get check
```
### 更换软件源
如果使用 apt-get 遇到速度慢或者源不存在等错误，可能需要更换源，[参考](http://shumeipai.nxez.com/2013/08/31/raspbian-chinese-software-source.html)。

### 树莓派命令
* 树莓派固件   
可以理解为树莓派的BIOS，引导程序之类的，这一部分博通不开源，给树莓派增加新功能主要通过固件来实现。

* 安装rpi-update

``` sh
sudo curl -L --output /usr/bin/rpi-update https://raw.githubusercontent.com/Hexxeh/rpi-update/master/rpi-update\
 && sudo chmod +x /usr/bin/rpi-update
```
* 升级firmware

``` sh
sudo rpi-update
```

### 参考
<http://www.baiyuxiong.com/?p=529>   
<http://blog.csdn.net/tiankongtiankong01/article/details/53869975>   
<http://blog.csdn.net/huayucong/article/details/54178049>   
<https://item.congci.com/-/content/shumeipai-raspbian-gujian-shengji-rpi-update>


