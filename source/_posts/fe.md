---
title: fe
date: 2020-02-28 13:18:52
tags:
---

### fe
#### js
- 原始类型
  - Boolean
  - String
  - Number
  - Undefined
  - Null
  - Symbol
- 类型判断
  - typeof
    - undefined
    - boolean
    - string
    - number
    - function
    - object
  - instanceof
    - 数组
    - 自定义类
- 类型传递
  - 值类型按值传递
    - Boolean
    - String
    - Number
    - Undefined
    - Null
  - 引用类型按共享传递
    - Object
    - Function
    - Array
    - Date
- 原型和原型链
  - 引用类型都有对象的特性，可自由扩展属性
  - 引用类型都有一个__proto__属性指向原型对象
  - 所有的函数都有prototype属性指向原型对象
  - 引用类型的__proto__指向它构造函数的prototype
  - 当试图得到一个对象的某个属性时如果本身没有则去__proto__上寻找形成原型链
- 作用域和作用域链
  - 全局作用域
  - 函数作用域
  - ES6后增加块级作用域
  - 当前作用域中没有的变量称为自由变量，自由变量向父级作用域寻值，形成作用域链

#### css
- 选择器权重和优先级
  - !important
  - 内联样式
  - ID选择器
  - 类、伪类、属性选择器
  - 元素选择器
- 盒子模型
  - margin
  - border
  - padding
  - content
  - 分类
    - border-box
    - content-box
- position
  - static
  - relative
  - absolute
  - fixed
- flex
  - container
    - flex-direction
    - justify-content
    - align-items
  - item
- 重绘和回流
  - 重绘
    - 简单的进行样式的变化，如颜色、背景等，不影响几何信息
    - 开销小
  - 回流（重排）
    - DOM中的尺寸大小几何信息，位置信息发生变化导致重新渲染
    - 开销大
    - getComputedStyle/currentStyle获取即时属性值的操作也会触发回流
  - 避免
    - 缓存DOM对象
    - 避免逐条修改样式，使用类名合并
    - 将DOM离线后操作不会有性能问题，操作完毕后上线display:none/block;
    - 现代浏览器会维护一个flush队列，在不得已的时候会执行队列
- 1px线实现方式
  - 渐变
  - 缩放
  - 图片(base64/svg)
- 图片保真
  - 统一使用最高倍图，但浪费带宽
  - srcset属性
- 字体适配
  - 默认最小限制
    - PC上是12px
    - 手机上是8px
  - 不建议使用奇数数值，容易在低端手机上出现锯齿
  - 尽量使用默认系统字体
  - font-family取值
    - 具体字体名称
    - 通用类别
      - serif 衬线字体族
      - sans-serif 非衬线字体族
      - monospace 等宽字体
      - cursive 草书字体
      - fantasy 艺术字体
      - systme-ui 系统默认字体
      - emoji 兼容表情符号
      - math 数学表达式
      - fangsong 仿宋

#### 算法
- 程序
  - 数据
  - 算法
- 数据结构
  - 简单数据结构
    - 有序数据结构：栈、队列、链表，省空间，存储空间小
    - 无序数据结构：字典、集合、散列集，省时间，读取时间快
  - 高级数据结构
    - 树、堆
    - 图
- 常见算法
  - 递归
  - 排序
  - 枚举
- 算法复杂度
  - 时间复杂度（好估算、好评估）
    - 常数阶O(1) #每行一次性的语句就是常数阶
    - 线性阶O(n)
    - 平方阶O(n^2)
    - 立方阶O(n^3)
    - k次方阶O(n^k)
    - 指数阶O(2^n)
    - 对数阶O(logN)
    - 线性对数阶O(nlogN)
  - 空间复杂度
  - 分析技巧
    - 几重循环，一般就是1层循环就是O(n)，两重循环就是O(n^2)
    - 如果有二分，则为O(logN)
    - 每行一次性的语句则为1
    - 所有行的复杂度相加后除去常数即得复杂度

