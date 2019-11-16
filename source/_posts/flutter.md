---
title: flutter
date: 2019-10-01 16:24:22
tags:
---

# flutter开发起步
## 核心
- 底层渲染逻辑skia
  - 利用自己的渲染引擎skia，不依赖OS的组件
  - 保证了高度一致性
- 上层开发语言dart
  - Dart同时支持JIT/AOT
  - 开发期调试效率高，发布期执行性能好
## 特性
- 跨平台
- 高保真(自己的渲染引擎skia完成了渲染的闭环，不用js扩展调用原生)
- 高性能

## 分层架构
- embedder
  - 操作系统适配层
  - 实现了渲染设置，线程设置，平台插件适配等
- engine
  - skia和text提供了调用底层渲染和排版的能力
  - dart提供了运行时调用dart和渲染引擎的能力
- framework
  - dart实现的UI SDK包含动画、图形绘制、手势识别等
{% asset_img flutterArch.png flutter %}

## 工作流程
- Widget以树的形式组织成控件树
- flutter通过每个控件创建不同类型的渲染对象组成渲染对象树
- 渲染对象树通过4个阶段完成展示
  - 布局 采用深度优先机制遍历渲染对象树，决定对象的位置和尺寸(支持布局边界和父约束)
  - 绘制 按位置和尺寸绘制到不同的图层，采用深度优先机制遍历(支持重绘边界)
  - 合成 将所有图层根据位置，尺寸，层级，大小，透明度等规则计算出最终显示效果简化渲染树
  - 渲染 几何图层数据交给skia引擎加工成二维图像数据最终给GPU进行渲染
{% asset_img flutter.jpg flutter技术点 %}

## 跨平台方案
- 三个时代
  - web容器时代(cordova,ionic,微信小程序都使用webview+jsBridge)
  - 泛web容器时代(reactNative,weex,快应用都把渲染交给原生)
  - 自绘引擎时代(flutter,客户端只提供一块画布即可)
{% asset_img hybrid.png 技术选型对比 %}

- 图像显示基本原理
  - CPU负责图像数据的计算
  - GPU负责图像数据的渲染，渲染后放入帧缓冲区，视频控制器根据垂直同步信号VSync以60t/s刷新给显示器
  - 显示器负责图像显示
- skia
  - c++开发的高性能2D图像绘制引擎

## 开发环境
- android
  - AS
  - AVD(Nexus6P)
- ios
  - Xcode
  - open -a Simulator
- flutter
  - export PUB_HOSTED_URL=https://pub.flutter-io.cn
  - export FLUTTER_STORAGE_BASE_URL=https://storage.flutter-io.cn
  - export FLUTTER_HOME=/Users/dh/dev/flutter
  - export PATH=$PATH:$FLUTTER_HOME/bin
  - flutter emulators [--launch apple_ios_simulator]
  - flutter doctor
- doctor问题解决方案
  - ios
    - gem sources --add https://gems.ruby-china.com/ --remove https://rubygems.org/
    - gem sources -l
    - gem update --system
    - sudo gem install cocoapods
    - pod setup
    - brew update
    - brew install --HEAD usbmuxd #usb通信抽象为tcp通信，与设备进行多路socket守护进程
    - brew link usbmuxd
    - brew install --HEAD libimobiledevice # 与设备进行通信的跨平台协议库
    - brew install ideviceinstaller # 在ios设备上管理app的工具
    - signing
  - android
    - flutter doctor --android-licenses
    - flutter plugin
  - vscode
    - flutter extension

## 工程结构
- android 安卓子工程
- build 构建产物
- ios ios子工程
- lib/main.dart flutter工程入口文件
- test 测试目录
- flutter_app_demo.iml 工程配置文件
- pubspec.lock 记录当前项目实际依赖信息的文件
- pubspec.yaml 管理第三方库和资源的配置文件

# Dart基础
## 特性
- 同时支持JIT/AOT编译模式
  - JIT just in time即时编译，适合开发环境
  - AOT ahead of time运行前编译，适合生产环境
- 内存分配和垃圾回收机制
  - 内存线性增长，创建对象时只在堆上移动指针
  - 并发是通过Isolate实现，使dart实现了无锁的内存分配
  - 避免了抢占式调度和共享内存
  - 垃圾回收采用多生代算法，只操作少量的活跃对象，忽略死亡对象
- 单线程模型
  - dart中没有线程，只有Isolate隔离区，isolate不共享内存
  - 通过事件循环在事件队列上传递消息
- 无需单独的声明式布局语言

## 语言基础
- dart概述
  - 是类型安全的语言，不会隐式转换类型
  - 所有的类型都是对象类型，都继承自顶层的Object
- main()作为程序的入口 
- 变量与类型
  - var声明变量表示变量类型自动推断
  - 未初始化的变量的值都是null
- 基本类型
  - String
    - UTF-16的字符串组成
    - 支持单引号、双引号
    - 支持${expression}或$var
    - 三个单引号或双引号来表示多行字符串
  - num
    - 64位int，代表整数类型
    - 64位double，代表浮点类型
  - bool
    - true
    - false
  - List
    - 类型约束List<int>/ list = <int>[]
    - 类型判断 list is List<int>
  - Map
    - 类型约束Map<String,String>/ map = <String,String>{}
    - 类型判断 map is Map<String,String>
- 常量
  - const 表示在编译期间即能确定的值
  - final 表示在运行时确定的值，一旦确定后就不可改变
