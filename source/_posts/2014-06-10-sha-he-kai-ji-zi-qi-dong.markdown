---
layout: post
title: "mac app的开机自启动"
date: 2014-06-10 14:15
comments: true
disqus: false
categories: Mac-develop
---
### 前言
在出现沙盒以前，mac下设置开机自启动是很容易的，使用 LSSharedFileListRef 很容易做到开机自启动。但自从沙盒出现之后，就变得麻烦了点，这里进行一下详细的使用描述。

### 非沙盒下的开机自启动
简单的说只需要两个函数就可以了。而且这种开机自启动和app所在的文件夹无关，并且可以在"系统偏好设置"->"用户与群组"->"登录项"里面看到，并进行设置。
<!-- more -->  

``` objc
+ (BOOL) isStartAtLogin
{
    BOOL  isHaveAdd = NO;
    NSString* appPath = [[NSBundle mainBundle] bundlePath];
    LSSharedFileListRef loginItems = LSSharedFileListCreate(NULL, kLSSharedFileListSessionLoginItems, NULL);
    UInt32 seedValue = 0;
    NSArray* loginItemsArray = (NSArray*)LSSharedFileListCopySnapshot(loginItems, &seedValue);
    CFURLRef tempUrl = (CFURLRef)[NSURL fileURLWithPath:appPath];
    for(NSInteger i = 0 ; i< [loginItemsArray count]; i ++ )
    {
        LSSharedFileListItemRef itemRef = (LSSharedFileListItemRef)[loginItemsArray objectAtIndex:i];
        if (LSSharedFileListItemResolve(itemRef, 0,&tempUrl, NULL) == noErr)
        {
            NSString * urlPath = [(NSURL*)tempUrl path];
            if ([urlPath compare:appPath] == NSOrderedSame)
            {
                isHaveAdd = YES;
                break;
            }
        }
    }
    [loginItemsArray release];
    CFRelease(loginItems);
    return isHaveAdd;
}

+ (BOOL) setStartAtLogin:(BOOL)startAtLogin
{
    NSString* appPath = [[NSBundle mainBundle] bundlePath];
    BOOL result = NO;
    if (startAtLogin)
    {
        if (![self isStartAtLogin])
        {
            CFURLRef url = (CFURLRef)[NSURL fileURLWithPath:appPath];
            LSSharedFileListRef newloginItems = LSSharedFileListCreate(NULL, kLSSharedFileListSessionLoginItems, NULL);
            LSSharedFileListItemRef item = LSSharedFileListInsertItemURL(newloginItems, kLSSharedFileListItemLast, NULL, NULL, url, NULL, NULL); 
            if (item)
            {
                result = YES;
                CFRelease(item);
            }
            if (newloginItems)
            {
                CFRelease(newloginItems);
            }
        }
    }
    else
    {
        LSSharedFileListRef loginItems = LSSharedFileListCreate(NULL,kLSSharedFileListSessionLoginItems, NULL);
        UInt32 seedValue = 0;
        NSArray* loginItemsArray = (NSArray*)LSSharedFileListCopySnapshot(loginItems, &seedValue);
        CFURLRef tempUrl = (CFURLRef)[NSURL fileURLWithPath:appPath];
        for(NSInteger i = 0 ; i < [loginItemsArray count]; i ++ )
        {
            LSSharedFileListItemRef itemRef = (LSSharedFileListItemRef)[loginItemsArray objectAtIndex:i];
            if (LSSharedFileListItemResolve(itemRef, 0,&tempUrl, NULL) == noErr)
            {
                NSString * urlPath = [(NSURL*)tempUrl path];
                if ([urlPath compare:appPath] == NSOrderedSame)
                {
                    OSStatus status = LSSharedFileListItemRemove(loginItems,itemRef);
                    result = (status == noErr);
                }
            }
        }
        [loginItemsArray release];
        CFRelease(loginItems);
    }
    return result;
}
```

### 沙盒下的开机自启动
沙盒下的自启动会比较麻烦，步骤比较多。这里以程序 test 为例，进行详细阐述。

#### 1. 添加Helper程序
沙盒下，app是无法自己做到开机自启动的，需要另外一个app协助才行。这里就创建这样一个app，为了便于区分起名testHelper。
![temp](/images/2014/06/10/01.jpg) 

#### 2. 设置Helper
test和testHelper都需要设置，这里先进行testHelper的设置.

* 修改build Configuration 为 Release 。
![temp](/images/2014/06/10/02.jpg) 
* 修改Info.plist文件，使app在运行时不显示到前端。
![temp](/images/2014/06/10/03.jpg) 
* 删除MainMenu.xib中的window窗口，    
并且删除 AppDelegate.h 中的`@property (assign) IBOutlet NSWindow *window;`。    
这些都是确保app在运行时不显示到前端。
![temp](/images/2014/06/10/04.jpg) 
* 修改 Build Settings 的 **Skip Install** 为 YES。
![temp](/images/2014/06/10/05.jpg) 
* 设置沙盒环境，证书，描述文件。    
这里设置的发布证书，如果需要测试，可以设置开发者证书进行测试。
![temp](/images/2014/06/10/06.jpg) 

