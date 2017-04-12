---
layout: post
title: 通知的使用
category: iOS开发笔记（基础）
tags: 通知
keywords: NSNotification
description: 通知的使用
---

```objective-c
//创建通知
    [[NSNotificationCenter defaultCenter] postNotificationName:kMusicPlaybackStateChangeNotification object:musicIdentifier];

//监听通知，其中notificationMethod为通知的执行方法
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(notificationMethod:) name:kMusicPlaybackStateChangeNotification object:nil];

//销毁通知
- (void)dealloc {
    [[NSNotificationCenter defaultCenter] removeObserver:self];
}
```