- 函数
  - 类型Function
  - 支持箭头函数
  - 函数的重载即提供同名但参数不同的函数，dart认为会导致混乱所以不支持
  - 但dart支持更高效的可选命名参数{}和可选参数[],也支持参数默认值
- 类
  - 实例变量/方法
  - 类变量/方法 static关键字
  - 变量/方法名称前面加上_即可作为private使用，否则就是public，_是库访问级别
  - 构造函数语法糖就是类中调用同类名相同的方法
  - 命名构造函数即类可以有多个构造函数
  - 实例化时可以省略new关键字
  - dart支持初始化列表，即支持多个构造函数，构造函数可以重定向:到另一个构造函数
- 复用
  - 继承父类 extends
  - 接口实现 implements
  - 混入 with 解决dart缺少对多重继承的支持问题还能避免多重继承导致的菱形歧义
- 运算符(用于简化处理变量实例缺失即null的情况)
  - ?. p?.printInfo()表示p为null时跳过，不为null时再调用，避免抛错
  - ??= a??=value 如果a为null则赋值value，否则跳过
  - ?? a ?? b 如果a不为null则返回a，否则返回b
  - dart提供了类似C++的运算符覆写机制，使用户可以自定义运算符，使用operator关键字和运算符一起使用来表示类成员运算符函数

# flutter基础
## widget
- 描述
  - 是flutter功能的抽象描述
  - 是视图的配置信息
  - 是数据的映射
  - 一切皆widget(view/viewController/Activity/Application/Layout等)
  - 由父到子、自顶向下方式构建，父widget控制子widget的样式
  - widget是不可变的，更新则意味着销毁和重建
- 分类
  - statelessWidget Model在build后不变，父widget通过初始化参数完全控制UI
  - statefulWidget build后还要关心和响应数据变化来进行重绘，setState就会触发
- 渲染过程
  - widget树 对视图的一种结构化描述数据，widget是不可变的需要很多的重建
  - element树 是widget的一个实例化对象，element.renderObject承载了视图构建的上下文数据，element是可变的，把真正需要修改的部分同步到renderObject中
  - renderObject树 主要负责视图渲染的对象
    - 布局 在renderObject中完成，采用深度优先机制遍历，确定位置和尺寸
    - 绘制 在renderObject中完成，采用深度优先机制遍历，确定图层
    - 合成 交给skia引擎
    - 渲染 交给skia引擎，在VSync信号同步时直接从渲染树合成Bitmap，交给GPU

## 生命周期
- app的生命周期
  - WidgetsBindingObserver类实现钩子，此类中有很多函数
  - didChangeAppLifecycleState
    - resumed 可见的，可以响应用户交互
    - inactive 非活动状态，不能响应用户交互(上下两个状态切换时的中间状态)
    - paused 不可见并不能响应用户交互但后台继续活动
  - addPostFrameCallback
    - 单次frame绘制回调，只会回调1次
  - addPersistentFrameCallback
    - 实时frame绘制回调，每次frame绘制完成后都回调
    - 适合做FPS监测
- 视图的生命周期，通过state体现
  - 创建
    - 构造方法，调用createState创建state，构造方法决定了最初呈现效果在state生命周期只会被调用1次
    - initState，在state生命周期只会被调用1次
    - didChangeDependencies
    - build，构建视图返回一个widget
  - 更新
    - setState
    - didChangeDependencies
    - didUpdateWidget
  - 销毁
    - deactivate
    - dispose
{% asset_img lifecycle1.png 生命周期1 %}
{% asset_img lifecycle2.png 生命周期2 %}

## 常用widget
- Text
  - Text
  - Text.rich
- TextSpan
- Image
  - Image.asset("images/logo.png")加载本地资源图片
  - Image.file(new File("/path/to/file"))本地图片
  - Image.network("http://xxx/xx.xx")加载网络图片
- FadeInImage 提供了图片占位，加载动画效果
- CachedNetworkImage 还可以把图片缓存到fs中，更强大
- FloatingActionButton 圆形按钮
- FlatButton 扁平化的按钮，默认背景透明，有交互效果
- RaisedButton 凸起的按钮，默认带有灰色背景，有交互效果
- ListView
  - ListView 适用于少量子元素，需要提前创建子widget，性能较差
  - ListView.builder 适用于子widget比较多的情况懒加载widget
  - ListView.separated 可设置分割线样式
- CustomScrollView
  - 处理多个需要自定义滚动效果的widget
  - 彼此独立的可滚动的widget统称为sliver
- ScrollController 滚动信息的监听
- ScrollNotification 获取滚动事件的通知，将ListView纳入子widget
- 布局类widget
  - 单子widget
    - Container
    - Padding
    - Center
  - 多子widget
    - Row
    - Column
    - Expanded 处理容器的剩余空间
  - 层叠widget
    - Stack 提供了层叠布局的容器
    - Positioned 提供了设置子widget位置的能力
- 自定义widget
  - 组合
  - 自绘
    - CustomPaint是用以承接自绘控件的容器，并不负责真正的绘制
    - 绘制使用画布Canvas和画笔Paint及绘制逻辑CustomPainter控制

## 主题定制
- 主题一般包括颜色、图片、字体、字号等资源和配置
- 实现
  - ios中通常将配置信息预写到plist中通过单例来控制
  - android中通常将配置信息写到style属性值的xml中，通过activity的setTheme切换
  - 前端通过切换css即可实现
