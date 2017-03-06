---
layout: post
title: "Mac app，创建基于文档的app"
date: 2014-02-20 14:24
comments: true
disqus: false
categories: mac_develop
---

## 简介
基于文档的app在mac下又是比较常见的形式，但是介绍的文章比较少。为了避免少走弯路，在此进行一些简单的介绍。
<!-- more -->   
## 创建
创建一个mac app，在下图这个界面，勾选**Create Document-Based Application**   
![temp](/images/2014/02/20/create.png) 

其中继承自**NSDocument**的**Document**就是文档类，区别于**AppDelegate**，**Document**不是单例，而是对应一个个文档实例。

## 自建文档文件图标
这里省略，格式为icns...

## 关联文件类型
* 导入文档图标（上步创建的图标）
* 在工程的*Info*里进行设置，如下图。   
![temp](/images/2014/02/20/info.png)   
**Document Types**设置文档关联的文件，*Identifier*为文档标示。   
**Exported UTIs**为具体的文档设置，*Identifier*必须和**Document Types**的一致，这里面可以设置图标，扩展名等等。   
注意图标必须设置在**Document Types**里面，运行之后就会立即生效。   
而且这两项都是可以设置多个关联的，关联多个文档类，关联多种格式。

## 运行
这时，运行工程，文件关联就已经完成了。可以创建一个指定的扩展名文件，看看是不是已经变成了指定的图标样式。如果没有改变，可以重启电脑之后看看。

## 编码
在**Document.m**文件里已经有两个函数。

``` objc
- (NSData *)dataOfType:(NSString *)typeName error:(NSError **)outError
{
    if ([typeName isEqualToString:@"com.tmyam.TestDocument.document"])
    {
        // TODO: 这里是具体的存储文件数据
        return [@"test" dataUsingEncoding:4];
    }
    else
    {
        if (outError)
        {
            *outError = [NSError errorWithDomain:@"TestDocumentErrorDomain"
                                            code:-1
                                        userInfo:@{NSLocalizedFailureReasonErrorKey:[NSString stringWithFormat:@"Unsupported data type: %@", typeName]}];
        }
    }
    return nil;
}

- (BOOL)readFromData:(NSData *)data ofType:(NSString *)typeName error:(NSError **)outError
{
    assert([typeName isEqualToString:@"com.tmyam.TestDocument.document"]);
    
    // TODO: 这里是具体的读取文件数据
 
    return YES;
}
```
## 示例
[TestDocument示例下载](/files/TestDocument.zip)

