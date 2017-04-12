---
layout: post
title: 创建图形上下文的方法
category: iOS画图
tags: draw
keywords: iOS CGContext UIGraphic
description: iOS create CGContext
---

**1. 通过CGContext的方法创建**

```objective-c
/**
  *  通过图片获得和图片大小一样的画布
  *
  *  @param inImage
  *
  *  @return 返回画布对象
  */
 - (CGContextRef)createARGBBitmapContextFromImage:(CGImageRef)inImage {
     CGContextRef    context = NULL;
     CGColorSpaceRef colorSpace;
     void *          bitmapData;
     size_t            bitmapByteCount;
     size_t            bitmapBytesPerRow;
    
     // Get image width, height. We'll use the entire image.
     size_t pixelsWide = CGImageGetWidth(inImage);
     size_t pixelsHigh = CGImageGetHeight(inImage);
    
     // Declare the number of bytes per row. Each pixel in the bitmap in this
     // example is represented by 4 bytes; 8 bits each of red, green, blue, and
     // alpha.
     bitmapBytesPerRow   = (pixelsWide * 4);
     bitmapByteCount     = (bitmapBytesPerRow * pixelsHigh);
    
     // Use the generic RGB color space.
     colorSpace = CGColorSpaceCreateDeviceRGB();
    
     if (colorSpace == NULL)
     {
         fprintf(stderr, "Error allocating color space\n");
         return NULL;
     }
    
     // Allocate memory for image data. This is the destination in memory
     // where any drawing to the bitmap context will be rendered.
     bitmapData = malloc( bitmapByteCount );
     if (bitmapData == NULL)
     {
         fprintf (stderr, "Memory not allocated!");
         CGColorSpaceRelease( colorSpace );
         return NULL;
     }
    
     // Create the bitmap context. We want pre-multiplied ARGB, 8-bits
     // per component. Regardless of what the source image format is
     // (CMYK, Grayscale, and so on) it will be converted over to the format
     // specified here by CGBitmapContextCreate.
     context = CGBitmapContextCreate (bitmapData,
                                      pixelsWide,
                                      pixelsHigh,
                                      8,      // bits per component
                                      bitmapBytesPerRow,
                                      colorSpace,
                                      kCGImageAlphaPremultipliedFirst);
     if (context == NULL) {
         free (bitmapData);
         fprintf (stderr, "Context not created!");
     }
     // Make sure and release colorspace before returning
     CGColorSpaceRelease( colorSpace );
     return context;
}
```

**2. 通过UIGraphicsBeginImageContextWithOptions的方法创建**

```objective-c
/**
   *  主要用来创建一个基于位图的图形上下文（这里称之为画布）
   *
   *  @param size   在调用UIGraphicsGetImageFromCurrentImageContext 时返回的画布的尺寸，这个尺寸是以像素点的方式返回，所以实际宽高的输出是size的值乘上后面提供的scale参数
   *  @param opaque 布尔值，表示图像是否不透明
   *  @param scale  生成bitmap的比例因子，如果设置的值为0的话表示和屏幕的scale一样
   */
 UIGraphicsBeginImageContextWithOptions(size, 0, 0);
 //获得当前画布
 CGContextRef context = UIGraphicsGetCurrentContext();
 //结束当前的画布操作
 UIGraphicsEndImageContext();
```

***注：在iOS中，最好用UIGraphicsBeginImageContextWithOptions来代替底层的CGContext。因为如果你用CGContext的方式创建一个画布（原文：offscreen bitmap，这里理解为一个画布）的话，创建出来的画布的坐标系为bitmap graphics context的方式的，而如果你用UIGraphicsBeginImageContextWithOptions创建画布的话，创建出来的画布坐标系是UIView方式的，这两个坐标系Y轴的坐标是刚好相反的。虽然你可以在最后要显示出来的时候手动转换坐标系为UIView的方式，但是明显这样的性能就不是最佳的了。***