- Theme(data:ThemeData(),child:MyWidget())
- Theme(data:Theme.of(context).copyWith(),child:MyWidget())
- flutter使用ThemeData统一管理
  - app全局范围 MaterialApp.theme
  - widget局部范围 Theme.data
    - 不想继承任何全局配置可直接新建一个ThemeData实例
    - 想继承可使用Theme.of(context).copyWith()
  - Theme.of(context)向上查找widget树，返回最近的Theme
  - defaultTargetPlatform 可判断当前运行的平台

## 依赖管理
- 原生
  - ios使用Images.xcassets管理图片，其他资源直接拖进项目即可
  - android使用drawable+分辨率命名的文件夹管理图片，res/layout放布局，res/values放资源描述文件，assets放原始文件
- flutter
  - assets可管理任意类型的资源
  - pubspec.yaml中配置指向对应目录即可
    - 可以单独指向某个文件
    - 也可以指向一个目录，目录制定并不会递归，所以需显示递归指定子目录
  - 资源引用
    - Image.asset()引用图片
      - 遵循基于像素密度的管理方式1.0x、2.0x、3.0x
      - flutter根据设备分辨率加载比例最接近的图片资源实现自动降级
      - 资源目录应该将1.0x、2.0x、3.0x图片资源分开管理
      - assets/bg.jpg
      - assets/2.0x/bg.jpg
      - assets/3.0x/bg.jpg
      - pubspec.yaml中仅声明1.0x资源即可 flutter.assets: assets/bg.jpg
    - rootBundle.loadString()加载字符串文件资源
    - rootBundle.load()加载二进制文件资源
    - fonts:[{family: name,fonts:[{asset:assets/fonts/name.ttf},{asset:assets/fonts/name-Italic.ttf,style:italic},{asset:assets/fonts/name-Bold.ttf,weight:700}]}]
- flutter框架前的原生配置
  - 更换app图标
    - ios ios/Runner/Assets.xcassets/AppIcon.appiconset
    - android android/app/src/main/res/mipmap
  - 更换app启动图
    - ios ios/Runner/Assets.xcassets/LaunchImage.imageset
    - android android/app/src/main/res/drawable/launch_background.xml
- 第三方组件库
  - pubspec.yaml
    - 类似于ios中的Podfile/android中的build.gradle/前端的package.json
    - pub
      - dart的包管理工具，类似于ios中的cocoaPods/android中的maven/前端npm
      - dart的包实际上就是一个包含了pubspec.yaml的目录
  - 推荐包使用区间进行版本的管理，dart/flutter的sdk运行环境使用固定版本号
  - 包依赖可以直接获取pub资源，也可以使用本地路径或git地址
  - 所有依赖确定并下载完毕后会生成.packages文件记录包映射信息，此文件需要忽略
  - 最后pub会生成pubspec.lock文件记录包的来源和版本号，此文件需要git

## 用户交互事件
- 原始的指针事件(PointerEvent)
  - 原生常见的触摸事件
  - 事件会从最内层冒泡到根节点，无法取消冒泡或停止分发，只能hitTestBehavior
  - ListenerWidget可以监听子widget的原始指针事件
- 手势识别(GestureDetector)
  - 多个原始指针事件的组合操作，是指针事件的语义化封装
  - GestureDetector可以处理各种高级触摸行为，可监听多个手势但最终只有一个生效
  - 不同手势通过flutter内部的arena进行PK，最终决定是什么手势
  - 父组件也需要处理子组件手势时需要RawGestureDetector和GestureFactory自定义

## 组件通信
- 组件通信标准方式是通过属性传值
- 跨组件通信三种方案
  - InheritedWidget
  - Notification
  - EventBus
- InheritedWidget
  - 从上到下传递
  - Theme是典型案例，父子建立观察者关系，上层属性修改后，子也会更新，默认只读
- Notification
  - 从下到上传递
  - NotificationListener进行监听
- EventBus
  - 上面两种依赖父子widget关系树
  - eventBus遵循了发布/订阅模式
  - 全局作用，但容易引起冲突，组件移除要清理事件

## 路由与导航
- Route 
  - 是页面的抽象，主要负责接收参数，创建页面，响应navigator的打开和关闭
- Navigator
  - 维护一个路由栈管理Route，可打开/关闭/替换
- 路由管理
  - 基本路由
    - 无需提前注册，页面切换时需要自己构造页面实例
    - Navigator.push(context,MaterialPageRouter(builder:()=>{}))
  - 命名路由
    - 需要提前注册，页面切换时根据注册的标示符打开
    - Navigator.pushNamed(context,"name")
    - Navigator.of(context).pushNamed("name",arguments:"args");
- 其他
  - 默认路由 UnknownRoute
  - 路由页面打开参数
    - RouteSettings 
    - args = ModalRoute.of(context).settings.arguments as String;
  - 路由页面返回参数
    - push页面时要设置目标页面关闭时的监听函数以获取返回参数Navigator.pushNamed(context,"name").then(args => print args);
    - 目标页面关闭路由时要传递相关返回参数 Navigator.pop(context,"args");

# flutter进阶
## 动画
- Animation
  - flutter动画库的核心类
  - flutter的动画状态和渲染是分离的
  - fultter动画的生成器
- AnimationController
  - flutter动画的控制器
  - 管理Animation，可设置动画的时长、启动动画、暂停、反转等
  - controller.forward()启动动画
  - controller.repeat()重复动画
  - controller.dispose()释放资源
- Listener
  - flutter动画的监听器
  - 是Animation的回调函数，可监听动画的进度变化
  - 监听变化后重新触发刷新实现动画
  - 实践中尽量避免直接使用提高性能
