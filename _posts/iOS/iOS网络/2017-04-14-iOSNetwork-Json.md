---
layout: post
title: iOS开发之网络数据解析 ——— Json数据解析
category: iOS网络
tags: Json XML Network
description: Json XML 解析
---

## 前言

​	在服务器请求之后，给客户端返回的数据，一般都是JSON格式或者XML格式（文件下载除外）。本文主要是记录对Json方面知识的学习总结。



## JSON 解析相关

### JSON的定义

> ​	JSON(JavaScript Object Notation, JS 对象标记) 是一种轻量级的数据交换格式。它基于 ECMAScript 规范的一个子集，采用完全独立于编程语言的文本格式来存储和表示数据。简洁和清晰的层次结构使得 JSON 成为理想的数据交换语言。 易于人阅读和编写，同时也易于机器解析和生成，并有效地提升网络传输效率。 —— 【摘抄自百度百科】



### JSON相关说明

​	JSON是一种轻量级的数据格式，在iOS开发中一般用于数据交互。

##### JSON的语法格式

1. JSON 里面的内容一般以键值对（key：value）存在。在iOS中，key的形式必须是字符串，用双引号“”包裹，key和value之间用冒号“:”隔开。

2. 两个数据之间由逗号隔开。

3. 花括号“{}”包起来的数据表示对象。

4. 方括号“[]”包起来的数据表示数组。

   ​



### JSON解析的相关规则

