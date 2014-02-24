---
layout: post
title: "Objective-C枚举介绍"
date: 2014-02-24 15:39
comments: true
disqus: false
categories: mac_develop ios_develop
---
Objective-C枚举的使用，常见的方式总结，以便自己使用时方便查阅。
## 1. 枚举介绍
### enum only

``` objective-c
enum
{
    TMEnumTestOne,
    TMEnumTestTwo,
};

```
* 这种枚举**不推荐**使用，因为不能用一种格式的方式表示枚举。
<!-- more -->

### enum 类型
``` objective-c
typedef enum
{
    TMEnumTestOne,
    TMEnumTestTwo,
    
} TMEnumTest;

```
``` objective-c
enum TMEnumTest
{
    TMEnumTestOne,
    TMEnumTestTwo,
};
typedef enum TMEnumTest TMEnumTest;

```
* 这两种写法，将枚举描述为一个类型*TMEnumTest*。   
但是这种写法也有局限性，在作为函数参数时，如果传入NSInteger类型的数据，则需要强制转换，否则会有类型不匹配的警告，所以这种方式也**不推荐**。如下图，
![temp](/images/2014/02/24/enum_warn.png) 

### 老式enum写法
``` objective-c
enum
{
    TMEnumTestOne,
    TMEnumTestTwo,
};
typedef NSUInteger TMEnumTest;
```
* 这种写法可以指定*TMEnumTest*的类型，并且作为函数参数时，传入的值不需要进行强制类型转换。   
但是这种写法不会检查枚举的正确性，是好是坏需要自己判断，如下图。本人**推荐**这种写法。
![temp](/images/2014/02/24/enum_no_check.png) 

### 新式enum写法
``` objective-c
typedef NS_ENUM(NSUInteger, TMEnumTest)
{
    TMEnumTestOne,
    TMEnumTestTwo,
};
```
``` objective-c
typedef NS_OPTIONS(NSUInteger, TMEnumTest)
{
    TMEnumTestOne  = 0,
    TMEnumTestTwo  = 1 << 0,
};
```
* **NS_ENUM**和**NS_OPTIONS**本质是一样的，仅仅从字面上来区分用途。**NS_ENUM**是通用情况，**NS_OPTIONS**一般用来定义位枚举。
[参考链接](http://blog.csdn.net/annkie/article/details/9877643)   
* 这种写法同样作为函数参数时，传入的值不需要进行强制类型转换。但是也同样不会检查枚举的正确性，和 **老式enum写法** 一样，如下图。
![temp](/images/2014/02/24/enum_no_check.png) 
* 但是不同的是，**新式enum写法** 在使用*switch*时，会进行值的检测，如果枚举中无此值，则会弹出警告，如下图。这种方式见仁见智了。本人**推荐**这种写法。
![temp](/images/2014/02/24/enum_switch.png)

## 2. 枚举最大值
对于一般的枚举，要获取枚举的最大值是很难的，因为随着枚举的扩充，最大值在不断变化，这时推荐使用一个固定的枚举表示最大值，例如：
``` objective-c
typedef NS_ENUM(NSUInteger, TMEnumTest)
{
    TMEnumTestOne,
    TMEnumTestTwo,
    
    TMEnumTestMax,  // 表示最大值
};
```
这样就可以使用*TMEnumTestMax*表示枚举*TMEnumTest*的最大值了。

## 3. 位枚举
位枚举是一种特殊的枚举，在apple的类中使用的很多，例如`NSStringCompareOptions`等。
``` objective-c
typedef NS_OPTIONS(NSUInteger, TMEnumTest)
{
    TMEnumTestOne     = 0,		   // 0
    TMEnumTestTwo     = 1 << 0,  	// 1
    TMEnumTestThree   = 1 << 1,		// 2
    TMEnumTestFour    = 1 << 2,		// 4
};
``` 

* 位枚举的特点是可以使用位运算来处理枚举值，实际使用中可以用一个变量存储多个枚举值，表示互不影响的多个设置。
``` objective-c
- (void) test
{
    TMEnumTest test = TMEnumTestTwo|TMEnumTestThree;  // 3
    
    /* 添加TMEnumTestFour到test中（如test已经包含TMEnumTestFour，则test值不变）
    
       当然这里也可以使用test += TMEnumTestFour,
       但是注意 + 不能在test中已经包含TMEnumTestFour的情况下使用。
    */
    test |= TMEnumTestFour;  // 7
    
    
    /* 将TMEnumTestThree从test中去除（如test不包含TMEnumTestThree，则test值不变）
    
       当然这里也可以使用test -= TMEnumTestThree，
       但是注意 - 不能在test中不包含TMEnumTestThree的情况下使用。
    */    
    test &= ~TMEnumTestThree; // 5
    
    
    // 判断 TMEnumTestFour枚举 是否被包含
    if ((test & TMEnumTestFour) == TMEnumTestFour)
    {
        NSLog(@"YES");
    }
    
    
    /* 判断 TMEnumTestFour枚举 是否被包含
     
     因为 (test & TMEnumTestFour) 的结果不是 0，就是TMEnumTestFour本身，
     而 TMEnumTestFour > 0 ，所以可以使用简易的判断
     */
    if (test & TMEnumTestFour)
    {
        NSLog(@"YES");
    }
}
``` 





