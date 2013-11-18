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
<!-- more -->
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


## 生成发布的描述文件
在编写程序的时候需要对沙箱环境进行测试，这里使用通用的Mac developer证书，以及通用的描述文件即可达到目的。但是发布的话还需要专用的描述文件。

[参考链接](http://my.oschina.net/joanfen/blog/133624)    

* 登陆 [MemberCenter](https://developer.apple.com/membercenter)

* 点击 **Certificates, Identifiers & Profiles** 进入管理界面，点击*Identifiers* -> *Provisioning Profiles* -> *Distribution*, 点击 `+` 按钮。

* 根据提示一步一步前进，*Profile Name:*可以使用 `AppNameProfile`的样式，最后点击生成。

* 下载生成的描述文件，右键用Xcode打开，然后在工程中选择 **3rd Party Mac Developer Application:...** 的证书及对应的描述文件。如下图   
![temp](/images/2013/11/18/code_sign.png)    

## 编写程序...
这里不多赘述了...

## 发布
程序写好，测试完成就可以发布了。
还记得在 **iTunes Connect** 添加的app吗，现在可以进行完善工作了。   
目前状态 **Prepare for Upload**

* 对 *Description* 和 *Screenshots* 重写填写和上传新的截图，并保存。全部改完后点击`Ready to Upload Binary`。

* *Export Compliance* 的提问选择 NO 就可以了。之后继续进入 **Waiting For Upload** 状态。

* 回到Xcode工程，选择菜单 *Product* -> *Archive*,进入 Organizer 的 Archives 界面。

* 选择`validate...`按钮验证app是否能通过验证。   
  这步是最容易发现错误的地方，错误的种类也多种多样，根据实际的错误自己修改吧。 
    
* 如果没有错误，完成之后点击`Distribute...`按钮，等待一段漫长的上传之后，回到**iTunes Connect**，状态变成了 **Upload Received**。

* 等待一段时间，机器会进行初步审核，成功后状态会变成 **Waiting For Review** ，失败的话有具体的提示。

* 之后就是等待人工审核了，这里只有等了，一般2~10天不等，升级会快一些。如果想取消发布，可以点击 **Binary Details** ，在新页面点击右上角的 `Reject This Binary`按钮。

* 漫长的等待之后，状态会变成 **In Review**，如果不顺利，那么又要从头开始上传，接着是漫长的等待...   
  如果顺利，状态之后会变成 **Processing for App Store**，最后是 **Ready for Sale**，至此发布完成。

  
