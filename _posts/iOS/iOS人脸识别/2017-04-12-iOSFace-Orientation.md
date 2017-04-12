---
layout: post
title: 系统人脸识别：解决系统人脸识别得到的图片方向问题
category: iOS人脸识别
tags: 人脸识别
keywords: iOS FaceFeature
description: iOS FaceFeature
---

##### 1、最后获得的图像反向转换

```objective-c
int height = CVPixelBufferGetHeight(pixelBuffer);
CGAffineTransform transform = CGAffineTransformMakeScale(1, -1);
transform = CGAffineTransformTranslate(transform, 0, -1 * height);
/* Do your face detection */
CGRect faceRect = CGRectApplyAffineTransform(feature.bounds, transform);
CGPoint mouthPoint = CGPointApplyAffineTransform(feature.mouthPosition, transform);
```

##### 2、如果图像一开始就不是正向的

```objective-c
/**
  *   问题来自 UIImage 的取向和 CIDetectorImageOrientation 之间的区别。从 iOS 的文档：
     CIDetectorImageOrientation

     要检测密钥被用来指定图像的显示方向的功能。此密钥是一个 NSNumber 对象具有相同的值定义的 TIFF 和 EXIF 规格 ；值的范围可以从 1 到 8。值指定原点 (0，0) 的图像所在的位置。如果不存在，默认值是 1，这意味着图像的原点是左上角。每个值所指定的图像来源的详细信息，请参阅 kCGImagePropertyOrientation。

     可用在 iOS 5.0 和更高版本。

     在 CIDetector.h 中声明。
     
     所以现在的问题是这些两个方向之间的转换，在这里是我的代码中的所作所为、 我测试和它为所有方向工作：
  *
  */

    int exifOrientation;

    switch (image.imageOrientation) {

        case UIImageOrientationUp: {
  
            exifOrientation = 1;
            break; 
        }

        case UIImageOrientationDown: {
          
          	exifOrientation = 3;
            break;
        }

        case UIImageOrientationLeft: {
          
            exifOrientation = 8;
            break;
        }

        case UIImageOrientationRight: {
          
            exifOrientation = 6;
            break;
        }

        case UIImageOrientationUpMirrored: {
         
          	exifOrientation = 2;
            break;
        }

        case UIImageOrientationDownMirrored: {
          
            exifOrientation = 4;
            break;
        }

        case UIImageOrientationLeftMirrored: {
          
            exifOrientation = 5;
            break;
        }

        case UIImageOrientationRightMirrored: {
          	
          	exifOrientation = 7;
            break;
        }
    }

    NSDictionary *detectorOptions = @{ CIDetectorAccuracy : CIDetectorAccuracyHigh }; 
// TODO: read doc for more tuneups

    CIDetector *faceDetector = [CIDetector detectorOfType:CIDetectorTypeFace context:nil options:detectorOptions];

    NSArray *features = [faceDetector featuresInImage:[CIImage imageWithCGImage:image.CGImage]
                                              options:@{CIDetectorImageOrientation:[NSNumber 
                                                                                  	numberWithInt:exifOrientation]}];
```



