---
layout: post
title: "mac app开发中添加第三方framework"
date: 2013-11-05 15:23
comments: true
disqus: false
categories: mac_develop
---
环境: `(mac 版本 10.9 ) ( Xcode 5.0 )`  
***
<br/>
####步骤很简单，但是总是忘记，所以在这里做个记录。

* 在工程目录中建个文件夹，存放将要添加的第三方framework
<!-- more -->

* 在工程的 *Build Phases* 栏点击菜单，如下图，添加 *Copy Files*  
![temp](/images/2013/11/05/add_copy_files.png)  

* 选择`Frameworks`,点击`+`号，然后找到存放第三方framework的文件夹，选择要添加的framework，如下图  
![temp](/images/2013/11/05/add_framework.png) 

* 展开*Link Binary With Libraries* 点击`+`号，同上一步一样，找到找到存放第三方framework的文件夹，选择要使用的framework，如下图  
![temp](/images/2013/11/05/use_framework.png)

右侧的文件管理里，刚添加的framework会位于顶端，可以稍作整理，移动到Frameworks组里，这时添加就完成了可以随意使用了。

#### 修改关联路径
一般情况下，如果将framework文件放到了工程文件夹的里面，那么在移动工程文件夹后，编译工程会出现无法找到framework头文件的错误，这是因为工程默认添加的路径使用了绝对路径的关系。 
  
* 修改   
在Xcode的 *Build Settings* 里找到 *Framework Search Paths* 栏，修改参数为   
$(SRCROOT)/`当前工程名`/`第三方framework所在的文件夹`   
例如：
![temp](/images/2013/11/05/change_path_framework.png)
这样就算移动工程文件夹，也不会报错了。



#### 清除冗余文件
对于已经添加的第三方framework，在生成的app中可以查看到framework的头文件，出于两个目的的考虑。  
1. 由于程序中已经引入了这些头文件，所以重复添加浪费了空间。  
2. 出于安全性的考虑，防止被别人使用或者破解。  

**清除方法：**

* 在工程的 *Build Phases* 栏点击菜单，入最上面的图里，这次添加 *Run Script*  
![temp](/images/2013/11/05/add_copy_files.png)

* 贴入如下代码

``` sh
echo "build path ${TARGET_BUILD_DIR}"  
cd ${TARGET_BUILD_DIR}/${FULL_PRODUCT_NAME}/Contents/Frameworks   
rm -rf */Headers   
rm -rf */Versions/*/Headers 
rm -rf */Versions/*/Resources/*/Contents/Headers
rm -rf */PrivateHeaders
rm -rf */Versions/*/PrivateHeaders
rm -rf */Versions/*/Resources/*/Contents/PrivateHeaders
```  
如下图   
![temp](/images/2013/11/05/clean_framework.png)

####编译问题  
对于编译出现签名错误的问题，可以查看[链接](http://tmyam.github.io/blog/2013/11/05/xcode5-mac-app-running-on-lion/)。  
>/Users/…/xxx.app: code object is not signed at all In subcomponent: /Users/…/xxx.app/Contents/Frameworks/xxx.framework Command /usr/bin/codesign failed with exit code 1

***
<br/>
**另外，由于framework中含有文件夹的快捷方式，在使用svn等版本管理软件时，新checkout的framework中的快捷方式会丢失，虽然编译运行没有问题，但是提交到mac appstore时无法通过，所以需要注意做压缩备份。**