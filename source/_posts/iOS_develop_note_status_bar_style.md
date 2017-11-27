---
layout:     post
title:      最低支持iOS9系统对应状态栏、导航栏UI设置
date:       2017-11-27 15:51:43
author:     liangtong
catalog: true
categories: iOS
tags: Note

---



​        iOS9之后，APP在修改状态栏前端等颜色时，弃用了部分接口。比如：

```Objective-C
// Setting the statusBarStyle does nothing if your application is using the default UIViewController-based status bar system.
@property(readwrite, nonatomic) UIStatusBarStyle statusBarStyle NS_DEPRECATED_IOS(2_0, 9_0, "Use -[UIViewController preferredStatusBarStyle]") __TVOS_PROHIBITED;
```

本文介绍最低支持iOS9系统对应的状态栏前段UI设置。

### 状态栏字体颜色

#### 程序启动Launch画面

对应状态栏字体颜色，可以通过在plist文件中添加属性 **UIStatusBarStyle** （或者 **Status bar style**）来设置字体颜色为黑色 **UIStatusBarStyleDefault** ，或者白色 **UIStatusBarStyleLightContent**

#### 功能页面

程序启动后，可以通过重写 **UIViewController** 中的方法 **preferredStatusBarStyle** 来实现。例如在程序的Base NavigationController中：

```Objective-C
- (UIStatusBarStyle)preferredStatusBarStyle{
    return UIStatusBarStyleLightContent;
}
```

### 导航栏字体颜色

可以在程序启动时，通过以下代码设置。例如以下代码将导航栏字体颜色设置为白色：

```Objective-C
    //NavigationBar 字体颜色
    [[UINavigationBar appearance] setTitleTextAttributes:[NSDictionary dictionaryWithObject:[UIColor whiteColor] forKey:NSForegroundColorAttributeName]];
```

### 背景颜色

在程序启动时，可以通过代码进行设置

```Objective-C
    UIColor* statusBarBackgroundColor;//状态栏颜色
    [[UINavigationBar appearance] setBarTintColor:statusBarBackgroundColor];
```