#### 浏览器相关
- 加载过程
  - DNS解析
  - TCP连接
  - HTTP请求
  - HTTP响应
  - 客户端渲染
    - 根据html生成DOM
    - 根据css生成CSSOM
    - DOM和CSSOM整合生成RenderTree
    - 布局合成 、绘制渲染
    - 遇到script会阻塞渲染，因为js执行和浏览器渲染共用一个线程
- 性能优化
  - 减少页面体积，提升网络加载
    - 静态资源压缩、合并
    - 静态资源缓存
    - 使用CDN
  - 优化页面渲染
    - css放前、js放后
    - 懒加载
    - 减少DOM操作
    - 事件节流
    - SSR
- web安全
  - SQL注入
  - XSS
  - CSRF

#### 性能优化
- DNS解析
  - 浏览器DNS缓存
  - DNS prefetch
- TCP连接
  - 长连接
  - 预连接
  - 接入SPDY协议
- HTTP请求
  - 减少次数（loader中的exclude、三方库DllPlugin/DllReferencePlugin）
  - 减少体积（webpack-bundle-analyzer、代码压缩混淆、gzip、tree-shaking、按需加载）
  - CDN
    - content delivery network
    - 2大功能
      - 缓存
      - 回源
    - CDN域名和业务服务域名一定要分开，避免cookie无用传输
  - Gzip
    - gzip的内核就是Deflate
    - 通常能减小70%的体积
    - 原理就是把内容中重复的字符串临时替换，使整个文件变小，所以重复率越高，收益越好
    - nginx等代理文件中可以配置index.js/index.js.gz 以减少cpu临时压缩的消耗
  - 图片优化
    - JPEG/JPG 有损压缩、体积小、色彩丰富，常用轮播图，不支持透明
    - PNG 无损压缩、质量高、支持透明，常用单色小图logo，体积大
      - PNG-8  二进制的位数8，最多支持256种颜色
      - PNG-24 二进制的位数24，最多支持1600万种颜色
    - SVG
      - 文本文件
      - 体积小、不失真、可压缩
      - 对图像的处理不是基于像素，而是基于对图像的描述
      - 可以放到HTML的DOM中，也可以单独保存.svg文件
      - 渲染成本比较高，性能要求比较高
    - Base64
      - 文本文件
      - 依赖编码，小图标解决方案
      - 编码后，体积会增大1/3
    - WebP
      - 年轻的全能选手
      - 支持有损和无损压缩
      - 支持透明、支持动态图、支持丰富的色彩、体积比较小
      - 兼容性比较差，编码时比jpg需要更多计算资源
- 渲染
  - SSR
    - SEO
    - 首屏白屏
  - CSS
  - JSji
  - DOM
  - 懒加载
  - 事件节流和防抖
  - 回流和重绘
- 浏览器引擎
  - 渲染引擎（内核）
    - 功能部件
      - html解释器，将html输出DOM
      - css解释器，将css输出CSSOM
      - 布局绘制，合成renderTree
      - 网络
      - 存储
      - 图形、图片解码器
      - 音视频
    - 常见分类
      - Trident（IE）
      - Gecko（Firefox）
      - Blink（Chrome、Opera）是webkit的一个分支
      - Webkit（Safari）
    - 优化
      - css
        - css解释器对每条规则都按从右到左的顺序去匹配，因此要减少选择器嵌套
        - 避免使用通配符，只对需要用到的元素进行选择
        - css是阻塞渲染的资源，需要尽早加载，尽早渲染，把css往前放
      - js
        - 默认的js也会阻塞DOM和CSSOM，因为可能会操作DOM
        - async模式加载js不会阻塞浏览器，异步加载js，加载完毕后立即执行
        - defer模式加载js不会阻塞浏览器，异步加载js，加载完毕后延迟到DOMContentLoaded后执行
      - DOM
        - 对DOM的修改引发了DOM几何尺寸的变化，浏览器都要重新计算几何属性，再将结果绘制出来，即回流重排
        - 对DOM的修改没有引发几何尺寸的变化，不需要重新计算几何属性，跳过重排直接执行重绘
        - 减少DOM的访问和操作
        - 必要的DOM操作可以使用DocumentFragment，不会有性能问题
  - js引擎
    - 异步队列
      - micro-task
        - process.nextTick
        - promise
        - MutationObserver
      - macro-task
        - setTimeout
        - setInteral
        - setImmediate
        - IO操作
    - 执行过程
      - 将一个macro-task执行并出队
      - 将一对micro-task执行并出对
      - 执行渲染，更新界面
      - 处理worker相关的任务
      - 循环，当我们需要在异步任务中实现DOM修改时，把它包装成micro-task是明智的选择