#### 3. 添加Helper的代码
参照下面的函数，复制到testHelper的AppDelegate中，注意修改identifier和appName为自己主app的信息。这段代码的意义是启动主app。

``` objc
- (void)applicationDidFinishLaunching:(NSNotification *)aNotification
{
    [self runMainApp:@"com.tmyam.test" appName:@"test"];
}

/** 运行开机自启动的app

 @param identifier 主app的标识符
 @param appName 主app的名称（在MacOS文件夹下的名称）
 */
- (void) runMainApp:(NSString*)identifier appName:(NSString*)appName
{
    // Check if main app is already running; if yes, do nothing and terminate helper app
    BOOL alreadyRunning = NO;
    NSArray *running = [[NSWorkspace sharedWorkspace] runningApplications];
    for (NSRunningApplication *app in running)
    {
        if ([[app bundleIdentifier] isEqualToString:identifier])
        {
            alreadyRunning = YES;
        }
    }
    if (!alreadyRunning)
    {
        NSString *path = [[NSBundle mainBundle] bundlePath];
        NSArray *p = [path pathComponents];
        NSMutableArray *pathComponents = [NSMutableArray arrayWithArray:p];
        [pathComponents removeLastObject];
        [pathComponents removeLastObject];
        [pathComponents removeLastObject];
        [pathComponents addObject:@"MacOS"];
        [pathComponents addObject:appName];
        NSString *newPath = [NSString pathWithComponents:pathComponents];
        [[NSWorkspace sharedWorkspace] launchApplication:newPath];
    }
    [NSApp terminate:nil];
}
```

#### 4. 设置主程序

* test添加Copy Files 。
![temp](/images/2014/06/10/07.jpg) 
* 设置 Copy Files 。Destination为"Wrapper"，Subpath为"Contents/Library/LoginItems"，添加"testHelper.app"如下图。   
![temp](/images/2014/06/10/08.jpg)
* 设置依赖，如下图。  
![temp](/images/2014/06/10/09.jpg) 
* 设置 Build Settings -> "Strip Debug Symbols During Copy" 为 NO 。
![temp](/images/2014/06/10/10.jpg) 
* 设置沙盒环境，证书，描述文件。    
这里设置的发布证书，如果需要测试，可以设置开发者证书进行测试。
![temp](/images/2014/06/10/11.jpg) 

#### 5. 主程序添加代码
* test首先导入两个文件，StartAtLoginController.h和StartAtLoginController.m，[StartAtLoginController下载](/files/StartAtLoginController.zip)

* 添加 ServiceManagement.framework
![temp](/images/2014/06/10/12.jpg) 

* 创建一个管理文件例如 TMStartAtLogin，负责管理开机自启动状态，并在里面粘贴以下代码。     
别忘了 #import "StartAtLoginController.h"    
这里的 Identifier是testHelper的标识符。

``` objc
+ (BOOL) isStartAtLogin
{
    StartAtLoginController* loginController = [[StartAtLoginController alloc]initWithIdentifier:@"com.tmyam.testHelper"];
    BOOL startedAtLogin = [loginController startAtLogin];
    [loginController release];
    return startedAtLogin;
}

+ (BOOL) setStartAtLogin:(BOOL)isStartLogin
{
    StartAtLoginController* loginController = [[StartAtLoginController alloc]initWithIdentifier:@"com.tmyam.testHelper"];
    loginController.startAtLogin = isStartLogin;
    BOOL result = loginController.enabled;
    [loginController release];
    return result;
}
```
#### 结尾
至此，所有的设置已经完成，可以通过主程序的 TMStartAtLogin 管理自启动的状态。
还有要注意的地方，如果要测试沙盒下的开机自启动，*需要将编译出的app放入系统的应用程序目录下*，否则不会生效。   

另外，在发布app时，会遇到因为使用了testHelper证书而导致上传到app store时的错误，这时需要深入到*".../Products/Applications/test.app/Contents/Library/LoginItems/testHelper.app/Contents"*目录下，删除embedded.provisionprofile文件，就可以正确上传了。

### 示例
[StartAtLoginController下载](/files/StartAtLoginController.zip)   
[test示例下载](/files/test-startatlogin.zip)

### 参考
<http://blog.timschroeder.net/2012/07/03/the-launch-at-login-sandbox-project/>
<http://stackoverflow.com/questions/11230168/submit-an-osx-app-with-helper-app-in-its-bundle>