- AnimatedWidget
  - 将Animation状态与其子widget视觉样式绑定，省去了状态监听和刷新UI
  - 使用时要继承此widget并接收Animation对象作为初始化参数,build方法中读值初始化
- AnimatedBuilder
  - 自动监听Animation对象变化根据需要刷新UI
  - 尺寸变化由builder(context,child)函数管理
  - 此类实现了动画与渲染的职责分离
- 跨页面共享的动画
  - 共享元素变换 SharedElementTransition
  - Hero(tag:"name",child:Widget)

## 单线程模型
- EventLoop机制
  - MicrotaskQueue微任务队列
    - 表示短时间就会完成的异步任务
    - 微任务在事件循环中的优先级最高，只要有任务就一直霸占着事件循环
    - scheduleMicroTask()
    - 一般不需要此队列
  - EventQueue事件队列
    - 优先级比较低，比较常用
    - dart为EventQueue任务提供了Future封装
    - Future提供了链式调用能力，可在异步完成后依次执行
    - then与Future函数体共用一个事件循环
    - Future执行完毕了又给引用添加了一个then方法或Future函数体为null时会把then方法体放入微任务队列，尽快执行
{% asset_img eventLoop.png 事件循环 %}
{% asset_img eventLoop.gif 事件循环 %}
- 异步处理
  - async/await
- 并发编程
  - dart是基于单线程模型，但也提供了基于Isolate的多线程机制提高多核cpu利用率
  - Isolate都有自己的EventLoop和Queue，资源隔离做的很好
  - Isolate之间不共享任何资源，只能依靠消息机制SendPort通信，避免了资源抢占需要加锁的问题
  - Isolate.spawn(fnName,args)
  - 主/并发Isolate之间通信靠ReceivePort.listen()和sendPort.send()
  - 为了方便使用抽象了支持并发计算的compute函数

## 网络编程
- 网络请求流程
  - 构造client，设置通用请求行为（超时）
  - 构造URI，设置请求header/body
  - 发起请求，等待响应
  - 解析响应的内容
- flutter实现方式
  - dart:io里的HttpClient
  - dart原生的http请求库
  - 第三方库dio(支持拦截器/请求合并等高级能力)
- JSON解析
  - flutter不支持运行时反射，所以不能自动解析JSON只能手动解析
  - dart:convert库中内置的JSON解码器把JSON字符串解析成对象
  - 字符串传给JSON.decode()返回一个Map再传给自定义解析类
  - 如果JSON数据格式比较复杂或量比较大推荐使用compute函数将解析放到新Isolate完成

## 持久化存储
- 文件
  - 目录
    - 临时目录Temporary(ios的NSTemporaryDirectory和andriod的getCacheDir)
    - 文档目录Documents(ios的NSDocumentDirectory和andriod的AppData目录)
  - 编码
    - 非常耗时需要在异步环境中编码
    - 防止异常需要try/catch
- SharedPreferences
  - 以原生方式为简单的键值对数据提供存储（string/int/double/bool）
  - ios中使用NSUserDefaults
  - android中使用SharedPreferences
- 数据库sqlite
  - 设定数据库存储地址时使用join方法自动处理路径分隔符
  - 创建数据库时传入的version与onCreate方法的回调中的version一致
  - 在app版本升级过程中需要对数据库存储字段改动需要onUpgrade方法处理
  - 数据库只会创建1次即onCreate在app卸载前只会执行1次

## dart层兼容原生
- 方法通道MethodChannel 解决原生能力复用问题
  - 基于方法通道(MethodChannel)机制可以把原生代码接口形式暴露给Dart
  - 构造方法通道时需要指定一个唯一的字符串标识符，然后在这个通道上发起方法调用
  - const mc = MethodChannel('com.ceair/utils');
  - try { await mc.invokeMethod('methodName') } catch(e){}
  - 方法通道调用过程中涉及的跨平台数据格式flutter会使用StandardMessageCodec进行序列化
  - 方法通道是非线程安全的，所以原生和flutter都需要在主线程操作否则可能会有奇怪的bug
- 平台视图PlatformView 解决原生视图复用问题
  - 允许flutter里嵌入原生的视图并加入flutter渲染树中实现混合视图
  - flutter通过原生视图的封装类(UIKitView和AndroidView)传入视图标识符发起请求
  - 原生代码交给PlatformViewFactory实现
  - 原生代码把视图标识符和工厂进行关联注册让flutter可直接找到工厂

## 原生工程混编flutter
- 混编原理 
  - android原生提供一个FLutterView
  - ios原生提供一个FlutterViewController
- 混编方式
  - 原生工程作为flutter工程的子工程，flutter统一管理（开发效率低）
  - flutter工程作为原生工程共用的子模块，原生工程不变的三端分离模式（推荐使用）
    - 抽离flutter工程，将不同平台的构建产物依照标准组件化形式管理
    - android项目把flutter模块打包成aar，通过build.gradle进行依赖管理
    - ios项目把flutter模块打包成pod，通过Podfle进行依赖管理
- flutter模块
  - flutter create -t module flutter_library
- android模块集成
  - 依赖1 flutter库和引擎 Flutter.jar
  - 依赖2 flutter工程产物 isolate_snapshot_instr/vm_snapshot_data/FLutter_assets...
  - 集成1 flutter build apk --[debug|release]
  - 集成2 把上一步的产物flutter-debug.aar放到安卓工程app/libs目录下
  - 集成3 build.gradle中添加对aar的依赖后sync一下
  - 集成4 MainActivity.java中进行调用
