## 前言

本文是基于官方最新稳定版本`get: ^4.3.8`来进行开发

本系列课程将会以最简单的方式来进行讲解，对于零基础也可以轻松掌握，全文深入浅出，每一个章节都会有对于案例来展示效果以及如何运用。

整一个课程系列都是极简的，如果你愿意花两个小时来学习，那么你将很容易掌握`GetX`并且用`GetX`应用到企业项目中。

## 视频教程地址

[视频教程地址](https://www.bilibili.com/video/BV1uq4y1U7fF?p=1)

##### 学完本系列课程你将学会：

-   知道什么是GetX
-   会使用Snackbar
-   会使用Dialog
-   会使用BottomSheet
-   会用Navigation
-   会用Obx响应式状态管理
-   会使用GetXController（三种使用方式GetBuilder、事件监听、生命周期、UniqueID）
-   语言国际化配置
-   依赖注入
-   GetX Service
-   GetX Binding（绑定）
-   GetX获取API接口数据并显示
-   获取存储和邮件验证
-   GetView和GetWidget
-   Get Cli 脚手架使用以及常用命令
-   GetX 中使用 GetConnect 和 StateMixin 获取 API 数据

## 为什么讲解此系列课程？

目前我在市面上找不到一个好的对`GetX`全面的教程，如果纯看官方文档去自行理解，学习周期长。而视频方面要么就是篇幅太长，学习成本高。要么就是比较零散，学起来像一只无头的苍蝇。所以我干脆直接自己出一个课程，该课程系列从简至深，方便大家系统的对`GetX`的认识。虽然讲解该系列课程耗费了我大量的时间，但是只要对你有帮助并且能够从中受益，这样就很好。

## 什么是GetX ?

[GetX中文官方文档](https://github.com/jonataslaw/getx/blob/master/README.zh-cn.md#%E5%85%B3%E4%BA%8Eget) [pub地址](https://pub.flutter-io.cn/packages/get)

> -   GetX 是 Flutter 上的一个轻量且强大的解决方案：高性能的状态管理、智能的依赖注入和便捷的路由管理。
> -   GetX 有3个基本原则：
>     -   **性能：** GetX 专注于性能和最小资源消耗。GetX 打包后的apk占用大小和运行时的内存占用与其他状态管理插件不相上下。如果你感兴趣，这里有一个[性能测试](https://github.com/jonataslaw/benchmarks)。
>     -   **效率：** GetX 的语法非常简捷，并保持了极高的性能，能极大缩短你的开发时长。
>     -   **结构：** GetX 可以将界面、逻辑、依赖和路由完全解耦，用起来更清爽，逻辑更清晰，代码更容易维护。

## 为什么使用GetX？

我们知道状态管理的框架有很多，使用原生的相对复杂，都是用`ChangeNotifier`来更新`Widget`，如果对于业务逻辑比较复杂还使用这种方式无疑是致命的。

其他的状态管理器也不错，但有其细微的差别。

> -   BLoC非常安全和高效，但是对于初学者来说非常复杂，这使得人们无法使用Flutter进行开发。
> -   MobX比BLoC更容易，而且是响应式的，几乎是完美的，但是你需要使用一个代码生成器，对于大型应用来说，这降低了生产力，因为你需要喝很多咖啡，直到你的代码在`flutter clean`之后再次准备好（这不是MobX的错，而是codegen真的很慢！）。
> -   Provider使用InheritedWidget来传递相同的监听器，以此来解决上面报告的ChangeNotifier的问题，这意味着对其ChangeNotifier类的任何访问都必须在widget树内。

## GetX响应式状态管理器

响应式编程可能会让很多人感到陌生，因为它很复杂，但是GetX将响应式编程变得非常简单。使用 Get 的响应式编程就像使用 setState 一样简单。

> -   你不需要创建StreamControllers.
> -   你不需要为每个变量创建一个StreamBuilder。
> -   你不需要为每个状态创建一个类。
> -   你不需要为一个初始值创建一个get。

## 安装

将 GetX 添加到你的 pubspec.yaml 文件中。

`1 2  dependencies:   get: ^4.3.8`

在需要用到的文件中导入，它将被使用。

`1  import 'package:get/get.dart';`

## Snackbar介绍

如果想在应用程序中触发某些特定的事件后，需要弹出一则快捷消息，那么使用`Snackbar`则是最佳的选择，接下来我们看一下`GetX`如何来联调`Snackbar`来使用。

### Snackbar基本使用

#### 第一步：应用程序入口设置

当我们导入依赖后，在应用程序顶层把`GetMaterialApp` 作为顶层，如下所示

`1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18  import 'package:flutter/material.dart'; import 'package:get/get.dart';  void main() {   runApp(MyApp()); }  class MyApp extends StatelessWidget {   @override   Widget build(BuildContext context) {     return GetMaterialApp(       title: "GetX",       home: Scaffold(         appBar: AppBar(title: Text("GetX Title"),),       ),     );   } }`

#### 第二步：调用snackbar

我们可以通过`Get.snackbar()` 来显示 `snackbar` ，如下所示

`1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20 21 22 23 24 25 26 27 28 29 30 31 32 33  import 'package:flutter/material.dart'; import 'package:get/get.dart';  void main() {   runApp(MyApp()); }  class MyApp extends StatelessWidget {   @override   Widget build(BuildContext context) {     return GetMaterialApp(       title: "GetX",       home: Scaffold(         appBar: AppBar(           title: Text("GetX Title"),         ),         body: Center(           child: Column(             mainAxisAlignment: MainAxisAlignment.center,             crossAxisAlignment: CrossAxisAlignment.center,             children: [               ElevatedButton(                 onPressed: () {                   Get.snackbar("Snackbar 标题", "欢迎使用Snackbar");                 },                 child: Text("显示 Snackbar"))             ],           ),         ),       ),     );   } }`

#### 效果展示

如果您运行了代码，那么恭喜你，你已经会用`GetX` 来展示`snackbar` 了。你将得到下面的结果：

![[622870be8da14a284fc89ebc314dd949_MD5.gif]](https://gz-ljm-blog.oss-cn-guangzhou.aliyuncs.com/blog/getx_snackbar.gif)

### Snackbar属性和说明

> 总共38个属性，

字段

属性

描述

title

String

弹出的标题文字

message

String

弹出的消息文字

colorText

Color

title和message的文字颜色

duration

Duration

Snackbar弹出的持续时间（默认3秒）

instantInit

bool

当false可以把snackbar 放在initState，默认true

snackPosition

SnackPosition

弹出时的位置，有两个选项【TOP，BOTTOM】默认TOP

titleText

Widget

弹出标题的组件，设置该属性会导致title属性失效

messageText

Widget

弹出消息的组件，设置该属性会导致messageText属性失效

icon

Widget

弹出时图标，显示在title和message的左侧

shouldIconPulse

bool

弹出时图标是否闪烁，默认false

maxWidth

double

Snackbar最大的宽度

margin

EdgeInsets

Snackbar外边距，默认zero

padding

EdgeInsets

Snackbar内边距，默认EdgeInsets.all(16)

borderRadius

double

边框圆角大小，默认15

borderColor

Color

边框的颜色，必须设置borderWidth，否则无效果

borderWidth

double

边框的线条宽度

backgroundColor

Color

Snackbar背景颜色，默认Colors.grey.withOpacity(0.2)

leftBarIndicatorColor

Color

左侧指示器的颜色

boxShadows

List

Snackbar阴影颜色

backgroundGradient

Gradient

背景的线性颜色

mainButton

TextButton

主要按钮，一般显示发送、确认按钮

onTap

OnTap

点击Snackbar事件回调

isDismissible

bool

是否开启Snackbar手势关闭，可配合dismissDirection使用

showProgressIndicator

bool

是否显示进度条指示器，默认false

dismissDirection

SnackDismissDirection

Snackbar关闭的方向

progressIndicatorController

AnimationController

进度条指示器的动画控制器

progressIndicatorBackgroundColor

Color

进度条指示器的背景颜色

progressIndicatorValueColor

Animation

进度条指示器的背景颜色，Animation

snackStyle

SnackStyle

Snackbar是否会附加到屏幕边缘

forwardAnimationCurve

Curve

Snackbar弹出的动画，默认Curves.easeOutCirc

reverseAnimationCurve

Curve

Snackbar消失的动画，默认Curves.easeOutCirc

animationDuration

Duration

Snackbar弹出和小时的动画时长，默认1秒

barBlur

double

Snackbar背景的模糊度

overlayBlur

double

弹出时的毛玻璃效果值，默认0

snackbarStatus

SnackbarStatusCallback

Snackbar弹出或消失时的事件回调（即将打开、已打开、即将关闭、已关闭）

overlayColor

Color

弹出时的毛玻璃的背景颜色

userInputForm

Form

用户输入表单

## Dialog介绍

`Dialog` 底层其实是对`AlertDialog`进行了封装， 一般用于二次确认的弹出框，比如当点击某个按钮提交资料时，需要用户二次确认，以防止误操作。

### Dialog使用

#### 第一步：应用程序入口设置

当我们导入依赖后，在应用程序顶层把`GetMaterialApp` 作为顶层，如下所示

`import 'package:flutter/material.dart'; import 'package:flutter_getx_example/DialogExample/DialogExample.dart'; import 'package:get/get.dart';  void main() {   runApp(MyApp()); }  class MyApp extends StatelessWidget {   @override   Widget build(BuildContext context) {     return GetMaterialApp(       title: "GetX",       home: DialogExample(),     );   } }`

#### 第二步：调用Dialog

我们可以通过`Get.defaultDialog()` 来显示 `dialog` ，如下所示

`import 'package:flutter/material.dart'; import 'package:get/get.dart';  class DialogExample extends StatelessWidget {   @override   Widget build(BuildContext context) {     return Scaffold(       appBar: AppBar(         title: Text("GetX Title"),       ),       body: Center(         child: Column(           mainAxisAlignment: MainAxisAlignment.center,           crossAxisAlignment: CrossAxisAlignment.center,           children: [             ElevatedButton(               onPressed: () {                 Get.defaultDialog();               },               child: Text("显示 Dialog"))           ],         ),       ),     );   } }`

#### 效果展示

如果您运行了代码，那么恭喜你，你已经会用`GetX` 来展示`dialog` 了。你将得到下面的结果：

[![[7bfc8045b601817055b6b263027d54fa_MD5.gif]]](https://gz-ljm-blog.oss-cn-guangzhou.aliyuncs.com/blog-video/get_dialog.gif)

### Dialog属性和说明

> 总共25个属性

字段

属性

描述

title

String

弹出的标题，默认（Alert)

titlePadding

EdgeInsetsGeometry

标题的内边距，默认（EdgeInsets.all(8)）

titleStyle

TextStyle

标题的样式

middleText

String

中间内容区域显示的文字

middleTextStyle

TextStyle

中间内容区域显示的文字样式

content

Widget

弹出的内容，该值设置后middleText将无效

contentPadding

EdgeInsetsGeometry

内容的内边距，默认（EdgeInsets.all(8)）

onConfirm

VoidCallback

确认按钮回调

onCancel

VoidCallback

取消按钮回调

onCustom

VoidCallback

自定义按钮回调

cancelTextColor

Color

取消按钮文字的颜色

confirmTextColor

Color

确认按钮文字的颜色

textConfirm

String

确认按钮的文字

textCancel

String

取消按钮的文字

textCustom

String

自定义按钮的文字

confirm

Widget

确认按钮的组件

cancel

Widget

取消按钮的组件

custom

Widget

自定义按钮的组件

backgroundColor

Color

弹出框的背景颜色

barrierDismissible

bool

是否可以通过点击背景关闭弹窗

buttonColor

Color

按钮的文字颜色，根据按钮类型来设定不同的位置

radius

double

弹出框的圆角大小，默认20

actions

List

增加额外的子组件

onWillPop

WillPopCallback

拦截关闭之前做一些操作

navigatorKey

GlobalKey

用于打开对话框的key

## BottomSheet介绍

`BottomSheet` 是底部弹出的一个组件，常用于单选、验证码二次校验弹窗等，`GetX`的`BottomSheet`底部弹出是自定义通过路由push的方法实现底部弹窗的一个效果。

### BottomSheet使用

我们可以通过`GetX`很轻松的调用`bottomSheet()`，而且无需传入`context`，下面我给出一个例子，使用`GetX`弹出`bottomSheet`并很轻松的实现切换主题

#### 第一步：应用程序入口设置

当我们导入依赖后，在应用程序顶层把`GetMaterialApp` 作为顶层，如下所示

`import 'package:flutter/material.dart'; import 'package:flutter_getx_example/DialogExample/DialogExample.dart'; import 'package:get/get.dart';  void main() {   runApp(MyApp()); }  class MyApp extends StatelessWidget {   @override   Widget build(BuildContext context) {     return GetMaterialApp(       title: "GetX",       home: DialogExample(),     );   } }`

#### 第二步：调用BottomSheet

我们可以通过`Get.bottomSheet()` 来显示 `BottomSheet` ，如下所示

`import 'package:flutter/material.dart'; import 'package:get/get.dart';  class BottomSheetExample extends StatelessWidget {   GlobalKey<NavigatorState> _navKey = GlobalKey();    @override   Widget build(BuildContext context) {     return Scaffold(       appBar: AppBar(         title: Text("GetX Title"),       ),       body: Center(         child: Column(           mainAxisAlignment: MainAxisAlignment.center,           crossAxisAlignment: CrossAxisAlignment.center,           children: [             ElevatedButton(onPressed: () {               Get.bottomSheet(                 Container(                   child: Wrap(                     children: [                       ListTile(                         leading: Icon(Icons.wb_sunny_outlined),                         title: Text("白天模式"),                         onTap: () {                           Get.changeTheme(ThemeData.light());                         },                       ),                       ListTile(                         leading: Icon(Icons.wb_sunny),                         title: Text("黑夜模式"),                         onTap: () {                           Get.changeTheme(ThemeData.dark());                         },                       )                     ],                   ),                 )               );             }, child: Text("Bottom Sheet"))           ],         ),       ),     );   } }`

#### 效果展示

如果您运行了代码，那么恭喜你，你已经会用`GetX` 来展示`BottomSheet` 了。你将得到下面的结果：

[![[35554e588225e8ba3aafb7808a8e184b_MD5.gif]]](https://gz-ljm-blog.oss-cn-guangzhou.aliyuncs.com/blog-video/get_bottomsheet1.gif)

### BottomSheet属性和说明

字段

属性

描述

bottomsheet

Widget

弹出的Widget组件

backgroundColor

Color

bottomsheet的背景颜色

elevation

double

bottomsheet的阴影

persistent

bool

是否添加到路由中

shape

ShapeBorder

边框形状，一般用于圆角效果

clipBehavior

Clip

裁剪的方式

barrierColor

Color

弹出层的背景颜色

ignoreSafeArea

bool

是否忽略安全适配

isScrollControlled

bool

是否支持全屏弹出，默认false

useRootNavigator

bool

是否使用根导航

isDismissible

bool

点击背景是否可关闭，默认ture

enableDrag

bool

是否可以拖动关闭，默认true

settings

RouteSettings

路由设置

enterBottomSheetDuration

Duration

bottomsheet进入时的动画时间

exitBottomSheetDuration

Duration

bottomsheet退出时的动画时间

## Navigation路由跳转

使用`GetX` 进行路由跳转非常的简单，只需要调用`Get.to()`即可进行路由跳转，而系统的路由跳转需要写八行代码，这是不能忍受的事情，而且涉及到**跳转动画设置** 、**动画时长定义**、**动画曲线** 等设置那就更加的复杂，而`GetX`为我们封装了`Navigation`，无需`context`可进行跳转，并且能很方便的使用跳转动画等。

### Navigation—通过to方法进行路由跳转

#### 第一步：应用程序入口设置

`1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17  import 'package:flutter/material.dart'; import 'package:flutter_getx_example/NavigationForNamedExample/NavigationForNamedExample.dart'; import 'package:get/get.dart';  void main() {   runApp(MyApp()); }  class MyApp extends StatelessWidget {   @override   Widget build(BuildContext context) {     return GetMaterialApp(       title: "GetX",       home: NavigationForNamedExample(),     );   } }`

#### 第二步：调用to方法

`1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20 21 22 23 24 25 26 27 28 29  import 'package:flutter/material.dart'; import 'package:flutter_getx_example/NavigationExample/home.dart'; import 'package:get/get.dart';  class NavigationExample extends StatelessWidget {   GlobalKey<NavigatorState> _navKey = GlobalKey();    @override   Widget build(BuildContext context) {     return Scaffold(       appBar: AppBar(         title: Text("GetX Navigation"),       ),       body: Center(         child: Column(           mainAxisAlignment: MainAxisAlignment.center,           crossAxisAlignment: CrossAxisAlignment.center,           children: [             ElevatedButton(               onPressed: () async {                 Get.to(Home());               },               child: Text("跳转到首页"))           ],         ),       ),     );   } }`

#### 效果展示

[![[8f977c276b68f4e4e844a6e234d0f33c_MD5.gif]]](https://gz-ljm-blog.oss-cn-guangzhou.aliyuncs.com/blog-video/getx_navigation_to.gif)

### Navigation—通过toNamed进行路由跳转

#### 第一步：应用程序入口设置

`1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20 21 22 23 24  import 'package:flutter/material.dart'; import 'package:flutter_getx_example/NavigationForNamedExample/NavigationForNamedExample.dart'; import 'package:get/get.dart';  void main() {   runApp(MyApp()); }  class MyApp extends StatelessWidget {   @override   Widget build(BuildContext context) {     return GetMaterialApp(       title: "GetX",       initialRoute: "/",       defaultTransition: Transition.zoom,       getPages: [         GetPage(name: "/", page: () => MyApp()),         GetPage(name: "/home", page: () => Home()),         GetPage(name: "/my", page: () => My(), transition: Transition.rightToLeft)       ],       home: NavigationForNamedExample(),     );   } }`

#### 第二步：调用toNamed

`1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20 21 22 23 24 25 26 27 28  import 'package:flutter/material.dart'; import 'package:get/get.dart';  class NavigationForNamedExample extends StatelessWidget {   GlobalKey<NavigatorState> _navKey = GlobalKey();    @override   Widget build(BuildContext context) {     return Scaffold(       appBar: AppBar(         title: Text("GetX NavigationForNamed"),       ),       body: Center(         child: Column(           mainAxisAlignment: MainAxisAlignment.center,           crossAxisAlignment: CrossAxisAlignment.center,           children: [             ElevatedButton(               onPressed: () async {                 Get.toNamed("/my");               },               child: Text("跳转到首页"))           ],         ),       ),     );   } }`

#### 效果展示

[![[8f977c276b68f4e4e844a6e234d0f33c_MD5.gif]]](https://gz-ljm-blog.oss-cn-guangzhou.aliyuncs.com/blog-video/getx_navigation_to.gif)

## Obx响应式状态管理

### 介绍

> 响应式编程可能会让很多人感到陌生，因为它很复杂，但是GetX将响应式编程变得非常简单。
> 
> -   你不需要创建StreamControllers.
> -   你不需要为每个变量创建一个StreamBuilder。
> -   你不需要为每个状态创建一个类。
> -   你不需要为一个初始值创建一个get。
> 
> 使用 Get 的响应式编程就像使用 setState 一样简单。

### 定义Obx变量的三种方式

`1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20 21 22 23 24 25 26 27 28 29  // 第一种 使用 Rx{Type} // final name = RxString(''); // final isLogged = RxBool(false); // final count = RxInt(0); // final balance = RxDouble(0.0); // final items = RxList<String>([]); // final myMap = RxMap<String, int>({});  // 第二种是使用 Rx，规定泛型 Rx<Type>。 // final name = Rx<String>(''); // final isLogged = Rx<Bool>(false); // final count = Rx<Int>(0); // final balance = Rx<Double>(0.0); // final number = Rx<Num>(0) // final items = Rx<List<String>>([]); // final myMap = Rx<Map<String, int>>({}); // 自定义类 - 可以是任何类 // final user = Rx<User>();  // 第三种更实用、更简单、更可取的方法，只需添加 .obs 作为value的属性。 // final name = ''.obs; // final isLogged = false.obs; // final count = 0.obs; // final balance = 0.0.obs; // final number = 0.obs; // final items = <String>[].obs; // final myMap = <String, int>{}.obs; // 自定义类 - 可以是任何类 // final user = User().obs;`

### 计数器案例

#### 第一步：应用程序入口设置

`1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17  import 'package:flutter/material.dart'; import 'package:flutter_getx_example/ObxCountExample/ObxCountExample.dart'; import 'package:get/get.dart';  void main() {   runApp(MyApp()); }  class MyApp extends StatelessWidget {   @override   Widget build(BuildContext context) {     return GetMaterialApp(       title: "GetX",       home: ObxCountExample(),     );   } }`

#### 第二步：声明Rx变量以及改变计数器的方法

`1 2 3 4 5 6 7  RxInt count = RxInt(0); // var count = Rx<double>(0); // var count = 0.obs;  void increment() {   count++; }`

#### 第三步：使用Obx监听值的改变

`1 2 3 4  Obx(() => Text(   "count的值为: $count",   style: TextStyle(color: Colors.red, fontSize: 30), )),`

#### 完整代码

`1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20 21 22 23 24 25 26 27 28 29 30 31 32 33 34 35 36 37 38 39 40 41  import 'dart:ffi';  import 'package:flutter/material.dart'; import 'package:get/get.dart';  class ObxCountExample extends StatelessWidget {   RxInt count = RxInt(0);   // var count = Rx<double>(0);   // var count = 0.obs;    void increment() {     count++;   }    @override   Widget build(BuildContext context) {     return Scaffold(       appBar: AppBar(         title: Text("GetX Obx---计数器"),       ),       body: Center(         child: Column(           mainAxisAlignment: MainAxisAlignment.center,           crossAxisAlignment: CrossAxisAlignment.center,           children: [             Obx(() => Text(               "count的值为: $count",               style: TextStyle(color: Colors.red, fontSize: 30),             )),             SizedBox(height: 20,),             ElevatedButton(               onPressed: () {                 increment();               },               child: Text("点我加1"))           ],         ),       ),     );   } }`

#### 效果展示

[![[c3173bf2e032fffa1e0c3a093fbcfe51_MD5.gif]]](https://gz-ljm-blog.oss-cn-guangzhou.aliyuncs.com/blog-video/obx---count.gif)

### 自定义类案例

#### 第一步：应用程序入口设置

`1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17  import 'package:flutter/material.dart'; import 'package:flutter_getx_example/ObxCustomClassExample/ObxCustomClassExample.dart'; import 'package:get/get.dart';  void main() {   runApp(MyApp()); }  class MyApp extends StatelessWidget {   @override   Widget build(BuildContext context) {     return GetMaterialApp(       title: "GetX",       home: ObxCustomClassExample(),     );   } }`

#### 第二步：创建Teacher类

`1 2 3 4 5 6 7 8 9 10 11 12 13  import 'package:get/get.dart';  class Teacher {    // rx 变量   var name = "Jimi".obs;   var age = 18.obs;    // 构造函数创建   // var name;   // var age;   // Teacher({this.name, this.age}); }`

#### 第三步：监听Teacher状态改变

`1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20 21 22 23 24 25 26 27 28 29 30 31 32 33 34 35 36 37 38 39 40 41 42  import 'package:flutter/material.dart'; import 'package:flutter_getx_example/ObxCustomClassExample/Teacher.dart'; import 'package:get/get.dart';  class ObxCustomClassExample extends StatelessWidget {    var teacher = Teacher();    // final teacher = Teacher(name: "Jimi", age: 18).obs;    @override   Widget build(BuildContext context) {     return Scaffold(       appBar: AppBar(         title: Text("GetX Obx---自定义类"),       ),       body: Center(         child: Column(           mainAxisAlignment: MainAxisAlignment.center,           crossAxisAlignment: CrossAxisAlignment.center,           children: [             Obx(() => Text(               "我的名字是 ${teacher.name.value}",               style: TextStyle(color: Colors.red, fontSize: 30),             )),             SizedBox(height: 20,),             ElevatedButton(               onPressed: () {                 teacher.name.value = teacher.name.value.toUpperCase();                  // teacher.update((val) {                 //   teacher.value.name = teacher.value.name.toString().toUpperCase();                 // });               },               child: Text("转换为大写"))           ],         ),       ),     );   } }`

#### 效果展示

[![[827d76f763097cd95d718e2cdfb7b5a4_MD5.gif]]](https://gz-ljm-blog.oss-cn-guangzhou.aliyuncs.com/blog-video/getx_obx_customclass.gif)

## 总结

通过这个章节知道了`GetX`是一个高性能的状态管理、智能的依赖注入和便捷的路由管理，我们介绍了什么`GetX`以及如果使用，包括`Snackbar`、`Dialog`、`BottomSheet`、`Navigation`、`Obx` 等进行了一个简单的介绍，使用`GetX`后能极简的缩减我们的代码。