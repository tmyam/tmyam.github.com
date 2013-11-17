---
layout: post
title: "mac app完整的发布流程"
date: 2013-11-17 10:45
comments: true
disqus: false
categories: mac_develop
---
发布mac app网上已经很多教程了，这里只是进行一个记录，有的时候容易忘记细节。

## 前提条件
1, 一个mac developer帐号

## 创建APP ID

* 登陆 [MemberCenter](https://developer.apple.com/membercenter)
* 点击 **Certificates, Identifiers & Profiles** 进入管理界面，点击*Identifiers* -> *App IDs*.

通常情况下我们会创建如 `com.company.*` 这样的通用型APP ID，可以适用于大部分程序,但是对于需要推送功能，以及app内含有其他app的程序，这样的ID就不行了。保险起见还是创建单独的APP ID。

* 点击`+` 按钮，进入创建页面。   
  *App ID Description* 就是一个名称，随意输入就行，不做他用。   
  *App Services* 应用将具有的能力，可以根据需要勾选。   
  *App ID Prefix* 标示前缀，不用管。   
  *App ID Suffix* 标示后缀，这里有两种选择 **Explicit App ID**这种就是一对一的ID，而**Wildcard App ID** 这种就是上面提到的一对多的ID。   
  这里创建 **Explicit App ID** ，*Bundle ID:* 为类似 `com.company.xxx`这种样式，`company`为公司名，`xxx`为应用程序名，注意大小写一致。   
 一路继续，创建成功。
 
## 在iTunes Connect添加App
这一步需要在开始开发之前做，因为App Name可能已经被别的人占用了，一旦做完应用才创建，不得不面临改名的困境，所以在开放前先将应用名占着。   

[参考链接](http://my.oschina.net/joanfen/blog/133642)   

* 通过[MemberCenter](https://developer.apple.com/membercenter) 点击进入**iTunes Connect**，这样可以使创建的ID快速生效。   
* 点击 **Manage Your Apps**,然后点击`Add New App`按钮。

然后是填写**App Information**  
 
  * 其中*SKU Number* 是区分你自己应用的标示，只要不重复就行，这里填写和App Name一样就行了。   
  * *Bundle ID* 选择之前创建的ID，然后继续。
  
之后是选择价格界面，选择之后点击继续。   

 最后是应用详细信息。 
    
* **Version Information**   
   *Version Number*  版本号   
   *Copyright*  版权，填写`2013 company Inc` 就可以了。   
* **Category**（app种类）   
  选择app所属的两种类型。
* **Rating**（分级）   
  一般根据情况选择，没有就全选*None*。
* **Metadata**（软件信息）   
  根据需要填写，也可以之后再更改。
* **Contact Information**   
  根据实际情况填写。
* **Uploads**（截图）   
  可以先选张上去，之后再更改。

至此，开放前的准备工作就完成了。
  
  
