---
layout: post
title: Video Player调研
category: iOS视频
tags: video
keywords: iOS video
description: iOS survey Video Player
---

#### 一、官方提供的播放方式：AVPlayer、MPMediaPlayer

-----

- 优点：提供较完整较稳定的API
- 缺点：支持的视频编码格式很有限：H.264、MPEG-4，扩展名（压缩格式）：.mp4、.mov、.m4v、.m2v、.3gp、.3g2等
- ​

通过输出 [AVURLAsset audiovisualMIMETypes]可以查到官方提供的APP支持的audio，video等Mime Type

 

#### 二、使用基于ffmpeg的kxmovie

-----

1. 在github上面下载kxmovieDemo[https://github.com/kinglonghuang/kxmovie](https://github.com/kinglonghuang/kxmovie)
2. 将编译好的FFMPEG库拖到Demo中(注：需要是编译好的)
3. 设置User Header Search Paths :$(SRCROOT)/KxMovieDemo/FFmpeg-iOS/include(注：这里的路径为/FFmpeg-iOS/include所在项目的相对路径)
4. 添加必要的库![pic1](/assets/postImages/iOSVideo/SurveyVideoPlayer/pic1.png)
5. 遇到的问题：

1）![problem1](/assets/postImages/iOSVideo/SurveyVideoPlayer/problem1.png)

解决方法：注释改行代码

 

2）![problem2](/assets/postImages/iOSVideo/SurveyVideoPlayer/problem2.png)

解决方法：改 PIX_FMT_NONE为 AV_PIX_FMT_NONE

 

3)![problem3](/assets/postImages/iOSVideo/SurveyVideoPlayer/problem3.png)

解决方法：改 avcodec_alloc_frame();为 av_frame_alloc();