- ios模块集成
  - 依赖1 flutter库和引擎 Flutter.framework
  - 依赖2 flutter工程产物 App.framework
  - 集成1 flutter build ios --[debug|release]
  - 集成2 把上一步的产物拷贝到原生项目根目录下的FlutterEngine目录并创建FlutterEngine.podspec
  - 集成3 pod lib lint
  - 集成4 Podfile中集成后需要pod install
  - 集成5 AppDelegate.m中进行调用

## 混合导航栈
- 原生采用单容器单页面机制(一个ViewController/Activity对应一个页面)
- Flutter采用单容器多页面机制(一个ViewController/Activity对应多个flutter页面)
- 原生跳转到flutter
  - ios初始化flutter容器(FlutterViewController)并设置初始路由页面即可
  - android需要把View包装到Activity的contentView中并设置初始化路由
- flutter跳转到原生
  - flutter打开新的原生页面(通过方法通道进行实现)
  - flutter回退到旧的原生页面(需要关闭flutter容器也是通过方法通道进行实现)
- 性能问题
  - 由于flutter容器初始化成本比较高，每启动一个实例都要创建一个新的engine和Isolate
  - 尽量避免flutter跳到原生后又跳到flutter，尽量在flutter完成业务的闭环
  - 业界解决方案
    - 今日头条修改flutterEngine源码，使多engine在底层共享Isolate
    - 闲鱼的共享FlutterView，通过hack方法由原生层驱动flutter层渲染内容
    - 上面2种解决方案都有自己的不足，所以尽量减少此应用场景
{% asset_img hybridNav.png 混合导航栈 %}

## 状态管理
- Provider
  - 官方推荐框架，是InheritWidget的语法糖
  - 提供了依赖注入的功能，允许widget树灵活处理数据
  - 可读写的ChangeNotifierProvider/MultiProvider和只读的Provider
  - Provider.of()可获取资源，但页面其他Widget也会刷新
  - ConsumerN可通过Builder(context,model,child)只更新依赖的widget提高性能
- 使用
  - pubspec.yaml中添加Provider依赖
  - 封装: 定义需要共享的数据模型，通过混入ChangeNotifier管理听众，模型中调用notifyListeners()通知听众刷新
  - 注入: 把模型放到widget的父级或更高ChangeNotifierProvider.value(value:SharedModel(),child:Widget())
  - 读写: Provider.of()/ConsumerN()

## 原生推送
- ios
  - 使用APNS苹果推送通知服务
  - 为了保证api统一，ios上也使用封装了APNs的第三方服务
- android
  - 类似Firebase云消息传递机制FCM(FirebaseCloudMessage)实现推送托管
  - 大陆通常使用三方推送(极光/友盟)适配
  - 第三方服务不能享受系统底层的优化，使用自建的长链接通道
  - 但也是共享所有接入第三方推送的app的推送通道，只要有一个存活就可推送
- 推送流程
  - 业务服务器调用APNS/FCM
  - 消息到达用户终端设备
  - 设备解析后把消息转给所属应用
- 极光接入流程
  - 初始化SDK setup(调用原生接口)
  - 获取地址id registrationID(调用原生接口)
  - 注册消息通知 setOpenNotificationHandler(原生回调dart)
  - 单例模式实现整个应用共享
  - android
    - JCommonService是一个后台Service，极光共享长链接通道核心
    - JPushMessageReceiver是一个BroadcastReceiver可获取推送消息
    - AndroidManifest.xml中声明上面2个的注册
    - 收到消息回调后首先启动MainActivity，等待Flutter初始化完成后再回调flutter推送消息
  - ios
    - podspec文件引入极光SDKjpush
    - 用户点击了推送消息，防止是从后台唤醒，要确保flutter初始化好后再回调flutter推送消息
  - 提供应用推送证书，关联极光应用配置
    - android注册appkey后根据包名进行注册并在build.gradle中绑定manifestPlaceholders
    - ios首先要申请推送证书(.p12证书或APNsAuthKey)并在平台配置绑定后工程开启push

## 国际化
- 本质是语言和地区的差异性配置
- 资源
  - 字符串文本
  - 货币单位
  - 时间格式
  - 背景图资源
- 步骤
  - 实现LocalizationsDelegate，将所有需要转换的资源声明为其属性
  - 手动翻译适配
  - app初始化时，将代理类设置为应用程序的翻译回调
  - 官方的方案可以先放弃，借鉴Flutter i18n插件
- Flutteri18n
  - as中安装此插件
  - pubspec.yaml中声明flutter_localizations sdk: flutter依赖
  - res/values/strings_en.arb文件是JSON格式的配置，存放标识符和翻译的键值对
  - 修改上面的arb文件会自动生成lib/generated/i18n.dart(支持静态映射和动态传参)
  - 初始化时2个重要参数localizationsDelegates翻译回调和supportedLocales所支持的语言配置
  - 配置翻译回调时需要GlobalMaterialLocalizations.delegate与GlobalWidgetsLocalizations.delegate是官方widgets本身的翻译回调
  - S.of(context)直接获取arb文件中翻译的文案
  - 翻译的代码只能在获取到context的前提下才能生效即MaterialApp的子widget，通过MaterialApp的onGenerateTitle回调设置title的国际化
  - ios程序有一套自建的语言环境管理机制，默认是英文，为了支持国际化需要额外配置Localization
  - 原生工程配置
    - 在flutter框架启动前的配置需要在原生中配置
    - android中应用名称在AndroidManifest.xml中application的android:label属性
    - 并且要在android/app/src/main/res中为要支持的语言创建values目录和strings.xml
    - ios工程中的应用名称是在Info.plist文件中，需要一个字符串资源引用的文件InfoPlist.strings并在Localizations中添加多语言

