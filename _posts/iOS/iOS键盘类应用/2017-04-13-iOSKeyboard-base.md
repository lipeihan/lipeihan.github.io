---
layout: post
title: 键盘项目相关文档
category: iOS键盘
tags: keyboard
description: 键盘项目相关文档
---



***该文档主要是记录一些关于自定义键盘的相关操作，基本用法，注意事项等。***

## 1、关于自定义键盘

##### 1、官方文档上的定义

​	A custom keyboard replaces the system keyboard for users who want capabilities such as a 					    novel text input method or the ability to enter text in a language not otherwise supported in iOS. The essential function of a custom keyboard is simple: Respond to taps, gestures, or other input events and provide text, in the form of an unattributed NSString object, at the text insertion point of the current text input object.

​	由这个定义可以知道，在自定义键盘中，你可以像普通应用一样做点击事件和手势，或者做键盘应该有的输入事件。其中需要注意的是，作为键盘输入的文本，必须是unattributed 的 NSString对象。



##### 2、在自定义键盘中至少必须提供的东西

- 一个基本的键盘页面


- 一个切换到别的键盘的按键

  ​

##### 3、关于第三方键盘的使用权限

- 当用户在安全文本（比如将secureTextEntry属性设为YES的输入框）输入对象中输入内容，iOS系统将暂时强行调用系统默认输入法，以保证用户的信息安全。当用户在其他非安全文本输入对象中输入时，您的第三方输入法将被重新调用。

  ​

- 第三方输入法也没有资格在电话号码对象中输入（就像联系人应用中的号码字段）。这些输入对象是专门为电信运营商所指定的一个小的数字字符集而建立的字符串对象，可以通过其是否具有下面任意一个输入法类型来识别。

  1.UIKeyboardTypePhonePad

  2.UIKeyboardTypeNamePhonePad

  当用户在电话号码对象中输入时，系统将暂时强行用相应的标准系统输入法来替换。而当用户在其他的输入对象中输入并需要一个标准输入法适配其类型特点的时候，您的第三方输入法将会自动恢复。

  ​

- 程序开发者可以选择禁止所有的第三方输入法在他们的应用中运行：使用定义在UIApplicationDelegate协议中的application:shouldAllowExtensionPointIdentifier:方法（返回值为NO）,从而始终使用系统输入法。

  ​

- 由于一个第三方输入法只能在其UIInputViewController对象的主视图内显示，它将不能够选择输入框中的文字。文本选择是在应用程序的控制下，而第三方输入法并没有权限来访问它。因此这也就意味着在编辑信息的时候无法移动光标，粘贴、复制以及剪切功能将无法使用。

  ​

- 第三方输入法和iOS 8.0的所有应用程序扩展一样，没有权限来访问设备话筒，所以听写输入也是不可能实现的。

  ​


## 2、键盘项目的创建

##### 1、按照常规的方式新建一个项目

##### 2、打开新建的项目，在Xcode中选中File —> New —>Target...

![Create1](/assets/postImages/iOSkeyboard/Img/Create1.png)

##### 3、选中iOS —> Application Extension,然后双击右边的Custom Keyboard跳转到项目创建页面，输入Project name并点击finish，创建成功。

![Create2](/assets/postImages/iOSkeyboard/Img/Create2.png)

##### 4、创建后,在项目文件的Target中会多一个你创建的Keyboard Extension Target，项目右边的文件结构中多了一个Keyboard Extension 文件夹，并且文件夹中多了KeyboardViewController（后面说明）文件。 ![Create3](/assets/postImages/iOSkeyboard/Img/Create3.png)



## 3、Custom Keyboard 开发的相关说明

下图显示了一些运行输入法时的重要元素以及其在一个典型开发流程中的位置：

 ![keyboard_architecture_2x](/assets/postImages/iOSkeyboard/Img/keyboard_architecture_2x.png)

#### 1、键盘应用的入口

###### 	 KeyboardViewController	

​	创建项目以后，xcode提供了一个名为KeyboardViewController的类，它是UIInputViewController的子类，作为键盘应用的入口。在Info.plist中也的NSExtension —> NSExtensionPrincipalClass 中也可以改成别的继承自UIInputViewController的ViewController作为入口。

###### UIInputViewController


​	UIInputViewController 提供的常用方法和属性：

​	1、dismissKeyboard 方法：关掉键盘

