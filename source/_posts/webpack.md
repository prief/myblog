---
title: webpack
date: 2019-08-17 16:29:05
tags: webpack
---

# 基础篇
## webpack及发展历史
- 目的
  - css的预处理
  - ES6等的支持
  - 图片压缩
  - 发布产物的压缩混淆
- 同类
  - rollup
  - parcel
- 安装准备
  - brew install nvm , windows可以安装nvm-windows
  - nvm install v10.16.3
  - node -v
  - npm -v
  - mkdir webpack-demo && cd webpack-demo
  - npm init -y
  - npm i -D webpack webpack-cli
  - ./node_modules/.bin/webpack -v
- 模块路径解析
  - 绝对路径（不推荐）
  - 相对路径
    - 查找相对路径下的同名的文件或目录
    - 是文件则直接加载
    - 是目录则查找目录下的package.json中的main字段
    - 有则按其加载
    - 没有main字段或没有package.json则加载index.js
  - 模块名
    - 查找当前目录/上级目录中node_modules中的同名目录
    - 查找全局node_modules中的模块

## 基础配置
- 基础
  - mode
  - entry
  - output
  - module
  - plugins
- mode
  - production 默认
  - development
  - none
- entry
  - 单入口 "path"
  - 多入口 {name:path}
- output
  - path 输出路径
  - filename 构建文件名称
    - '[name]' 对应入口中的name
    - '[hash]' 对应文件的hash
- loaders
  - 本质是一个函数，接收源文件作为参数输出转换后的结果
  - 执行顺序
    - 同一个rule中多个loader从后到前执行
    - 多个loader匹配时用enforce:pre|post约定顺序
    - 行内loader，在require('loader1!./file.json')
    - 所有的loader按照前置loader/行内loader/普通loader/后置loader顺序执行
  - babel-loader
    - 解析ES6/7
    - 需要安装@babel/core @babel/preset-env @babel/proposal-class-properties等
    - 配置文件.babelrc中增加presets和plugins
  - style-loader
    - 将css插入head的style标签
  - css-loader
    - 解析.css文件转换成commonjs对象
  - less-loader
    - 解析less代码成css，依赖less
  - file-loader
    - 解析各种文件资源（图片/字体等）
    - 如果希望能把小资源转成base64可以使用url-loader的limit
    - 把二进制文件转成base64后文件大小会增加二进制文件的1/3左右
  - raw-loader 将文件以字符串内容形式导入
  - thread-loader
- plugins
  - 本质是一个类，实现了apply方法
  - CommonsChunkPlugin chunk相同代码抽取成公共js
  - CleanWebpackPlugin 清理构建目录
  - CopyWebpackPlugin
  - ZipWebpackPlugin 将打包出的资源生成一个zip包
  - HtmlWebpackPlugin 
  - ExtractTextWebpackPlugin
  - MiniCssExtractPlugin 需要配合.loader替换style-loader进行css文件提取
  - OptimizeCssAssetsWebpackPlugin
  - UglifyjsWebpackPlugin

## 高级配置
- 环境差异
  - module.exports=(env,argv)=>{console.log(argv.mode)}
  - 生产环境
    - 压缩资源
    - 分离资源
  - 开发环境
    - debug
    - hotReload
  - 配置拆分
    - base.js
    - dev.js
    - prod.js
- 构建监听
  - webpack 命令行传 --watch
  - webpack.config.js中 watch:true
  - 是通过轮询文件最后修改时间进行的，同时也有aggregateTimeout防止短时间多次修改，有poll指定每秒轮询多少次，ignore指定忽略，这些属性在watchOptions中
- WDS
  - webpack-dev-server
  - WDS不输出文件都是存在内存中，所以不用手动刷新浏览器可实现HMR
  - dev: "webpack-dev-server --open"
  - webpack.config.js
    - mode: 'development'
    - plugins: [new webpack.HotModuleReplacementPlugin()]
    - devServer:{hot:true,contentBase:'./dist',before(app){app.get("path",(req,res)=>{res.json({})})},proxy:{"/api":{target:'',pathRewrite:{}}}}
  - WDS灵活定制版WDM webapck-dev-middleware
  - HMR 
    - 由compiler把源码编译成bundle和HMR的patch
    - bundle由本地的bundleServer返回给浏览器
    - HMR的patch由本地的HMRServer(WebSockt)返回浏览器的HMRRuntime
    - 浏览器执行bundle和patch执行代码
    - 核心api
      - module.hot
      - module.hot.accept("指定模块",()=>{})
      - module.hot.dispose((data)=>{}) #当前模块