## 适配屏幕分辨率
- 适配屏幕旋转
  - 竖屏模式/横屏模式2套布局方案
  - OrientationBuilder的builder函数可以回调屏幕状态orientation
  - MediaQuery.of(context).orientation可以在OrientationBuilder外获取状态
  - SystemChrome.setPreferredOrientations([DeviceOrientation.portraitUp]);关闭横屏
- 适配分辨率
  - 将屏幕空间划分为多个窗格即用原生类似的Fragment/ChildViewController概念抽象独立区块视觉功能
  - 多窗格布局可以在平板电脑和横屏模式上，实现更好的视觉平衡效果
  - 页面的实现和区块的实现是相互独立的，所以可以实现区块的复用
  - MediaQuery.of(context).size.width可获取屏幕宽度

## 编译模式
- 运行模式
  - Debug JIT
  - Release AOT
  - Profile Release+Profile(Observatory) flutter run --profile
- 编译模式
  - JIT 
    - JustInTime运行时编译
    - 动态编译/将dart代码编译成中间代码scriptSnapshot最终生成DartKernel(可动态更新)需要在设备上用DartVM解释执行实现widget重建
    - 可以模拟器和真机运行
    - 打开assert断言/debug调试/observatory调试/hotReload热重载
    - 没有优化应用启动速度/代码执行速度/二进制包大小/部署
    - flutter run --debug
  - AOT
    - AheadOfTime运行前编译
    - 静态编译/生成设备可执行的二进制码
    - 只能真机运行
    - 关闭assert断言/debug调试/observatory调试/hotReload热重载
    - 优化应用启动速度/代码执行速度/二进制包大小/部署
    - flutter run --release
