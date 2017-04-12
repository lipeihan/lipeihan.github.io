---
layout: post
title: 单例的定义
category: iOS开发基础
tags: 基础
keywords: iOS Singleton
description: 单例的定义
---

***单例模式是一种常用的软件设计模式。在它的核心结构中只包含一个被称为单例的特殊类。通过单例模式可以保证系统中一个类只有一个实例。***



***以下是在oc中单例的定义方式：***

```objective-c
//在.h文件中定义
+(instancetype) alloc __attribute__((unavailable("alloc not available, call sharedInstance instead")));
-(instancetype) init __attribute__((unavailable("init not available, call sharedInstance instead")));
+(instancetype) new __attribute__((unavailable("new not available, call sharedInstance instead")));
```

```objective-c
//在.m 文件中定义：
+ (instancetype)sharedInstance {
   
    static id shared = nil;
    static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{
        shared = [[super alloc] initUniqueInstance];
    });
   
    return shared;
}

- (instancetype)initUniqueInstance {
   
    self = [super init];
    if (self) {
       
        [self customInit];
    }
    return self;
}

- (void)customInit {
   //TODO:init actions  
}
```

