---
layout: post
title: 系统人脸识别：iOS 官方人脸检测Demo
category: iOS人脸识别
tags: 人脸识别
keywords: iOS FaceFeature
description: iOS FaceFeature
---

```objective-c
/**
 *  注意：CIFaceFeature 提供的坐标都是和CGContext一样是反方向的，所以以下的画图操作都是用了CGContext的方式
 */
- (void)faceRegonized {
    CIContext *context = [CIContext contextWithOptions:nil];
    UIImage *imageInput = [UIImage imageNamed:@"example"];
    CIImage *image = [CIImage imageWithCGImage:imageInput.CGImage];
   
    //设置识别参数
    NSDictionary *param = [NSDictionary dictionaryWithObject:CIDetectorAccuracyHigh
                                                      forKey:CIDetectorAccuracy];
    //声明一个CIDetector，并设定识别类型
    CIDetector* faceDetector = [CIDetector detectorOfType:CIDetectorTypeFace
                                                  context:context options:param];//取得识别结果
    NSArray *detectResult = [faceDetector featuresInImage:image];

    //开始一个画布，并将画布原图画到画布中去
    UIGraphicsBeginImageContext(image.size);
    CGContextDrawImage(UIGraphicsGetCurrentContext(), CGRectMake(0, 0, image.size.width, image.size.height), image.CGImage);

    for(CIFaceFeature* faceFeature in detectResult) {
        //脸部
        UIView* faceView = [[UIView alloc] initWithFrame:faceFeature.bounds];
        faceView.layer.borderWidth = 1;
        faceView.layer.borderColor = [UIColor orangeColor].CGColor;
        
        CGFloat faceWidth = faceFeature.bounds.size.width;

        
         //CIFaceFeature 所有的属性
        NSLog(@"-------------------------------------\n");
        NSLog(@"faceBounds = %@",NSStringFromCGRect(faceFeature.bounds));
        NSLog(@"face hasLeftEyePosition = %d",faceFeature.hasLeftEyePosition);
        NSLog(@"face leftEyePosition = %@",NSStringFromCGPoint(faceFeature.leftEyePosition));
        NSLog(@"face hasRightEyePosition = %d",faceFeature.hasRightEyePosition);
        NSLog(@"face rightEyePosition = %@",NSStringFromCGPoint(faceFeature.rightEyePosition));
        NSLog(@"face hasMouthPosition = %d",faceFeature.hasMouthPosition);
        NSLog(@"face mouthPosition = %@",NSStringFromCGPoint(faceFeature.mouthPosition));
        NSLog(@"face hasTrackingID = %d",faceFeature.hasTrackingID);
        NSLog(@"face trackingID = %d",faceFeature.trackingID);
        NSLog(@"face hasTrackingFrameCount = %d",faceFeature.hasTrackingFrameCount);
        NSLog(@"face trackingFrameCount = %d",faceFeature.trackingFrameCount);
        NSLog(@"face hasFaceAngle = %d",faceFeature.hasFaceAngle);
        NSLog(@"face faceAngle = %f",faceFeature.faceAngle);
        NSLog(@"face hasSmile = %d",faceFeature.hasSmile);
        NSLog(@"face leftEyeClosed = %d",faceFeature.leftEyeClosed);
        NSLog(@"face rightEyeClosed = %d",faceFeature.rightEyeClosed);
        NSLog(@"\n-------------------------------------");
       
        //画一个矩形出识别出来的脸部的方位
        UIBezierPath *path = [UIBezierPath bezierPath];
        CGPoint point = faceFeature.bounds.origin;
        CGSize size = faceFeature.bounds.size;
        [path moveToPoint:point];
        [path addLineToPoint:CGPointMake(point.x + size.width, point.y)];
        [path addLineToPoint:CGPointMake(point.x + size.width, point.y + size.height)];
        [path addLineToPoint:CGPointMake(point.x, point.y+ size.height)];
        [path addLineToPoint:point];
        [[UIColor redColor] setStroke];
        [path stroke];
       
        [[UIColor redColor] setStroke];
        [path stroke];

        //左眼
        if (faceFeature.hasLeftEyePosition) {
            [self drawCycleAtPoint:faceFeature.leftEyePosition withRadius:faceWidth*0.1];
        }
       
        //右眼
        if (faceFeature.hasRightEyePosition) {
             [self drawCycleAtPoint:faceFeature.rightEyePosition withRadius:faceWidth*0.1];
        }
        //嘴巴
        if (faceFeature.hasMouthPosition) {
             [self drawCycleAtpoint:faceFeature.mouthPosition withRadius:faceWidth*0.2];
        }
    }

     //获得所有在原图中圈出来脸之后最后的图片，此时的图片是反方向的
    UIImage *tempImage = UIGraphicsGetImageFromCurrentImageContext();
   
    //将画布清空，并把放方向的图片用CG的方式画到原图中去（CG画出来的东西都是原来的反方向，并不是个好方法，实际开发中用别的方法）
    CGContextClearRect(UIGraphicsGetCurrentContext(), CGRectMake(0, 0, image.size.width, image.size.height));
    CGContextDrawImage(UIGraphicsGetCurrentContext(), CGRectMake(0, 0, image.size.width, image.size.height), tempImage.CGImage);
   
    //获得重画之后的图片，并结束画布
    tempImage = UIGraphicsGetImageFromCurrentImageContext();
    UIGraphicsEndImageContext();
   
    //将最终的图片放到View中去
    [self.imageView setImage:tempImage];
}

- (void)drawCycleAtPoint:(CGPoint)point withRadius:(CGFloat)radius {
    /*画圆*/
    //边框圆
    CGContextSetRGBStrokeColor(UIGraphicsGetCurrentContext(),1,0,0,1.0);//画笔线的颜色
    CGContextSetLineWidth(UIGraphicsGetCurrentContext(), 1.0);//线的宽度
    //void CGContextAddArc(CGContextRef c,CGFloat x, CGFloat y,CGFloat radius,CGFloat startAngle,CGFloat endAngle, int clockwise)1弧度＝180°/π （≈57.3°） 度＝弧度×180°/π 360°＝360×π/180 ＝2π 弧度
    // x,y为圆点坐标，radius半径，startAngle为开始的弧度，endAngle为 结束的弧度，clockwise 0为顺时针，1为逆时针。
    CGContextAddArc(UIGraphicsGetCurrentContext(), point.x, point.y, radius, 0, 2*M_PI, 0); //添加一个圆
    CGContextDrawPath(UIGraphicsGetCurrentContext(), kCGPathStroke); //绘制路径
}
```