- 运行时识别编译模式
  - 区别
    - debug模式下打印详细日志，调用开发接口
    - release模式下记录精简日志，调用生产接口
  - 识别
    - 通过断言识别 assert((){ // DoSth4Debug; return true;}()) release下此代码被删除
    - 通过DartVM提供的编译常数识别 if(kReleaseMode){}else{/ DoSth4Debug;} 代码总会被打包
- 分离配置环境
  - 抽象配置并用InheritedWidget封装
  - 配置多入口(main_dev.dart/main.dart)
  - 读取配置(运行时通过InheritedWidget将配置部分应用到子widget)
  - 编译打包(通过不同选项构建不同的安装包flutter run -t|--target main[_dev].dart)
- 原生配置差异
  - android: build flavor
  - ios: 多个build target

## hotReload
{% asset_img hotReload.png 热重载流程 %}
- 热重载流程
  - 扫描工程改动
  - 增量编译
  - 推送更新
  - 代码合并
  - Widget重建
- 不支持热重载场景(此场景可以用hotRestart)
  - 代码编译错误
  - widget状态无法兼容
  - 全局变量和静态属性的更改
  - main()里的更改
  - initState()里的更改
  - 枚举和泛类型的更改

## 工具链优化
- 输出日志
  - print() 涉及IO操作，耗费系统资源
  - debugPrint() 可以定制打印能力如debugPrint = (String msg,{int wrapWidth}{})
- 断点调试
  - 标记断点
  - 调试应用
  - 查看信息
- 布局调试
  - DebugPainting
    - 布局边界展示辅助线
    - debugPaintSizeEnabled=true
  - FlutterInspector 查看具体信息

## 性能优化
- 性能问题
  - GPU线程问题
    - 涉及widget裁剪/蒙层等多视图叠加渲染可main中使用checkerboardOffscreenLayers=true检查
    - 缺少缓存导致静态图像反复绘制可main中使用checkerboardRasterCacheImages=true检查，有问题可使用RepaintBoundary缓存
  - UI线程问题(cpu)
    - OpenDevTools
    - Performance性能工具
      - 可记录cpu帧图(火焰图)展示cpu调用栈表示cpu繁忙程度
      - y轴表示调用栈，每一层都是一个函数，调用栈越深火焰越高，底部就是正在执行的函数，上方是父函数
      - x轴表示单位时间，在x轴越长表示执行时间越长，平顶表示函数可能存在性能问题
    - 有问题可以使用Isolate或compute()将耗时的操作移到主Isolate外完成
- 性能图层
  - PerformanceOverlay
  - 为采集真实的环境需要使用Profile分析模式
  - 模拟器使用x86指令集/真机使用ARM指令集
  - flutter run --profile
  - 性能图层展示了最近300帧的表现
  - 为了保证60Hz的刷新频率，每一帧都要小于16ms(1/60)
- 经验
  - 控制build方法耗时，将widget拆小越小越可复用
  - 不要使用widget半透明效果，而使用图片代替
  - 列表采用懒加载

## 自动化测试
- 单元测试
  - 对软件中最小可测试单元(语句/函数/方法/类)进行验证
  - pubspec.yaml中需要依赖test包 dev_dependencies: test:
  - 测试用例
    - 定义 test()/group()测试用例封装类
    - 执行
    - 验证 expect()将执行结果与预期进行比较
  - 模拟mock
    - pubspec.yaml中依赖mockito
    - Mock类可模拟任何外部依赖
    - when().thenAnswer()当符合条件时进行注入
- UI测试
  - pubspec.yaml中使用flutter_test包提供核心框架
  - testWidgets('name',(tester) async{})测试用例封装类
  - await tester.pumpWidget(MyApp())触发MyApp渲染
  - find.text(str)查找字符串文本为str的widget
  - find.byIcon(Icons.add)查找到按钮控件
  - await tester.tap()点击
  - await tester.pump()强制渲染刷新

# 综合应用
## 生产异常捕获和信息采集
- flutter异常
  - try/catch捕获异常
  - dart程序不强制要求必须处理异常(dart用事件循环机制来运行任务，各任务状态独立)
- dart异常
  - App异常
    - 同步异常 通过try/catch捕获
    - 异步异常 通过Future的catchError语句捕获
    - 集中管理 通过Zone.runZoned Zone表示代码执行的范围相当于沙盒,onError回调捕获错误，统一处理异常可以把main()的runApp()放到Zone中
```

runZoned<Future<Null>>(() async {
  runApp(MyApp());
}, onError: (error, stackTrace) async {
 //Do sth for error
});

```
  - Framework异常
    - 触发了底层的try/catch，有异常就会渲染ErrorWidget
    - 为了提高用户体验需要重写ErrorWidget.builder()自定义错误页面
```

ErrorWidget.builder = (FlutterErrorDetails flutterErrorDetails){
  return Scaffold(
    body: Center(
      child: Text("Custom Error Widget"),
    )
  );
};

```
  - FlutterError
    - 为了集中处理框架异常
    - FlutterError.onError在接收到框架异常时执行相应的回调
    - 可以把flutter框架的异常统一转发到当前Zone里统一处理，这样就可以捕获应用所有异常
```

FlutterError.onError = (FlutterErrorDetails details) async {
  //转发至Zone中
  Zone.current.handleUncaughtError(details.exception, details.stack);
};

runZoned<Future<Null>>(() async {
  runApp(MyApp());
}, onError: (error, stackTrace) async {
 //Do sth for error
});

```
- 异常上报
  - 三方服务厂商
    - 友盟
    - bugly(社区比较活跃)
    - sentry(开源)
  - bugly接入
    - dart接口封装(实现单例的FlutterCrashPlugin的setup/postException的方法通道)
    - ios
      - flutter_crash_plugin.podspec引入BuglySDK
      - 实现原生接口FlutterCrashPlugin
    - android
      - build.gradle引入BuglySDK(crashreport和nativecrashreport)
      - 实现原生接口FlutterCrashPlugin
      - AndroidManifest.xml中配置网络/日志读取等的权限注册
    - 关联应用配置
      - ios只需要调用dart的setup就可以
      - android需要build.gradle中增加NDK架构支持和AndroidP的network_security_config.xml允许http传输数据并在AndroidManifest.xml中新增同名的网络安全配置
    - 接入插件
      - pubspec.yaml中添加dependencies
      - main()里拦截到应用所有的错误后调用上报接口
  - 底层异常
    - flutter只能拦截dart层的异常
    - engine层大部分代码都是C++写的，一旦有异常需要借助原生系统的Crash监听机制
    - bugly也可以自动收集原生代码的crash，开发者可以把flutterEngine层的符号表下载下来，使用安卓的ndk-stack或ios的symbolicatecrash或atos命令对对crash堆栈进行解析得出引擎层崩溃的代码

## 线上质量
- 页面异常率
  - 异常发生次数/页面pv数
```

// 异常发生次数
int exceptionCount = 0; 
Future<Null> _reportError(dynamic error, dynamic stackTrace) async {
  exceptionCount++; //累加异常次数
  FlutterCrashPlugin.postException(error, stackTrace);
}

Future<Null> main() async {
  FlutterError.onError = (FlutterErrorDetails details) async {
    //将异常转发至Zone
    Zone.current.handleUncaughtError(details.exception, details.stack);
  };

  runZoned<Future<Null>>(() async {
    runApp(MyApp());
  }, onError: (error, stackTrace) async {
    //拦截异常
    await _reportError(error, stackTrace);
  });
}

// 页面打开次数
int totalPV = 0;
//导航监听器
class MyObserver extends NavigatorObserver{
  @override
  void didPush(Route route, Route previousRoute) {
    super.didPush(route, previousRoute);
    totalPV++;//累加PV
  }
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return  MaterialApp(
    //设置路由监听
       navigatorObservers: [
         MyObserver(),
       ],
       home: HomePage(),
    ); 
  }   
}


// 页面异常率计算

double pageException() {
  if(totalPV == 0) return 0;
  return exceptionCount/totalPV;
}

```
- 页面帧率
  - FPS(60Hz，因为VSync信号周期就是每秒60次)
  - window.onReportTimings()回调最近绘制帧所耗费的时间
  - FPS = 60 * 实际渲染的帧数 / 本来应该渲染的帧数
```

import 'dart:ui';

var orginalCallback;

void main() {
  runApp(MyApp());
  //设置帧回调函数并保存原始帧回调函数
  orginalCallback = window.onReportTimings;
  window.onReportTimings = onReportTimings;
}

//仅缓存最近25帧绘制耗时
const maxframes = 25;
final lastFrames = List<FrameTiming>();
//基准VSync信号周期
const frameInterval = const Duration(microseconds: Duration.microsecondsPerSecond ~/ 60);

void onReportTimings(List<FrameTiming> timings) {
  lastFrames.addAll(timings);
  //仅保留25帧
  if(lastFrames.length > maxframes) {
    lastFrames.removeRange(0, lastFrames.length - maxframes);
  }
  //如果有原始帧回调函数，则执行
  if (orginalCallback != null) {
    orginalCallback(timings);
  }
}

double get fps {
  int sum = 0;
  for (FrameTiming timing in lastFrames) {
    //计算渲染耗时
    int duration = timing.timestampInMicroseconds(FramePhase.rasterFinish) - timing.timestampInMicroseconds(FramePhase.buildStart);
    //判断耗时是否在Vsync信号周期内
    if(duration < frameInterval.inMicroseconds) {
      sum += 1;
    } else {
      //有丢帧，向上取整
      int count = (duration/frameInterval.inMicroseconds).ceil();
      sum += count;
    }
  }
  return lastFrames.length/sum * 60;
}


```
- 页面加载时长
```

class MyHomePage extends StatefulWidget {
  int startTime;
  int endTime;
  MyHomePage({Key key}) : super(key: key) {
    //页面初始化时记录启动时间
    startTime = DateTime.now().millisecondsSinceEpoch;
  }
  @override
  _MyHomePageState createState() => _MyHomePageState();
}

class _MyHomePageState extends State<MyHomePage> {
  @override
  void initState() {
    super.initState();
    //通过帧绘制回调获取渲染完成时间
    WidgetsBinding.instance.addPostFrameCallback((_) {
      widget.endTime = DateTime.now().millisecondsSinceEpoch;
      int timeSpend = widget.endTime - widget.startTime;
      print("Page render time:${timeSpend} ms");
    });
  }
  ...
}

```

## 工程架构
- 组件化
  - 独立的功能进行拆分
  - 可以是一个包/页面/UI控件/常用函数
  - 基本原则
    - 单一性原则，清晰自己的边界专注做自己单一的事
    - 抽象化原则，抽象接口设计，变化因子尽量自己维护
    - 稳定性原则，外部依赖尽量稳定，如果不稳定考虑下一条原则
    - 自完备性原则，尽量自给自足减少外部依赖，除非上一条满足
  - 实施步骤
    - 剥离并实现基础功能
      - 网络请求
      - 组件中间件
      - 第三方库封装(尽量不直接依赖外部代码)
      - UI组件
    - 抽象并划分业务模块
      - 粒度可先粗后细
      - 后续分步迭代
    - 最小化服务能力
- 平台化
  - 组件化的升级，在组件化的基础上增加了统一分层和依赖治理的概念
  - 组件化关注组件的独立性，平台化关注组件之间的关系合理性(尽量符合单向依赖原则)
  - 如果下层确实要依赖上层可以增加中间层(EventBus/Provider/Router)

## 构建打包发布环境
- travisCI
- ios逆向之重签名
```

# 列出所有开发者证书文件
security find-identity -p codesigning -v

# 找一个开发环境配置文件生成entitlements.plist
security cms -D -i XX.mobileprovision  > profile.plist
/usr/libexec/PlistBuddy -x -c 'Print :Entitlements' profile.plist > entitlements.plist
cat entitlements.plist

# 把准备好的开发环境配置文件复制到XX.app文件夹下
cp XX.mobileprovision Payload/XX.app/embedded.mobileprovision

# 修改包Info.plist中的Bundle Identifier与配置文件中的Bundle Identifier保持一致
/usr/libexec/PlistBuddy -c "Set :CFBundleIdentifier com.XX.XX" Payload/XX.app/Info.plist

# 移除之前的签名文件夹
rm -rf Payload/XX.app/_CodeSignature

# 重签名framework
/usr/bin/codesign --force --sign 84A4B9F1F902462CC33D01E9FF72C1BA04A97653 --entitlements entitlements.plist /Payload/XX.app/Frameworks/JSONModel.framework

# 重签名app执行文件
/usr/bin/codesign --force --sign 84A4B9F1F902462CC33D01E9FF72C1BA04A97653 --entitlements entitlements.plist Payload/XX.app/XX

# 查看app签名信息
codesign -vv -d Payload/XX.app

# 安装调试
ios-deploy -d -b Payload/XX.app

# 打包
zip -qry ppdest.ipa Payload
rm -rf Payload/

```

## 构建混合开发框架
- 混合开发
  - 原生负责提供容器和基础能力支撑
  - flutter负责业务和大部分渲染工作
- 混合开发工程架构
  - 依赖分层管理
    - 原生对flutter的依赖抽象为依赖flutter模块封装的原生组件
    - flutter对原生的依赖抽象为依赖插件封装的原生行为
  - flutter模块定义为原生工程的独立业务层，以原生基础业务层向flutter模块提供业务通用能力，原生基础能力层向flutter模块提供基础功能支撑 
{% asset_img projectArch.png 混合开发工程架构 %}
- 混合开发工作模式
{% asset_img workflow.png 混合开发工作流 %}
{% asset_img cmd.png flutter命令行 %}

- 原生插件依赖管理
  - ios的AFNetworking
  - android的OkHttp
- flutter模块工程依赖管理
  - 使用flutter插件(pubspec.yaml)
  - 模块工程的ios构建产物封装以提供原生ios工程依赖管理
  - 模块工程的android构建产物封装以提供原生android工程依赖管理
  - flutter模块工程把所有原生的依赖都交给了原生工程管理所以其构建产物并不会携带原生插件的封装实现，我们需要遍历模块工程所使用的原生依赖组件，并为他们逐一生成插件代码对应的原生组件封装

## 实践
- TabBar
  - TabBar 默认会在 widget 树中向上寻找离它最近的一个 DefaultTabController节点作为自己的 TabController
  - 如果手动创建 TabController，那么必须将它作为参数传给 TabBar
- FocusScope.of(context).requestFocus(myFocusNode)
- flutter drive --target=test_driver/app.dart