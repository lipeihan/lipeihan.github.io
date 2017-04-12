---
layout: post
title: iOS人脸识别调研
category: iOS人脸识别
tags: 人脸识别
keywords: iOS FaceFeature
description: iOS FaceFeature
---

#### 1、系统的人脸识别

​    使用CoreImage里面的CIFaceFeature

- ​     可以实现图片的人脸检测，以及人脸的眼睛，嘴巴的位置定位。
- ​     可以检测眨眼和微笑
- ​     实现起来比较简单。
- ​     精度还算比较好

​     

​     实时视频预览，拍照

1. AVCapture ＋ CIFaceFeature

​     在 AVCaptureVideoDataOutputSampleBufferDelegate 协议的

```objective-c
- (void)captureOutput:(AVCaptureOutput *)captureOutput didOutputSampleBuffer:(CMSampleBufferRef)sampleBuffer fromConnection:(AVCaptureConnection *)connection；
```

​    方法里面拿出当前帧的图片进行人脸识别：微笑相机

 

2. AVCapture中的 AVMetadataFaceObject

- 设置 AVCaptureMetadataOutput中的 

  ```objective-c
  metadataObjectTypes = @[AVMetadataObjectTypeFace];
  ```

  并添加到 AVCaptureSession中去

- 监听 AVCaptureMetadataOutputObjectsDelegate回调并在回调方法：

  ```objective-c
  - (void)captureOutput:(AVCaptureOutput *)captureOutput didOutputMetadataObjects:(NSArray *)metadataObjects fromConnection:(AVCaptureConnection *)connection;
  ```

  中取得metadataObjects数组。

- 在 AVCaptureVideoDataOutputSampleBufferDelegate 协议的：

  ```objective-c
  - (void)captureOutput:(AVCaptureOutput *)captureOutput didOutputSampleBuffer:(CMSampleBufferRef)sampleBuffer fromConnection:(AVCaptureConnection *)connection;
  ```

  方法里面取出metadataObjects数组，强制转换为 AVMetadataFaceObject对象作处理。

​     

​     **以上两种方法由官方提供，返回识别到的人脸的范围，前置摄像头加上马赛克特效很流畅。后置摄像头比较慢，不过应该可以调低分辨率来提高效率。**

 

3. GPUImage 有对方法“1”进行了封装，运行demo性能相对还是比较高。

 

#### 2、Face++本地库 FaceppLocalDetector

- 使用简单     
- 只能检测到人脸的范围，精度相对于系统的会高一些（之前系统那些判断不到的，还有误判的用这个库都没有出现）
- 有在线版
- 有提供付费的SDK，人脸检测、人脸关键点检测和人脸分析，官网打出的广告是美颜相机，美图秀秀等比较出名的几个都是用的他的SDK。

 

#### 3、讯飞开发平台的人脸识别

- demo很详细，提供的framework里面每个.h文件都有很详细的中文注释
- 有在线版本
- 本地静态图片识别，只能检测到人脸的范围，精度想对于系统的会高一些（之前系统那些判断不到的，还有误判的用这个库都没有出现）
- 视频预览人脸识别（AVCapture），精度高，效率高，提供人脸眉毛，眼睛，鼻子，嘴巴共21个关键点检测。效果很好。
- 离线版官方是说免费的。

 

#### 4、OpenCV

- 基于C++
- 可以通过官方提供的对应的haarcascade_XXXXXX.xml文件检测出脸，眼睛，鼻子，嘴巴等，脸的精度还可以，其余器官的很一般
- haarcascade_XXXXXX.xml文件我理解为物体特征分类器，里面记录一系列物体特征的集合，检测的精度和这些文件相关联，不过里面的数据很难理解

