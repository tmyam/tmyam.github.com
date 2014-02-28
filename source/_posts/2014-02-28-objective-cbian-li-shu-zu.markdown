---
layout: post
title: "Objective-C遍历数组"
date: 2014-02-28 11:46
comments: true
disqus: false
categories: mac_develop ios_develop
---
数组的遍历有多种方式，这里介绍一下常见的方式，使用以及注意事项。


## 常用写法简介
### 1. 经典写法
``` objective-c
- (void) test:(NSArray*)array
{
    NSInteger count = array.count;
    for (NSInteger i = 0 ; i < count ; i ++ )
    {
        NSLog(@"%@",array[i]);
    }
    NSLog(@"end");
}
```
之所以使用count来存储数组总数，而不是写成 **i < array.count** ,是因为循环每进行一次都会调用数组的计数方法，这将导致性能变得很差。
<!-- more -->

### 2. NSEnumerator
``` objective-c
- (void) test:(NSArray*)array
{
    id test = nil;
    NSEnumerator* enumerator = [array objectEnumerator];
    while (test = [enumerator nextObject])
    {
        NSLog(@"%@",test);
    }
    NSLog(@"end");
}
```
NSEnumerator的优势在于不需要知道数据总数，所以它适合应用在结构化数据上，比如链表，数据流等。

### 3. 快速枚举
``` objective-c
- (void) test:(NSArray*)array
{
    for (NSString* test in array )
    {
        NSLog(@"%@",test);
    }
    NSLog(@"end");
}
```
快速枚举是apple推荐的方式，在使用上比经典的写法性能上更快。   
使用上如果需要反向枚举，只需要将**array**改成**array.reverseObjectEnumerator**就可以了。

### 4. 枚举块
``` objective-c
- (void) test:(NSArray*)array
{
    [array enumerateObjectsUsingBlock:^(NSString* obj, NSUInteger idx, BOOL *stop) {
        
         NSLog(@"%@",obj);
    }];
    NSLog(@"end");
}
```
枚举块的优势在于它会返回对象和索引, 而其他的枚举方法只会返回对象.

## 并发执行
现在是多核的时代，为了发挥多核的优势，在有些时候并发执行就非常具有优势。如果在枚举中有许多事要做，但并不关心枚举顺序的情况下，使用并发就很合适。   
但是对于数组总量小一些的数组，并发执行不一定会带来性能提升，反而开销可能远多于其带来的好处。  
所以具体的使用需要自己进行权衡。

### 1. 基本的并发枚举块
``` objective-c
- (void) test:(NSArray*)array
{
    [array enumerateObjectsWithOptions:NSEnumerationConcurrent usingBlock:^(NSString* obj, NSUInteger idx, BOOL *stop) {
        
        NSLog(@"%@",obj);
        
    }];
    NSLog(@"end");
}
```
这种方式会为每一个枚举创建一个线程，等到所有的枚举都执行完毕才会进行下一步。

### 2. 异步GCD并发
``` objective-c
- (void) test:(NSArray*)array
{
    dispatch_queue_t queue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);
    for(NSString* test in array)
    {
        dispatch_async(queue, ^{
            
            NSLog(@"%@",test);
        });
    }
    NSLog(@"end");
}
```
这种方式会为每一个枚举创建一个线程，但是不会等待所有枚举执行完成，而是for循环完成后就会进行下一步了，是一种异步执行的方式。

### 3. 等待GCD并发 
``` objective-c
- (void) test:(NSArray*)array
{
    dispatch_queue_t queue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);
    dispatch_group_t group = dispatch_group_create();
    for(NSString* test in array)
    {
        dispatch_group_async(group, queue, ^{
            
            NSLog(@"%@",test);
        });
    }
    dispatch_group_wait(group, DISPATCH_TIME_FOREVER);
    dispatch_release(group);
    NSLog(@"end");
}
```
这种方式使用group将多个block组成一组以监测这些Block全部完成或者等待全部完成。这种方式的效果类似于`1.基本的并发枚举块`。

### 4. dispatch_apply
``` objective-c
- (void) test:(NSArray*)array
{
    dispatch_queue_t queue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);
    dispatch_apply(array.count, queue, ^(size_t index){
        
        NSLog(@"%@",[array objectAtIndex:index]);
        
    });
    NSLog(@"end");
}
```
这是系统提供的一种并发执行方式，这种方式的效果同样类似于`1.基本的并发枚举块`。

<br/>
### 参考
本文只是粗略的介绍，如果想了解更详细的使用，建议阅读下面的链接。
<http://www.oschina.net/translate/high-performance-collection-looping-objective-c>

<http://www.dreamingwish.com/dream-2012/gcd介绍（二）-多核心的性能.html>