- 浏览器缓存
  - MemoryCache
    - 本着节约内存的原则，一般较小的资源有几率入内存
  - ServiceWorkerCache 离线缓存
    - 脱离主线程之外的独立线程
    - 不能操作DOM，只能做些js的计算，数据的请求
    - serviceWorker生命周期
      - install
      - active
      - working
  - HTTPCache
    - 强缓存（200）
      - expires（http1.0,依赖本地时间）
      - cache-control(http1.1,完全替代expires，优先级高)
        - max-age=31536000(有效时间长度，单位秒)
        - s-maxage优先级高于max-age，只在代理服务器上有效
        - public可以被客户端和代理服务器缓存，private只能被客户端缓存，默认private
        - no-cache 忽略所有客户端缓存，与服务器协商缓存
        - no-store 忽略所有缓存，包括协商缓存，直接请求资源
    - 协商缓存（304）
      - Last-Modified/If-Modified-Since（时间戳）
        - 只是更新了文件的元信息，比如touch了文件一下，也会造成更新
        - 时间粒度只能到秒，如果1秒内完成了内容的变更，却不会更新
      - ETag/If-None-Match
        - 解决上述2个问题
        - 基于文件内容为资源编码的唯一字符串标示
        - 因为ETag的生成要耗费服务器资源，所以是上面的补充，不能完全替代，优先级高于上面
        {% asset_img http协商缓存响应头设置流程.png http协商缓存响应头设置流程 %}

  - PushCache 
    - http2的特性
    - 是缓存的最后一道防线，在上面3种缓存都未命中的情况下才会询问PushCache
    - 不同的页面共享了一个http2连接，就共享同一个PushCache
    - 会话级的缓存，session关闭，缓存失效
- 本地存储
  - cookie
    - 状态维持，解决http无状态的问题
    - 体积限制，最大4k
    - 流量消耗
  - storage
    - localStorage 永久有效
    - sessionStorage 会话级有效
  - IndexedDB
    - 非关系型数据库