- 文件指纹
  - hash 和整个项目有关，只要项目内有变化hash就变，文件处理也可以用[hash:8]
  - chunkhash 不同的entry会生成不同的chunkhash，常用于js，常设置在output.filename中[chunkhash:8]
  - contenthash 文件内容不变contenthash不变，常用于css，常设置在MiniCssExtractPlugin的filename中[contenthash:8]
- 代码压缩
  - html 使用html-webpack-plugin的minify对象属性
  - css 使用optimize-css-assets-webpack-plugin和cssnano
  - js 内置了uglifyjs-webpack-plugin
- 按需加载
  - import(/* webpackChunkName: "lodash" */ 'lodash').then((_) => {})
  - webpackChunkName作为注释会告知webpack动态加载模块的名称，配合output.chunkFilename指定chunk输出的文件名，否则会以简单的数字标示
  - 需要syntax-dynamic-import这个babel插件处理import()
  - 按需动态加载的import()语法依赖promise，对于低版本的要promise的polyfill
- postcss
  - autoprefixer
    - o presto
    - ms trident
    - moz gecko
    - webkit webkit
  - npm i -D postcss-loader autoprefixer
- 屏幕分辨率
  - rem: font-size of the root element，是相对单位
  - npm i -D px2rem-loader
  - options:{remUnit:75,remPrecision:8}
  - npm i lib-flexible lib-flexible在页面渲染时计算rem的值
