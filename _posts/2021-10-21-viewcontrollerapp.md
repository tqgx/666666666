---
layout: post
title: "ViewController和APP的生命周期"
subtitle: "ViewController and APP Lifecycle"
date: 2021-10-21
author: "NKQ"
header-img: "img/home-bg-art.jpg"
tags:
 - ViewController
 - AppDelegate
 - iOS
---

## APP生命周期

APP是运行与系统之中的，因此系统会对APP产生影响，比方说让APP从前台退出，用户暂时去使用其他APP，这时APP会进入后台运行状态，至于是否挂起，还要看具体的实现情况，事实上APP的生命周期包含了以下几个状态

![img](./app-life-cycle.png)

### 状态

#### not running

app还未被启动或者被系统或用户终止了的状态

#### inactive

app正在启动时处于的状态，但是不接收操作事件的通知，这意味着用户无法点击，但可能会运行开发者的其他代码

#### active

app正在运行，接收用户的操作并进行响应

#### background

用户切换app或者回到桌面时，app会进入后台状态，这时候可能会运行一些代码，如果没得运行，就会进入挂起状态（suspended）

在后台运行时，app仍然可以进行一些工作，比如下载，定位，更新视图等，比方说现在的一些与钱相关的app，切换app时他们会自动变模糊

有的程序经过特殊的请求后可以长期处于Backgroud状态

#### suspended

app进入挂起状态，不运行任何代码，如果系统的内存不够时，就会把app干掉

### 变化

#### iOS13以前

iOS13之前的生命周期，定义在`@protocol(UIApplicationDelegate)`中，也就是AppDelegate中

```ObjC
- (void)applicationDidFinishLaunching:(UIApplication *)application;
- (BOOL)application:(UIApplication *)application willFinishLaunchingWithOptions:(nullable NSDictionary<UIApplicationLaunchOptionsKey, id> *)launchOptions API_AVAILABLE(ios(6.0));
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(nullable NSDictionary<UIApplicationLaunchOptionsKey, id> *)launchOptions API_AVAILABLE(ios(3.0));

- (void)applicationDidBecomeActive:(UIApplication *)application;
- (void)applicationWillResignActive:(UIApplication *)application;
- (void)applicationDidEnterBackground:(UIApplication *)application API_AVAILABLE(ios(4.0));
- (void)applicationWillEnterForeground:(UIApplication *)application API_AVAILABLE(ios(4.0));
- (void)applicationDidReceiveMemoryWarning:(UIApplication *)application;      // try to clean up as much memory as possible. next step is to terminate app
- (void)applicationWillTerminate:(UIApplication *)application;
```

#### iOS13之后

iOS13之后，大部分的生命周期函数在SceneDelegate中使用

```objective-c
- (void)sceneDidDisconnect:(UIScene *)scene {
    // Called as the scene is being released by the system.
    // This occurs shortly after the scene enters the background, or when its session is discarded.
    // Release any resources associated with this scene that can be re-created the next time the scene connects.
    // The scene may re-connect later, as its session was not necessarily discarded (see `application:didDiscardSceneSessions` instead).
}


- (void)sceneDidBecomeActive:(UIScene *)scene {
    // Called when the scene has moved from an inactive state to an active state.
    // Use this method to restart any tasks that were paused (or not yet started) when the scene was inactive.
}


- (void)sceneWillResignActive:(UIScene *)scene {
    // Called when the scene will move from an active state to an inactive state.
    // This may occur due to temporary interruptions (ex. an incoming phone call).
}


- (void)sceneWillEnterForeground:(UIScene *)scene {
    // Called as the scene transitions from the background to the foreground.
    // Use this method to undo the changes made on entering the background.
}


- (void)sceneDidEnterBackground:(UIScene *)scene {
    // Called as the scene transitions from the foreground to the background.
    // Use this method to save data, release shared resources, and store enough scene-specific state information
    // to restore the scene back to its current state.
}
```

## ViewController生命周期

ViewController的生命周期与APP的生命周期完全是两个概念，视图控制器是存在于APP之中，MVC中的C，用于控制开发者创建的视图，用户在APP直观看到的内容，都是由视图控制器控制，视图控制器可以有多个，至少要有一个，多个可以通过UINavigationController来管理，下一部分探讨

ViewController从创建到失效，包含了4个状态，分别是

- not loaded
- disappear
- appear
- MemoryWarning

从名字就可以看出，分别是视图未加载，视图未显示，视图正在显示，收到内存警告

对应的函数定义在UIViewController中

```objc
- (void)viewDidLoad; // Called after the view has been loaded. For view controllers created in code, this is after -loadView. For view controllers unarchived from a nib, this is after the view is set.
- (void)viewWillAppear:(BOOL)animated;    // Called when the view is about to made visible. Default does nothing
- (void)viewDidAppear:(BOOL)animated;     // Called when the view has been fully transitioned onto the screen. Default does nothing
- (void)viewWillDisappear:(BOOL)animated; // Called when the view is dismissed, covered or otherwise hidden. Default does nothing
- (void)viewDidDisappear:(BOOL)animated;  // Called after the view was dismissed, covered or otherwise hidden. Default does nothing
// Called just before the view controller's view's layoutSubviews method is invoked. Subclasses can implement as necessary. The default is a no-op.
- (void)viewWillLayoutSubviews API_AVAILABLE(ios(5.0));
// Called just after the view controller's view's layoutSubviews method is invoked. Subclasses can implement as necessary. The default is a no-op.
- (void)viewDidLayoutSubviews API_AVAILABLE(ios(5.0));
- (void)didReceiveMemoryWarning; // Called when the parent application receives a memory warning. On iOS 6.0 it will no longer clear the view by default.
```

其中viewDidLoad仅调用一次，就是视图加载时，其他的几个方法会多次调用，可以用于控制一些比较耗费系统资源的事情。比如定位，蓝牙等，让这些占用资源的功能可以关闭掉

在写viewWillAppear，viewDidAppear，viewWillDisappear，viewDidDisappear时，需要呼叫super方法

viewWillLayoutSubviews，viewDidLayoutSubviews这两个方法用于调整视图，一个是在视图显示前更改，一个是视图显示后再更改

didReceiveMemoryWarning就是收到内存警告了，程序可能会被干掉，这时候用户看到的就是闪退，但是也可以Override这个方法，释放些内存