**[Apple 规定：](https://developer.apple.com/reference/foundation/nsjsonserialization?language=objc#overview)**

An object that may be converted to JSON must have the following properties:

- The top level object is an [`NSArray`](https://developer.apple.com/reference/foundation/nsarray?language=objc) or [`NSDictionary`](https://developer.apple.com/reference/foundation/nsdictionary?language=objc).
  - 顶层必须是数组（NSArray）或者字典（NSDictionary）
- All objects are instances of [`NSString`](https://developer.apple.com/reference/foundation/nsstring?language=objc), [`NSNumber`](https://developer.apple.com/reference/foundation/nsnumber?language=objc), [`NSArray`](https://developer.apple.com/reference/foundation/nsarray?language=objc), [`NSDictionary`](https://developer.apple.com/reference/foundation/nsdictionary?language=objc), or [`NSNull`](https://developer.apple.com/reference/foundation/nsnull?language=objc).
  - 所有的对象（内容）都必须是NSSting、NSNumber、NSArray、NSDictionary或者NSNull的实例。
- All dictionary keys are instances of [`NSString`](https://developer.apple.com/reference/foundation/nsstring?language=objc).
  - 所有的dictionary的键必须是字符串NSSting类型
- Numbers are not NaN or infinity.
  - 数字类型不能为空或者无穷

Other rules may apply. Calling [`isValidJSONObject:`](https://developer.apple.com/reference/foundation/nsjsonserialization/1418461-isvalidjsonobject?language=objc) or attempting a conversion are the definitive ways to tell if a given object can be converted to JSON data.//其他的规则可以通过调用isValidJSONObject:方法判断是否可以转换成JSON数据。

> Thread Safety
>
> On iOS 7 and later and macOS 10.9 and later `NSJSONSerialization` is thread safe.
>
> //在iOS7以后和macOS 10.9以后，NSJSONSerialization已经是线程安全的了

**例：{"name":"lipeihan","age":"18","skill":["iOS","Android","Unity 3d"]}；**

**tip：可以通过http://www.json.cn进行在线编辑JSON，以及检测格式是否错误。**



##### JSON和OC转换的对照表

| JSON           | OC           |
| -------------- | ------------ |
| 大括号{}          | NSDictionary |
| 中括号[]          | NSArray      |
| 双引号""          | NSSting      |
| 数字（整型，浮点数，布尔值） | NSNumber     |
| null           | NSNull       |



### JSON解析的相关API

**关于JSON 和Objective-C Object 相互之间的转换主要的类是：NSJSONSerialization。**

**其里面包含的API如下：**

1. 通过JSON数据创建OC对象

   ```objective-c
   /**
    *	通过NSData的方式生成OC对象
    *	data:JSON 数据
    *	opt：读取的选项
    *	error：错误
    *	返回OC对象
    */
   + (id)JSONObjectWithData:(NSData *)data 
                    options:(NSJSONReadingOptions)opt 
                      error:(NSError * _Nullable *)error;
   /**
    *	通过NSInputStream的方式生成OC对象
    *	data:JSON 数据
    *	opt：读取的选项
    *	error：错误
    *	返回OC对象
    */
   + (id)JSONObjectWithStream:(NSInputStream *)stream 
                      options:(NSJSONReadingOptions)opt 
                        error:(NSError * _Nullable *)error;
   ```

2. 通过OC对象生成JSON Data数据

   ```objective-c
   /**
    *	通过OC对象生成JSON Data数据
    *	被转换的OC对象
    *	opt：生成JSON数据的选项
    *	error：错误
    *	返回JSON Data数据
    */
   + (NSData *)dataWithJSONObject:(id)obj 
                          options:(NSJSONWritingOptions)opt 
                            error:(NSError * _Nullable *)error;

   /**
    *	通过OC对象生成JSON Data数据
    *	被转换的OC对象
    *	opt：生成JSON数据的选项
    *	error：错误
    *	返回写入的字节数，如果是0，则表示写入错误
    */
   + (NSInteger)writeJSONObject:(id)obj 
                       toStream:(NSOutputStream *)stream 
                        options:(NSJSONWritingOptions)opt 
                          error:(NSError * _Nullable *)error;

   /**
    *	判断返回OC对象是否能被转换成有效的JSON数据
    */
   + (BOOL)isValidJSONObject:(id)obj;
   ```

3. 常量

   ```objective-c

   1、NSJSONReadingOptions
     /**
       *	NSJSONReadingMutableContainers  = (1UL << 0),  
       *	容器可变，NSMutableDictionary 或NSMutableArray。
       *
       *	NSJSONReadingMutableLeaves      = (1UL << 1),
       *	叶子可变，返回的 JSON 对象中字符串的值为 NSMutableString。
       *
       *	NSJSONReadingAllowFragments     = (1UL << 2)
       *	允许 JSON 字符串最外层既不是 NSArray 也不是 NSDictionary，但必须是有效的 JSON 片段
       */
     
   2、NSJSONWritingOptions：
     /**
       *	NSJSONWritingPrettyPrinted = (1UL << 0)             漂亮的格式打印 
       */
   ```

   ​

### JSON解析Demo

```objective-c
//JSON To Objective-C
- (void)convertJsonToOC {
    
    NSString *jsonString = @"{\"name\":\"lipeihan\",\"age\":18,\"skill\":[\"iOS\",\"Android\",\"Unity 3d\"]}";
    
    NSData *jsonData =  [jsonString dataUsingEncoding:NSUTF8StringEncoding];
    
    /**
     *	通过NSData的方式生成OC对象
     *	data:JSON 数据
     *	options:
     *      NSJSONReadingOptions
     *          NSJSONReadingMutableContainers  = (1UL << 0),
     *          容器可变，NSMutableDictionary 或NSMutableArray。
     *
     *          NSJSONReadingMutableLeaves      = (1UL << 1),
     *          叶子可变，返回的 JSON 对象中字符串的值为 NSMutableString。
     *
     *          NSJSONReadingAllowFragments     = (1UL << 2)
     *          允许 JSON 字符串最外层既不是 NSArray 也不是 NSDictionary，但必须是有效的 JSON 片段
     *	error：错误
     *	return：返回OC对象
     */
    NSDictionary *jsonObj = [NSJSONSerialization JSONObjectWithData:jsonData options:NSJSONReadingMutableContainers error:nil];
    
    NSLog(@"convertJsonToOC = %@" ,jsonObj);
}
```



**运行结果：**

![JSON1](/assets/postImages/iOSNetwork/Json/JSON1.jpeg)



```objective-c
//Objective-C Object to JSON
- (void)convertOCToJson {

    NSString *testString = @"Test Json";
    NSArray *testArray = @[@"iOS",@"Android",@"Unity 3d"];
 
    NSDictionary *testDictionary = @{
                                     @"name":@"lipeihan",
                                     @"age":@(18),
                                     @"skill":@[@"iOS",@"Android",@"Unity 3d"]
                                     };
    
    NSLog(@"-----testString----");
    [self jsonConvertedByObjectiveCObject:testString];
    
    NSLog(@"-----testArray----");
    [self jsonConvertedByObjectiveCObject:testArray];
    
    NSLog(@"-----testDictionary----");
    [self jsonConvertedByObjectiveCObject:testDictionary];
    
}

- (BOOL)jsonConvertedByObjectiveCObject:(id)ocObject {

    BOOL isValid = [NSJSONSerialization isValidJSONObject:ocObject];
    
    if (isValid) {
        
        /**
         *	通过OC对象生成JSON Data数据
         *	obj:被转换的OC对象
         *	options：生成JSON数据的选项
         *	error：错误
         *	返回JSON Data数据
         */
        NSData *jsonData = [NSJSONSerialization dataWithJSONObject:ocObject
                                                           options:NSJSONWritingPrettyPrinted
                                                             error:nil];
        
        NSString *jsonString = [[NSString alloc] initWithData:jsonData
                                                     encoding:NSUTF8StringEncoding];
        
        NSLog(@"jsonString = %@",jsonString);
    } else {
        NSLog(@"ocObject can not be converted to JSON Data");
    }
 
    return isValid;
}
```



**运行输出**

```objective-c
2017-04-14 19:23:53.141 JsonTestDemo[2078:310884] -----testString----
2017-04-14 19:23:53.142 JsonTestDemo[2078:310884] ocObject can not be converted to JSON Data
  //正如上文提到的，并不是所有的对象都能转换成json数据
  
2017-04-14 19:23:53.142 JsonTestDemo[2078:310884] -----testArray----
2017-04-14 19:23:53.143 JsonTestDemo[2078:310884] jsonString = [
  "iOS",
  "Android",
  "Unity 3d"
]
2017-04-14 19:24:24.919 JsonTestDemo[2078:310884] -----testDictionary----
2017-04-14 19:24:25.775 JsonTestDemo[2078:310884] jsonString = {
  "name" : "lipeihan",
  "age" : 18,
  "skill" : [
    "iOS",
    "Android",
    "Unity 3d"
  ]
}
```

## 附录

### Demo地址

https://github.com/lipeihan/JsonTestDemo/tree/master

### 最后

如果对大家有帮助，请[github上follow和star](https://github.com/lipeihan)，本文发布在[Pierce的技术博客](https://lipeihan.github.io)，转载请注明出处