```
var db;
// 参数1位数据库名，参数2为版本号
const request = window.indexedDB.open("name", 1)
// 使用IndexedDB失败时的监听函数
request.onerror = function(event) {
  console.log('无法使用IndexedDB')
}
// 成功
request.onsuccess  = function(event){
  // 此处就可以获取到db实例
  db = event.target.result
  console.log("你打开了IndexedDB")
}


// onupgradeneeded事件会在初始化数据库/版本发生更新时被调用，我们在它的监听函数中创建object store
request.onupgradeneeded = function(event){
  let objectStore
  // 如果同名表未被创建过，则新建test表
  if (!db.objectStoreNames.contains('test')) {
    objectStore = db.createObjectStore('test', { keyPath: 'id' })
  }
}  

  // 创建事务，指定表格名称和读写权限
  const transaction = db.transaction(["test"],"readwrite")
  // 拿到Object Store对象
  const objectStore = transaction.objectStore("test")
  // 向表格写入数据
  objectStore.add({id: 1, name: 'test1'})

  // 操作成功时的监听函数
  transaction.oncomplete = function(event) {
    console.log("操作成功")
  }
  // 操作失败时的监听函数
  transaction.onerror = function(event) {
    console.log("这里有一个Error")
  }
  


```
```

// lazyload
// 获取所有的图片标签
const imgs = document.getElementsByTagName('img')
// 获取可视区域的高度
const viewHeight = window.innerHeight || document.documentElement.clientHeight
// num用于统计当前显示到了哪一张图片，避免每次都从第一张图片开始检查是否露出
let num = 0
function lazyload(){
    for(let i=num; i<imgs.length; i++) {
        // 用可视区域高度减去元素顶部距离可视区域顶部的高度
        let distance = viewHeight - imgs[i].getBoundingClientRect().top
        // 如果可视区域高度大于等于元素顶部距离可视区域顶部的高度，说明元素露出
        if(distance >= 0 ){
            // 给元素写入真实的src，展示图片
            imgs[i].src = imgs[i].getAttribute('data-src')
            // 前i张图片已经加载完毕，下次从第i+1张开始检查是否露出
            num = i + 1
        }
    }
}
// 监听Scroll事件
window.addEventListener('scroll', lazyload, false);

// fn是我们需要包装的事件回调, delay是时间间隔的阈值
function throttle(fn, delay) {
  // last为上一次触发回调的时间, timer是定时器
  let last = 0, timer = null
  // 将throttle处理结果当作函数返回
  
  return function () { 
    // 保留调用时的this上下文
    let context = this
    // 保留调用时传入的参数
    let args = arguments
    // 记录本次触发回调的时间
    let now = +new Date()
    
    // 判断上次触发的时间和本次触发的时间差是否小于时间间隔的阈值
    if (now - last < delay) {
    // 如果时间间隔小于我们设定的时间间隔阈值，则为本次触发操作设立一个新的定时器
       clearTimeout(timer)
       timer = setTimeout(function () {
          last = now
          fn.apply(context, args)
        }, delay)
    } else {
        // 如果时间间隔超出了我们设定的时间间隔阈值，那就不等了，无论如何要反馈给用户一次响应
        last = now
        fn.apply(context, args)
    }
  }
}

// 用新的throttle包装scroll的回调
const better_scroll = throttle(() => console.log('触发了滚动事件'), 1000)
document.addEventListener('scroll', better_scroll)



// throttle
// fn是我们需要包装的事件回调, interval是时间间隔的阈值
function throttle(fn, interval) {
  // last为上一次触发回调的时间
  let last = 0
  
  // 将throttle处理结果当作函数返回
  return function () {
      // 保留调用时的this上下文
      let context = this
      // 保留调用时传入的参数
      let args = arguments
      // 记录本次触发回调的时间
      let now = +new Date()
      
      // 判断上次触发的时间和本次触发的时间差是否小于时间间隔的阈值
      if (now - last >= interval) {
      // 如果时间间隔大于我们设定的时间间隔阈值，则执行回调
          last = now;
          fn.apply(context, args);
      }
    }
}
// 用throttle来包装scroll的回调
const better_scroll = throttle(() => console.log('触发了滚动事件'), 1000)
document.addEventListener('scroll', better_scroll)

// fn是我们需要包装的事件回调, delay是每次推迟执行的等待时间
function debounce(fn, delay) {
  // 定时器
  let timer = null
  
  // 将debounce处理结果当作函数返回
  return function () {
    // 保留调用时的this上下文
    let context = this
    // 保留调用时传入的参数
    let args = arguments

    // 每次事件被触发时，都去清除之前的旧定时器
    if(timer) {
        clearTimeout(timer)
    }
    // 设立新定时器
    timer = setTimeout(function () {
      fn.apply(context, args)
    }, delay)
  }
}

// 用debounce来包装scroll的回调
const better_scroll = debounce(() => console.log('触发了滚动事件'), 1000)
document.addEventListener('scroll', better_scroll)



```
- 性能监测
  - 可视化工具
    - Performance
    - LightHouse
  - 性能api
    - performance

