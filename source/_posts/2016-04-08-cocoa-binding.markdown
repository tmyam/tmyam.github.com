---
layout: post
title: "cocoa binding"
date: 2016-04-08 10:17
comments: true
disqus: false
categories: mac_develop
---
## 前言
cocoa binding一项可以让开发变得更加便捷的技能，网上的资料非常有限，所以在这里进行一下记录。概念约定:    
正向操作(转换) ---> 代码控制的操作(转换)    
反向操作(转换) ---> 用户界面操作控制(转换)
         

## 简单的使用
比如，有这样一个需求，监控 **NSPopUpButton** 的选择下标的变化：    
1. 可以用代码改变选择的下标    
2. 当用户在界面上选择改变下标时，可以监测到这种改变
<!-- more -->  
####1. 一般的做法
``` objective-c 
// 在xib中使用 IBOutlet 绑定控件，这样可以满足需求1
@property (nonatomic ,strong,nonnull) IBOutlet NSPopUpButton*   popupButton;
// 需求1
self.popupButton.indexOfSelectedItem = 1;

// 使用 IBAction 绑定action，可以满足需求2
- (IBAction) popupButtonChangeIndex:(id)sender
{
	NSLog(@"%ld",[sender indexOfSelectedItem]);
}

```
这样虽然满足了需求，但是需要使用两个绑定，IBOutlet，IBAction，代码量较多。
####2. 使用bind      

``` objective-c 
写法 1 (定义属性)
// 定义选择下标属性
@property (nonatomic ,assign) NSInteger   index;
// 主要是需要写这个set函数 // 需求2
- (void) setIndex:(NSInteger)index
{
	_Index = index;
    NSLog(@"%ld",index);
}
//需求1
self.index = 1;

写法 2 (不定义属性)
// 需求2 // 不定义属性，则需要一个值来保存选择的下标，并写出get方法
- (void) setIndex:(NSInteger)index
{
	xx = index;
    NSLog(@"%ld",index);
}
- (NSInteger) index
{
    return xx;
}
//需求1
self.index = 1;

写法1的好处是简便，写法2的好处是不受限于属性的类型，比如xx可以自定义为NSNumber类型。
```
界面绑定，如下图    
![temp](/images/2016/04/08/bind_selectindex.png)    
第一个红线标注的是选择绑定的对象，第二个标注的是绑定对象的属性，即`index`。
当然也可以不使用界面绑定，使用代码绑定。
```objective-c
[self.popupButton bind:@"selectedIndex" toObject:self withKeyPath:@"index" options:nil];

- (void) dealloc
{
    [self unbind:@"selectedIndex"];
}

@"selectedIndex"可以用NSSelectedIndexBinding代替
```
## 进阶
####1. 绑定NSTextField
输入变化是经常遇到的监测对象，绑定的方式可以实现限制输入内容的功能。

例如，下面是一个输入框，限定输入0~99。
```objective-c
// 绑定的文本
@property (nonatomic ,strong) NSString*     number;
// 监听变化
- (void) setNumber:(NSString *)number
{
    if (!number.length)
    {
        number = @"0"; //默认值
    }
    NSLog(@"%@",number);
    _number = number;
}
```
在界面上绑定**NSTextField**的**value**。注意勾选红色标注        
![temp](/images/2016/04/08/bind_textfield.png)     
当然，这样仅仅只是可以检测文本的输入变化，并不能限制输入的内容。还需要创建一个Formatter继承自系统的NSFormatter，并重写里面的函数。
```objective-c
@implementation TMNumFormatter

- (NSString*) stringForObjectValue:(id)object
{
    return(NSString*)object;
}

- (BOOL) getObjectValue:(out id*)object 
              forString:(NSString*)string 
       errorDescription:(NSString**)error
{
    *object = [NSString stringWithString:string];//必须这样写，否则bind会失效
    return YES;
}

- (BOOL)isPartialStringValid:(NSString*)partialString 
            newEditingString:(NSString**)newString 
            errorDescription:(NSString**)error
{
    if (partialString.length)
    {
        if (![self isInteger:partialString]) // 判断是否为纯数字
        {
            return NO;
        }
        else if (partialString.length > 2)   // 限制长度，最多两位
        {
            return NO;
        }
        else
        {
            for (NSInteger i = 0; i < partialString.length ; i ++ )
            {
                unichar c = [partialString characterAtIndex:i];
                if (c >= '0' && c <= '9')
                {
                    continue;
                }
                else
                {
                    return NO;
                }
            }
            
            // 限制大小
            NSInteger num = [partialString integerValue];
            if (num < 0 || num > 99)
            {
                return NO;
            }
            return YES;
        }
    }
    else
    {
        return YES;
    }
}

/** 判断文本是否为数字

 @param string 输入的文本

 @return 是否是纯数字
 */
- (BOOL) isInteger:(NSString *)string
{
    NSScanner* scan = [NSScanner scannerWithString:string];
    NSInteger val = 0;
    return [scan scanInteger:&val] && scan.isAtEnd;
}

@end
```
然后在xib中创建一个Formatter，并关联**Outlets**中的formatter，如下图：    
![temp](/images/2016/04/08/bind_formatter.png)

####2. Value Transformer类型变换
比如上边的例子，我想要的是一个数字类型，但是bind的默认类型却是NSString类型，这时就可以使用类型转换了。
```objective-c
@property (nonatomic ,strong) NSNumber*     inter;
- (void) setInter:(NSNumber *)inter
{
    _inter = inter;
    NSLog(@"%@",inter);
}
```
需要创建一个继承自**NSValueTransformer**的类，重写里面的函数。
```objective-c
// 正向：NSNumber --> NSString
// 反向：NSString --> NSNumber
@implementation TMValueTransformer

// 正向操作时，输出的类型（控件接收的类型）
+ (Class) transformedValueClass
{
    return [NSString class];
}

// 是否允许反向操作，只有允许反向，reverseTransformedValue:函数才有用
// 如果为NO，则用户无法在界面上输入内容
+ (BOOL) allowsReverseTransformation
{
    return YES;
}

// 正向输出，NSNumber --> NSString
- (nullable id)transformedValue:(id)value
{
    return [value stringValue];
}

// 反向输出，NSString --> NSNumber
- (nullable id)reverseTransformedValue:(nullable id)value
{
    return @([value integerValue]);
}
@end
```
xib里面的绑定设置，如下图：    
![temp](/images/2016/04/08/bind_valuetransformer.png)

## 注意
####1. Read-Only 的绑定
有些bind类型，是Read-Only的，这时有些需求就无法满足了，需要注意。    
比如**NSImageView**的**valuePath**是Read-Only的，只满足正向的操作，即可以通过代码修改路径，显示不同的图片，但是界面操作拖入一张图片的时候，绑定的属性不能获取到这张图片的路径。    
所以对于Read-Only属性，只适合正向操作，即禁止用户进行修改的选项。    
对于哪些bind属性是Read-Only的，可以查询[参考链接](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/CocoaBindingsRef/CocoaBindingsRef.html)   

<br/>
### 参考
<https://developer.apple.com/library/mac/documentation/Cocoa/Reference/CocoaBindingsRef/CocoaBindingsRef.html>
<https://developer.apple.com/library/mac/documentation/Cocoa/Reference/CocoaBindingsRef/Concepts/BindingTypes.html>