​	2、advanceToNextInputMode方法：切换到下一个键盘（在自定义键盘中必须使用）

​	3、inputView 属性：相当于普通应用ViewController.view.(通过测试，UIInputViewController.view 				和UIInputViewController.inputView 是同一个对象)，可以通过向inputView中添加自定义的Subviews来显示自定义的View，给inputView重新赋值为一个自定义的UIInputView对象作为显示。

​	4、textDocumentProxy 属性：一个实现UIKeyInput协议的对象，通过这个属性可以实现判断是否有输入，给输入框插入文本，文本回退等功能（后面解释）。

​	还有一些方法暂时没用到过，还有待发掘。

#### 2、键盘输入等基本操作

​	键盘的输入操作主要是通过UIInputViewController中的textDocumentProxy 属性提供的几个主要方法：

- 输入文本：

  ​	[self.textDocumentProxy insertText:@"hello "]; 

- 回退操作：

  ​	[self.textDocumentProxy deleteBackward];

- 输入换行操作：

  ​	[self.textDocumentProxy insertText:@"\n"];

- 输入的上一个字符

  ​	@property (nonatomic, readonly) NSString *documentContextBeforeInput;

- 即将输入的一个字符

  ​	@property (nonatomic, readonly) NSString *documentContextAfterInput;

- 将输入的字符移动到某一位置

  ​	-(void)adjustTextPositionByCharacterOffset:(NSInteger)offset;



​	键盘切换操作：

- 关掉键盘，在UIInputViewController中调用

  ​[self dismissKeyboard];	

- 切换到下一个键盘，在UIInputViewController中调用

  [self advanceToNextInputMode];

  ​

#### 3、修改键盘高度

    //官方文档：you can adjust a custom keyboard’s height any time after its primary view 		initially draws on screen.
    //但是如果修改高度的约束的时候会有约束冲突的warning输出
    //有些情况下，如在邮件中退到后台在回来重新显示键盘时，ViewDidAppear 不会被运行到，所以修改约束在	ViewWillAppear中处理
    NSLayoutConstraint *heightConstraint =
    [NSLayoutConstraint constraintWithItem: self.view
                                 attribute: NSLayoutAttributeHeight
                                 relatedBy: NSLayoutRelationEqual
                                    toItem: nil
                                 attribute: NSLayoutAttributeNotAnAttribute
                                multiplier: 0.0
                                  constant:kKeyboardHeight];
    [self.view addConstraint:heightConstraint];


#### 4、关于info.plist配置中，一些关键的key说明

###### 1、Bundle display name && Bundle name

​	在Settings > General > Keyboard > Keyboards 中显示的keyboard 名称为：

​		Keyboard extension Bundle name - Main Application Bundle name

​	在Settings > General > Keyboard > Keyboards > Add New Keyboard…中显示的名称为：

​		Keyboard extension Bundle name

###### 2、NSExtension

 ![NSExtension](/assets/postImages/iOSkeyboard/Img/NSExtension.png)

NSExtensionAttributes里面的配置表示着custom keyboard的特点和需求。

- IsASCIICapable--布尔值，默认为NO,表示第三方输入法是否可以向文档中插入ASCII字符串。如果您的第三方输入法专门提供UIKeyboardTypeASCIICapable输入法特性，那么将这个选项设置为YES。


- PrefersRightToLeft--布尔值，默认为NO,表示第三方输入法使用的是否是一个从右到左的语言。如果您的输入法主语言是从右到左书写的，那么讲这个选项设置为YES。


- PrimaryLanguage--字符串值，默认为en-US（美国英语），用以表示您输入法的主语言，使用模式为<语言>-<地区>。您可以在这个页面找到某一语言和地区所对应的字符串。


- RequestsOpenAccess--布尔值，既Full Access（下一节会说明），默认为NO，表示第三方输入法是否要扩展到已满足基本输入法需求的沙箱之外。如果开放存取功能，您的输入法将获得如下特性，但每一个都有相应的责任：

  ​

  ​	1.访问位置服务、通信录数据库以及相机，每一个都需在第一次访问时获得授权。

  ​	2.可选择与容纳该输入法的应用的共享容器，数据，资源等，比如在应用中可以定制词汇表，数据交流等。

  ​	3.能够将输入的字符和其他输入事件上传至服务器进行处理。

  ​	4.可以使用剪切板 UIPasteboard对象

  ​	5.播放音频，包括按键音：通过调用 playInputClick 方法设置

  ​	6.访问iCloud，例如，能够确保输入法的设置以及您的自动修正词汇能够在所有用户设备上同步。

  ​	7.通过包含还输入法的应用，能够访问Game Center和应用内购买。

  ​	8.能够和受控应用进行协同，如果您使用来设计该输入法以支持移动设备管理(MDM)。

  ​