#### 技能分层
- 基础页面开发（pc网页）
  - 设计稿审查
    - 确定设计稿的开发友好性（是否有还原成本高和无法实现的地方）
    - 特殊元素是否有合理的边界处理（如文案超出外层容器怎么办）
    - 确定页面的框架结构（layout）
    - 识别可复用的组件（跨页面可复用/当前页面可复用）
  - 编写页面骨骼框架
    - 盒模型统一 box-sizing:border-box;
    - 布局
      - 普通文档流
      - 浮动布局
      - 绝对布局
      - 弹性布局
      - 网格布局
  - 填充网页内容
  - 润色
    - BEM 
      - 基于组件的css命名规范
      - block模块，模块名字的单词之间用-连接
      - element元素，模块中的子元素，用__连接模块名
      - modifier修饰符，元素的其他形态，用--连接
      - B-B__E--M
  - 兼容性测试
    - html兼容性
    - css兼容性
    - js兼容性
- 响应式页面开发（移动端网页） 
  - 目标
    - 为不同的浏览器窗口使用不同的样式代码
    - 页面元素的尺寸能够依据浏览器窗口变化而平滑变化
  - 步骤
    - 添加vierport的meta标签
    - 使用mediaQuery 
      - @media(min|max-width|height orientation)
      - 两种方式
        - style代码里@media (){}
        - <link media="(min-width: 769px)" href="min-769.css">
      - 样式断点，参考bulma框架
        - mobile
        - tablet 769
        - desktop 1024
        - widescreen 1216
        - fullhd 1408
    - 使用viewport单位和rem 解决元素的尺寸能响应变化
      - 使用vw作为唯一单位
        - sass函数将设计稿尺寸的像素单位转换为vw单位
        - 所有尺寸全部转换为vw包括文字
        - 1px使用transform的scale实现
        - vw缺点就是会无限放大或缩小
      - vw + rem
        - 根元素的字体大小为vw，同时限制最大值/最小值并配合body最大宽/最小宽实现断点
        - 其他元素统一使用rem单位
```

// iPhone 6尺寸作为设计稿基准
$vw_base: 375; 
@function vw($px) {
    @return ($px / $vm_base) * 100vw;
}


.mod_grid {
    position: relative;
    &::after {
        // 实现1物理像素的下边框线
        content: '';
        position: absolute;
        z-index: 1;
        pointer-events: none;
        background-color: #ddd;
        height: 1px;
        left: 0;
        right: 0;
        top: 0;
        @media only screen and (-webkit-min-device-pixel-ratio: 2) {
            -webkit-transform: scaleY(0.5);
            -webkit-transform-origin: 50% 0%;
        }
    }
    ...
}


// vw+rem
// rem 单位换算：定为 75px 只是方便运算，750px-75px、640-64px、1080px-108px，如此类推
$vw_fontsize: 75; // iPhone 6尺寸的根元素大小基准值
@function rem($px) {
     @return ($px / $vw_fontsize ) * 1rem;
}
// 根元素大小使用 vw 单位
$vw_design: 750;
html {
    font-size: ($vw_fontsize / ($vw_design / 2)) * 100vw; 
    // 同时，通过Media Queries 限制根元素最大最小值
    @media screen and (max-width: 320px) {
        font-size: 64px;
    }
    @media screen and (min-width: 540px) {
        font-size: 108px;
    }
}
// body 也增加最大最小宽度限制，避免默认100%宽度的 block 元素跟随 body 而过大过小
body {
    max-width: 540px;
    min-width: 320px;
}
```
- 滑屏应用开发（活动营销页面）
  - swiper
  - 自己实现
    - 手势动作判断
    - 执行相应动画
- 动效开发（活动营销页面）
  - transition 补间动画 
  - animation 逐帧动画+补间动画
  - gif
  - js控制关键帧sprite的background-position
  - canvas库（createJS，pixi.js）
  - svg+SMIL
    - 声明视窗 <svg width=100 height=100 ></svg>
    - 绘制路径 <path d="指令数据" style="填充描边"></path>
    - 绘制图形 <circle cx="" cy="" r="" style="" />
    - 添加动画 <animateMotion>
    - SMIL synchronizedMultimediaIntegrationLanguage同步多媒体集成语言，主要用作交互

    
#### 软技能
- 健身
- 理财
- 韧性
- 责任心
- 持续学习能力
- 团队合作能力
- 交流沟通能力
