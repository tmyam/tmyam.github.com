---
layout: post
title: "Mavericks(10.9) 下 Xcode 5 创建的mac app Lion(10.7)无法运行的问题"
date: 2013-11-05 10:05
comments: true
categories: mac_development
---
## 前因
最近mac操作系统升级到了Mavericks(10.9)，编程工具Xcode也升级到了5.0，但是升级之后问题多多。
先是以前创建的mac app工程使用到第三方framework的地方，无法编译通过了，其次就是编译出的程序在Lion(10.7)系统下无法运行了，要做兼容的话，真是让人头疼。
<!-- more -->

## 解决
### 引用第三方framework
原本mac app开发使用的第三方framework是不需要签名的，但是系统升级到Mavericks，Xcode升级到5.0后，再引用无签名的第三方framework时就无法编译通过了，这对于使用资源是很不利的。

>/Users/.../xxx.app: code object is not signed at all 
In subcomponent: /Users/.../xxx.app/Contents/Frameworks/xxx.framework 
Command /usr/bin/codesign failed with exit code 1

解决方案：  

* 方案1: 这个问题只出现在Mavericks + Xcode5.0下面，可能是一个系统bug，所以可以选择降系统。（比较麻烦）

* 方案2: 可以找到第三方framework的开源文件，自己使用签名进行编译。（不够通用）

* 方案3: 在工程的 `Other Code Signing Flags` 栏加入 `--deep` 给第三方framework签名。[参考链接](http://support.hockeyapp.net/discussions/problems/14709-code-sign-error-in-xcode-501-for-os-x-target)


### Lion(10.7)的兼容
Mavericks(10.9) + Xcode5.0创建的mac app程序无法在Lion(10.7)上面运行，就算选择了tag为10.7也不行。  
究其原因发现，新创建的程序下面多了个 *Base.lproj* 文件夹，*MainMenu.xib* 文件就在里面，而在以前，*MainMenu.xib* 文件是在 *en.lproj* 文件夹下的。  

而且系统也有相应的提示
>warning: Internationalization of /Users/.../Base.lproj/MainMenu.xib is not available when compiling for targets before Mac OS X 10.8

* 修改很简单，点击*MainMenu.xib*，找到如下图的位置，点击 `Base`  
![temp](/images/2013/11/05/xib_change.png)  
之后弹出对话框，如下图，点击 `Move` 
![temp](/images/2013/11/05/xib_move.png)   
工程中的`Base.lproj`文件夹已经没有用了，可以选择删除。

这样编译出的app就可以兼容Lion(10.7)了。