#### 5、Full Access

| Full Access     | 能力与限制                                    | 隐私考虑                                     |
| :-------------- | :--------------------------------------- | :--------------------------------------- |
| 关闭         （默认） | 1、输入法可以执行一个基本输入法的所有正常功能；                                         2、存储一个常用文本的自动更正和建议到单词词典（lexicon）  ；                   3、有权使用在设置中的用户词典 ；                                                                     4、不能和包含它的应用共享数据;                                                                                    5、除了自定义键盘自身容器意外，无法访问其他文件系统；                             6、无法直接或者间接参与iCloud，游戏中心，或在应用程序中购买。 | 用户在使用自定义键盘的过程中知道输入的内容只会传到输入框所在的app中      |
| 开启              | 1、除联网外的所有自定义键盘拥有的功能                                                         2、访问位置服务、通信录数据库以及相机，每一个都需在第一次访问时获得授权。                                                                                                                        3、能够将输入的字符和其他输入事件上传至服务器进行处理。                       4、可选择与容纳该输入法的应用的共享容器，数据，资源等，比如在应用中可以定制词汇表，数据交流等。                                                                                    5、可以使用剪切板 UIPasteboard对象                                                                        6、播放音频，包括按键音：通过调用 playInputClick 方法设置                           7、访问iCloud，例如，能够确保输入法的设置以及您的自动修正词汇能够在所有用户设备上同步。                                                                                                  8、通过包含还输入法的应用，能够访问Game Center和应用内购买。                                                                                              9、能够和受控应用进行协同，如果您使用来设计该输入法以支持移动设备管理(MDM)。 | 1、用户知道他们输入的内容可能会提供给键盘开发者                                                                                                                                                                                                                                                                                                                                                2、必须遵守在App Store审核指南以及iOS开发者程序许可协议中的联网输入法指南，可以在[App Review Support](https://developer.apple.com/support/app-review/)页面中找到 |



###### **在键盘应用中是否获得Full Access 的方法：**

    if ([UIPasteboard generalPasteboard]) {
     	//Full Access
    } else {
     	//No Full Access
    }

#### 6、主应用程序和键盘应用的数据交流



​	***首先需要有开了Full Access之后才能在你主应用和键盘应用之间作数据交流，所以在键盘的info.plist文件中将RequestsOpenAccess键设置yes，让用户在设置中有Full Access选项***



###### 1、创建*.entitlements 文件

- 在Xcode上方的导航栏中，选中File > New >File，然后选中右边的Resource，再创建一个plist文件。 ![AddPlist](/assets/postImages/iOSkeyboard/Img/AddPlist.png)


- 选中-右键新创建的plist文件，然后进入Finder，将plist文件的后缀由*.plist 改成 *.entitlements，

- 重新将创建的*.entitlements文件拉到项目中去，并勾选上主应用和拓展应用的Target

   ![AddFile](/assets/postImages/iOSkeyboard/Img/AddFile.png)

- 在xcode中打开*.entitlements文件，添加一个array类型的key：com.apple.security.application-groups

- 然后添加一个Item代表Group Id ![GroupId](/assets/postImages/iOSkeyboard/Img/GroupId.png)


​	***这个GroupID作为主应用和Extension应用数据交流的Identity***

- 分别在Main Target 和 Extension Target 的Build setting中，查找Code Signing Entitlements,并在这个key中加入这个文件的相对路径，如：Test.entitlements文件放在项目文件中的根目录，则直接写入Test.entitlements，如果Test.entitlements放在A目录，则写入A/Test.entitlements。 ![GroupId](/assets/postImages/iOSkeyboard/Img/GroupId.png)
- 键盘和主应用通过[[NSUserDefaults alloc] initWithSuiteName:kGroupIdentifier]; 获得UserDefault对象进行数据交流。




#### 7、其他问题

待补充