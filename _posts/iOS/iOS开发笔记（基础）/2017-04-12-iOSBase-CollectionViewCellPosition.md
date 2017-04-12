---
layout: post
title: 获取cell 在collection view 中的位置
category: iOS开发笔记（基础）
tags: 基础
keywords: iOS CollectionView cell position
description: CollectionView cell position
---

```objective-c
//求得出点击的cell在当前View的位置
UICollectionViewLayoutAttributes *attributes = [collectionView layoutAttributesForItemAtIndexPath:indexPath];
 
CGPoint cellCenter = attributes.center;
CGPoint anchorPoint = [collectionView convertPoint:cellCenter toView:self.view];
```