- 内联资源到html
  - 内联html/js
    - raw-loader 
    - html模版里写${ require('raw-loader!./meta.html')} 直接内联html
    - script里写${ require('raw-loader!babel-loader!../node_modules/lib-flexible/flexible.js') }内联js
  - 内联css
    - style-loader options:{insertAt:'top',singleton:true //所有style合并成1个 }
    - html-inline-css-webpack-plugin 将打包好的css内联
- MPA
  - 手动的可增加1个entry，然后增加1个对应的htmlWP，不方便维护
  - 通用的动态设置entry:glob.sync(path.join(__dirname,'./src/*/index.js'))和对应的htmlWP
```

const setMPA = () => {
    const entry = {};
    const htmlWebpackPlugins = [];
    const entryFiles = glob.sync(path.join(__dirname, './src/*/index.js'));
    Object.keys(entryFiles)
        .map((index) => {
            const entryFile = entryFiles[index];
            const match = entryFile.match(/src\/(.*)\/index\.js/);
            const pageName = match && match[1];

            entry[pageName] = entryFile;
            htmlWebpackPlugins.push(
                new HtmlWP({
                    inlineSource: '.css$',
                    template: path.join(__dirname, `src/${pageName}/index.html`),
                    filename: `${pageName}.html`,
                    chunks: ['vendors', pageName],
                    inject: true,
                    minify: {
                        html5: true,
                        collapseWhitespace: true,
                        preserveLineBreaks: false,
                        minifyCSS: true,
                        minifyJS: true,
                        removeComments: false
                    }
                })
            );
        });

    return {
        entry,
        htmlWebpackPlugins
    }
}

const {entry,htmlWebpackPlugins} = setMPA()

```
- sourceMap
  - 开发环境使用，生产环境只需要传到监控平台
  - 关键字
    - eval 使用eval包裹模块代码，最后面制定对应的源文件，不生成单独的map文件
    - cheap 不包含列信息
    - source-map 生成.map文件
    - inline 将.map作为DataURI嵌入，不单独生成.map
    - module 包含loader的source-map,可对依赖分析
  - 类型
    - 生产环境使用source-map，map文件上传到监控平台
    - 开发环境使用eval-source-map，加速打包
- 提取公共资源
  - 基础库(vue/react/react-dom)分离到cdn，可使用html-webpack-externals-plugin
  - 也可以用webpack4内置的splitChunksPlugin代替CommonsChunkPlugin
  - chunks说明
    - async 默认选项，表示异步引入的库进行分离
    - initial 同步引入的库进行分离
    - all 推荐使用，所有引入的库都进行分离
  - chunks分离后需要把分离出的name添加到htmlWP中的chunks
```
 optimization:{
        splitChunks:{
            minSize: 0,
            maxSize:1000,
            minChunks: 2,
            cacheGroups:{
                vendors:{
                    test:/(react|react-dom)/,
                    name:"vendors",
                    chunks:"all"
                },
                commons:{
                  name:"commons",
                  chunks:"all",
                  minChunks:2
                }
            }
        }
    }

```
- treeShaking
  - 把模块中没被引用的方法在uglify阶段去除掉
  - 使用
    - 必须是ES6的语法，不能用在CommonJS，ES6可以静态分析
    - 在.babelrc里设置module:false把模块的静态分析交给webpack处理
    - 对class类的treeShaking需要在.babelrc里配置loose:true
    - mode为production情况下默认开启了此功能
    - 要求导出的函数不能有副作用，否则也会失效,即包的package.json.sideEffects:false声明
  - 原理
    - DCE dead code elimination 无用代码擦除
      - 代码不可到达
      - 代码执行的结果不会被用到
      - 只写不读的代码
    - ES6模块特点
      - import只能出现在代码顶层
      - import的模块名只能是字符串常量
      - import的binding是immutable的
- ScopeHoisting
  - 问题
    - 构建后的模块代码都是通过闭包实现IIFE
    - 大量的闭包导致代码体积增大，运行时内存开销增大
  - 分析
    - 把所有的模块都包裹一层函数形成IIFE
    - 把import转换成_webpack_require,export转换成_webpack_exports
    - 把所有模块都缓存到modules数组
    - 通过WEBPACK_REQUIRE_METHOD(0) 启动程序
  - scopeHoisting将模块代码按照引用顺序放在一个函数作用域，适当的做重命名防止变量冲突，达到减少函数声明和内存开销
  - 使用时mode设置为production默认开启
  - 手动开启使用 new webpack.optimize.ModuleConcatenationPlugin()
- 动态import
  - 把相同的代码抽离到一个共享模块，在通过懒加载动态import使初始下载的代码更小
  - 懒加载
    - CommonJS使用方式require.ensure()
    - ES6使用方式 动态import目前还没有原生支持，需要babel插件
      - npm i @babel/plugin-syntax-dynamic-import
      - .babelrc中plugins:['@babel/plugin-syntax-dynamic-import']
      - 代码中需要的地方用import('./dynamic.js').then()
- ESLint
  - 制定规范
    - 基于eslint:recommmend配置进行改进，不重复造轮子
    - 能够帮助发现错误的规则全部开启
    - 保持风格统一，不要限制开发体验
  - 落地
    - 和CICD集成 如gitlab的pipline
    - 和webpack集成
  - 本地开发增加precommit钩子(本地可以--no-verify绕过，所以CICD必须要有)
    - npm i -D husky
    - "precommit": "lint-staged"
    - "lint-staged": {"linters":"*.{js,scss}":["eslint --fix", "git add"]}
  - webpack集成ESLint
    - .js文件先用eslint-loader再使用babel-loader
    - npm i -D eslint eslint-loader babel-eslint eslint-plugin-import ...
    - .eslintrc.js
```
module.exports = {
    parser:"babel-eslint",
    extends:[''],
    env:{
        browser:true,
        node:true
    },
    rules:{
        indent:[2,2]
    }
}

```

- 打包库和组件
  - 需求
    - 打包压缩版和非压缩版
    - 支持AMD/CJS/ESM模块引入和script直接引入，统称UMD
  - 实现
```
const TerserPlugin = require('terser-webpack-plugin')
module.exports = {
  mode:'none',
  entry:{
    'name':'./src/index.js',
    'name.min':'./src/index.js'
  },
  output:{
    filename:'[name].js',
    library:'libName', // 库的全局变量
    libraryExport:'default',
    libraryTarget:'umd' // 库的引入方式
  },
  optimization:{
    minimize:true,
    minimizer:[
      new TerserPlugin({
        include:/\.min\.js$/, //只对.min压缩
      })
    ]
  }
}

// package.json
"main": "index.js",
"scripts": {
  "build": "webpack",
  "prepublish": "webpack"
},

// index.js
if(process.env.NODE_ENV === 'production'){
  module.exports = require('./dist/name.min.js')
}else{
  module.exports = require('./dist/name.js')
}

```

- SSR
  - server side render
  - 优势
    - 减少网络请求
    - 减少白屏时间
    - 对SEO友好
  - 实现思路
    - 服务端使用server的renderToString将组件渲染成字符串，路由返回对应的字符串模板
    - 客户端进行环境判断请求对应的文件
  - 问题
    - nodejs中没有浏览器的window需要hack：if(typeof window === 'undefined'){ global.window = {} }
    - 不兼容的组件需要根据打包环境进行适配
    - http请求需要改写成axios
    - css样式不显示可以采用浏览器端模板占位符替换的方式'<!--HTML_PLACEHOLDER-->'
    - 首屏业务数据也可用模板占位符替换的方式<!--INITIAL_DATA_PLACEHOLDER-->
- 优化构建日志
  - webpack.config.js中stats字段控制统计信息
    - errors-only
    - minimal
    - none | false
    - normal | true
    - verbose 
  - friendly-errors-webpack-plugin
    - stats: 'errors-only'
    - new FriendlyErrorsWebpackPlugin()
    - 日志提示
      - success
      - warning
      - error
- 构建异常和中断
  - 如果没有错误则process.exit(0)
  - 如果有错误则process.exit(非0)，回调函数err.code则为非0数值
  - compiler每次构建结束后都会触发done这个hook
  - hook回调stats.compilation.errors错误对象

# 进阶篇
## 可维护的配置
- 抽离成npm包的意义
  - 通用性
    - 开发者无需关注构建配置
    - 统一团队构建脚本
  - 可维护性
    - 构建配置合理拆分
    - README/CHANGELOG
  - 质量
    - 冒烟测试/单元测试/测试覆盖率
    - CI
- 构建配置管理的可选方案
  - 通过多个配置文件管理对应环境 webpack --config参数控制
  - 构建配置设计成一个库 如neutrino webpack-blocks
  - 设计成一个工具 如create-react-app nwb
  - 配置放在一个文件，通过--env控制
- 构建配置包设计
  - 通过多个配置文件管理对应环境
    - webpack.base.js
      - 资源解析(es6/react/css/less/图片/字体)
      - 样式增强(css前缀自动补齐/px2rem)
      - 目录清理
      - 多页面打包
      - 构建过程日志优化
      - 构建错误捕获和处理
      - css提取成一个单独的文件
    - webpack.dev.js
      - hmr
      - sourcemap
    - webpack.prod.js
      - 代码压缩
      - 文件指纹
      - treeShaking
      - scopeHoisting
      - 速度优化 基础包放cdn
      - 体积优化 代码分割动态import
    - webpack.ssr.js
      - output.libraryTarget
      - css解析
  - npm包
    - 规范(git commit/READMEESLINT/SemVer)
    - 质量(冒烟测试/单元测试/测试覆盖率/CI)
  - 配置组合
    - webpack-merge
    - module.exports=merge(baseConf,devConf)
  - 目录结构
    - test/
    - lib/
    - README.md
    - CHANGELOG.md
    - .eslintrc.js
    - .gitignore
    - package.json
    - index.js
- 构建包使用ESLINT
  - 使用eslint-config-airbnb-base规则集
  - 使用eslint --fix做自动修复
  - package.json "lint":"eslint ./lib --fix"
  - .eslintrc.js
```
module.exports = {
  extends:['airbnb-base'],
  parser:'babel-eslint',
  env:{
    browser:true,
    node:true
  }
}

```

- 冒烟测试smoke testing
  - 指对提交测试的软件在进行详细测试前的预测试
  - 主要目的是暴露基本功能失效的严重问题
  - 执行
    - 构建是否成功 webpack(conf,(err,stats)=>{})
    - 是否生成对应文件 glob.sync(['*.js']).length
- 单元测试和测试覆盖率
  - 框架
    - mocha(单纯的测试框架)+chai(断言库)
    - jasmine/jest 集成框架，开箱即用
  - mocha接入
    - npm i -D mocha chai
    - test.js中编写describe/it/expect
    - package.json中"test":"mocha "
    - npm run test
  - 测试覆盖率
    - npm i -D istanbul
    - package.json中"test":"istanbul cover mocha"
- CI
  - 作用
    - 快速发现错误
    - 防止分支大幅偏离主干
  - 措施
    - 集成前必须跑通测试，否则不予集成
  - 排名
    - travis-ci
    - circle-ci
    - jenkins-ci
- 发布npm包
  - npm login
  - npm version patch|minor|major(自动git提交和tag)
  - npm publish
- git提交规范和changeLog生成
  - 提交规范优势
    - 加快review流程
    - 根据提交生成changeLog
    - 可追溯可回顾
  - 技术方案
    - 目地统一提交日志标准
    - 使用angular的git commit规范
      - 信息分3部分，空行分割
        - 标题(首字母不大写，末尾不要标点)
          - type(scope): subject
        - 主题内容
        - 尾注
      - 提交类型type限制
        - feat：新增feature
        - fix：修复bug
        - docs：仅仅修改了文档
        - style：仅仅修改文件样式，不改变逻辑
        - perf：优化相关，提升体验性能等
        - refactor：代码重构无新功能或bug修复
        - test：测试用例
        - chore：改变构建流程或增加依赖库工具
        - revert：回滚到上一版本
      - scope(可根项目分成大类如docs/components)
      - 主题内容可以详细列明每个修改点影响点
      - 尾注可以增加链接或关闭issue等
    - 日志提交时友好的提示工具:commitize
    - 不符合要求的拒绝提交:validate-commit-msg
    - 统一changeLog文档信息生成:conventional-changelog-cli
  - 本地增加precommit钩子
```
npm i -D husky validate-commit-msg conventional-changelog-cli

"scripts":{
  "commitmsg":"validate-commit-msg",
  "changelog":"conventional-changelog -p angular -i CHANGELOG.md -s -r 0"
}

```
- 语义化版本号
  - semantic versioning
    - major 做了不兼容api的修改
    - minor 做了向下兼容的功能新增
    - patch 做了向下兼容的问题修正
    - major.minor.patch版本号严格递增
  - 优势
    - 避免出现循环依赖
    - 减少依赖冲突
  - 发布重要版本时可以发先行版本
    - alpha 内测版
    - beta 外部小范围测试版，可以加新功能
    - rc 公测版release candidate不会加新功能，主要用排错
    - major.minor.patch-alpha.2

## 构建速度和体积优化
- 初级分析
  - 使用webpack内置的stats
```
"scripts":{
  "build:stats":"webpack --env production --json > stats.json"
}

```
- 速度分析
  - 使用speed-measure-webpack-plugin插件
  - 可以看到每个loader和插件执行耗时和总耗时
```
npm i -D speed-measure-webpack-plugin

const SMWP = require('speed-measure-webpack-plugin')

const smp = new SMWP();

module.exports = smp.wrap(WEBPACK_CONFIG)

```
- 体积分析
  - 使用webpack-bundle-analyzer插件
  - 构建完毕自动在8888端口展示结果
```
npm i -D webpack-bundle-analyzer

const { BundleAnalyzerPlugin} = require('webpack-bundle-analyzer');

plugins:[
  new webpack-bundle-analyzer()
]

```
- 速度提升
  - 使用高版本nodejs和webpack，从底层做了优化
  - 多进程多实例构建 thread-loader 
  - 多进程多实例并行压缩terser-webpack-plugin
```
npm i -D thread-loader terser-webpack-plugin

use:[
  {
    loader:"thread-loader",
    options:{
      workers:3
    }
  },
  "babel-loader"
]

optimization:{
  minimizer:[
    new TerserWebpackPlugin({
      parallel:true // 默认cpu核心2倍-1
    })
  ]
} 

```
- 进一步分包
  - htmlWebpackExternalsPlugin将基础包分离到cdn引入的缺点是可能有很多基础包很多script请求
  - splitChunks缺点是每次都要分析依赖的基础包
  - DLLPlugin分包可以把框架基础包和业务基础包进行预编译，打包成一个文件，然后通过DLLReferencePlugin对manifest.json引用
```
// webpack.dll.js
const path = require('path')
const webpack = require('webpack')
module.exports = {
  entry:{
    library:[
      'react',
      'react-dom'
    ]
  },
  output:{
    filename:"[name]_[chunkhash]_dll.js",
    path:path.join(__dirname,'dll'),
    library:"[name]"
  },
  plugins:[
    new webpack.DllPlugin({
      name:"[name]_[hash]",
      path:path.join(__dirname,'dll/[name].json')
    })
  ]
}

// package.json
"scripts":{
  "dll":"webpack --config webpack.dll.js"
}

// webpack.prod.js

new webpack.DllReferencePlugin({
  manifest: require('./dll/library.json')
})

```
- 缓存
  - 提升二次构建速度
  - 思路
    - babel-loader开启缓存
    - terser-webpack-plugin开启缓存
    - hard-source-webpack-plugin开启缓存
```
// babel-loader
'babel-loader?cacheDirectory=true'

// terser-webpack-plugin
optimization:{
  minimizer:[
    new TerserWebpackPlugin({
      parallel:true,
      cache:true
    })
  ]
}

// hard-source-webpack-plugin
let HSWP= require('hard-source-webpack-plugin')

plugins:[
  new HSWP()
]

```
- 缩小构建目标
  - 尽可能少的构建模块 exclude:'node_modules'
  - 减少文件搜索范围
    - 优化resolve.modules [path.resolve(__dirname,'node_modules')]
    - 优化resolve.mainFields ['main']
    - 优化resolve.extensions ['.js','.json']
    - 优化resolve.alias {react:path.resolve(__dirname,'node_modules/....js')}
- 图片压缩
  - 基于nodejs的imagemin或tinypng的api，使用image-webpack-loader
  - imagemin优势
    - 很多可配置项目
    - 可引入第三方优化插件
    - 可处理多种图片格式
```
{
  loader: 'image-webpack-loader',
  options: {
    mozjpeg: {
      progressive: true,
      quality: 65
    },
    // optipng.enabled: false will disable optipng
    optipng: {
      enabled: false,
    },
    pngquant: {
      quality: '65-90',
      speed: 4
    },
    gifsicle: {
      interlaced: false,
    },
    // the webp option will enable WEBP
    webp: {
      quality: 75
    }
  }
}

```
- treeShaking擦除无用css
  - 默认模块中只要有一个方法被引用整个文件会被打入bundle中，treeShaking是在uglify阶段擦除无用
  - webpack4中production默认开启treeShaking
  - 前提必须是ESM语法，CJS不支持
  - 手动开启需要在.babelrc中设置modules:false
  - 删除无用css2种方案
    - purifyCSS 遍历代码，识别已经用到的css
    - uncss 通过document.querySelector进行识别
  - 实践
    - purgecss-webpack-plugin + mini-css-extract-plugin
- 动态polyfill
  - 减少构建体积
  - polyfill是根据UA下发需要的polyfill来按需加载
  - 可以直接script[src='']来引用polyfill.io服务
  - 也可以基于官方开源的方案自建polyfill的cdn服务
  - ua篡改也可以根据代码结果降级为获取全部polyfill

# 原理篇
## 源码掌握原理
- webpack源码
  - 本质
    - 基于事件流的编程范例，运行一系列的插件
    - compiler = webpack(options)
  - 启动流程
    - npm scripts
    - webpack cli
    - 实际都是执行的node_modules/webpack/bin/webpack.js
  - 主要步骤
```
process.exitCode = 0;
const runCommand = (cmd,args)=>{}
const installed = packageName=>{}
const CLIs = []
const installedCLIs = CLIs.filter()
if(installedCLIs.length ===0 ){

}else if(installedCLIs.length ===1){

}else{

}

```
- webpack-cli源码
  - 主要流程
    - 引入yargs，对命令行进行定制
    - 分析命令行参数，对参数进行转换，组成编译配置项
    - 引入webpack，根据配置项进行编译
  - 不需编译的子命令
    - init
    - migrate
    - add
    - remote
    - serve
    - info
    - generate-loader
    - generate-plugin
- tapable
  - 类似nodejs的EventEmitter,主要控制钩子的发布和订阅
  - 插件系统发布或订阅钩子函数进行处理
  - 9大hook类
    - SyncHook
    - SyncBailHook
    - SyncWaterfallHook
    - SyncLoopHook
    - AsyncParallelHook
    - AsyncParallelBailHook
    - AsyncSeriesHook
    - AsyncSeriesBailHook
    - AsyncSeriesWaterfallHook
  - 绑定和执行
    - 同步:tap和call
    - 异步:tapAsync/tapPromise/tap和callAsync/
    promise
```
const hook1 = new SyncHook(["a1","a2","a3"])

hook1.tap("hook1",(a1,a2,a3)=>{})
hook1.call(1,2,3)
```

- 简易实现webpack
  - babel语法转换 babylon
  - 依赖分析 babel-traverse

## 自定义loader
- loader链式调用和执行顺序
  - loader只是一个导出为函数的js模块
  - module.exports = function(source){return s}
  - 链式调用时从右到左从后到前依次串行执行
  - 函数组合的2种方式
    - unix的pipline从左到右
    - compose的方式从右到左compose=(f,g)=>(...args)=>f(g(...args)) webpack采用此方式
- loader-runner
  - 可用webpack-cli的generate-loader命令
  - loader-runner可以在不安装webpack情况下运行loaders
  - webpack也使用loader-runner执行loader
  - 开发中也可以用loader-runner进行loader的开发和调试
- pitchLoader
  - 可以跳过loader的处理
  - 实现简单，可以更灵活的定义loader的执行
- 实战
```
mkdir raw-loader && cd raw-loader
npm init -y 
npm i loader-runner

/src/demo.txt
demotxt

/src/raw-loader.js
module.exports=function(src){
  const json = JSON.stringify(src)
              .replace(/\u2028/g,'\\u2028')
              .replace(/\u2029/g,'\\u2029')

  // 正常返回 return `export default ${json}`; 
  // 抛错 throw new Error('myError');
  // 也可通过this.callback(error,data1 [,data2...])
  this.callback(null, json);
}

/run-loader.js
const {runLoaders } = require('loader-runner');
const fs = require('fs');
const path = require('path')
runLoaders({
    resource:path.join(__dirname,'./src/demo.txt'),
    loaders:[
        path.join(__dirname,'./src/raw-loader.js')
    ],
    context:{
        minimize:true
    },
    readResource:fs.readFile.bind(fs)
},function(err,res){
    console.log(err,res)
})

node run-loader.js
```
- 高级用法
  - loader-utils的getOptions获取loader参数
```
const loaderUtils = require('loader-utils');
module.exports = function(source){
  const {name} = loaderUtils.getOptions(this);
}
```
  - loader的异步处理
    - 通过this.async()返回的一个函数进行异步处理
    - 第一个参数是error，第二个参数是处理结果
```
module.exports = function(source){
  const callback = this.async();

  fs.readFile(path,'utf-8',(err,data)=>{
    callback(err,data)
  })
}
```
  - loader的缓存
    - webpack中默认开启缓存
    - 可使用this.cacheable(false)关掉缓存
    - 缓存的条件是相同的输入有相同的输出
    - 有依赖的loader无法使用缓存
  - loader的文件输出
    - this.emitFile(outputPath,content)

## 自定义plugin
- 插件
  - 没有loader那样的独立运行环境，只能在webpack内运行
  - 插件需要导出一个类，类需要实现apply方法
  - apply方法接收到一个compiler对象参数
  - 插件内通过compiler的hooks和compilation的hooks做逻辑处理
- 搭建插件运行环境
```
const path = require('path');
const DemoPlugin = require('./plugins/demo.js');
const options = {}

module.exports = {
  entry:{
    lib: path.join(__dirname,'src/lib.js')
  },
  output:{
    path:path.join(__dirname,'dist'),
    filename:'[name].js'
  },
  plugins:[
    new DemoPlugin(options)
  ]

}

// plugins/demo.js
module.exports = class DemoPlugin {
  constructor(options){
    this.options = options
  }
  apply(compiler){
    console.log(this.options);
    compiler.hooks.done.tap('demo',()=>{})
  }
}
```
- 错误处理
  - 接收参数时可以直接throw new Error()
  - hooks处理阶段通过compilation.warnings或errors.push('msg')
- 文件输出
  - 通过compilation.assets配合webpack-sources包
```
const JSZip = require('jszip')
const RawSource = require('webpack-sources').RawSource
const path = require('path')
const zip = new JSZip()
module.exports = class DemoPlugin {
  constructor (options) {
    this.options = options
  }

  apply (compiler) {
    compiler.hooks.emit.tapAsync('zipPlugin', (compilation, cb) => {
      const folder = zip.folder(this.options.name)
      for (const filename in compilation.assets) {
        const source = compilation.assets[filename].source()
        folder.file(filename, source)
      }
      zip.generateAsync({
        type: 'nodebuffer'
      }).then((content) => {
        console.log(compilation.options)
        const outputPath = path.join(compilation.options.output.path, this.options.name + '.zip')
        const relativePath = path.relative(compilation.options.output.path, outputPath)
        compilation.assets[relativePath] = new RawSource(content)
        cb()
      })
    })
  }
}

```
