---
title: cli
date: 2019-08-17 16:46:23
tags:
---

# webpack
## loader
- 三种使用方式
  - webpack.config.js
  - 内联在import语句中
  - 在cli命令中指定
- 常用loader
  - babel-loader
  - style-loader 在html中注入style标签
  - css-loader 解析@import url()等
  - postcss-loader
  - sass-loader
  - html-loader
  - vue-loader
  - file-loader
  - url-loader

## plugin
- 常用plugin
  - HtmlWebpackPlugin
  - CommonsChunkPlugin
  - DefinePlugin
  - DllPlugin
  - ExtractTextWebpackPlugin
  - HotModuleReplacementPlugin
  - UglifyjsWebpackPlugin
  - CopyWebpackPlugin

# 工程化模版
## 初始化
- mkdir template && cd template && npm init -y
- npm i -D webpack webpack-cli vue-loader vue-template-compiler html-webpack-plugin css-loader style-loader sass-loader sass postcss-loader  postcss-preset-env url-loader file-loader @babel/core @babel/preset-env babel-loader webpack-dev-server connect-multiparty mockjs concurrently
- npm i vue vue-router @babel/polyfill axios
- 浏览器支持列表
  - package.json.browserslist
  - .browserlistrc
  - browserlist
  - 环境变量BROWSERLIST 
- .babelrc
- postcss.config.js
- webpack.config.js

## 模块化
- 意义
  - 避免命名/变量冲突
  - 更清晰的依赖关系
  - 可维护
  - 可复用
  - 降低复杂度
- 主流实现
  - AMD
    - 异步加载，适合浏览器端
    - require
    - define([deps...],(deps...)=>{return {}})
  - CommonJS
    - 同步加载，适合服务端，因为大都在本地
    - require
    - module.exports | exports.
  - ES6
    - js语言层面支持的模块化，可做静态依赖分析，适合多端
    - import
    - export

## 本地开发环境
- npm install --save-dev webpack-dev-server
- "start": "webpack-dev-server --open"
- HMR
  - hotModuleReplacement
  - 能生效是因为模块实现了HMR接口
```
if (module.hot) {
  module.hot.accept("./print.js", function() {
    console.log("接收更新后的模块");
    print();
  });
}

```
- sourceMap
  - source-map 适合生产环境，映射关系完整但运行慢
  - eval-source-map 适合开发环境，只映射到行但运行快

## 本地mock
- 服务 express
- 路由 url与数据的路由绑定
- 数据模拟 mockjs
  - 数据模版定义规范DTD(data template definition)
    - 属性名 与规则之间用|分割
    - 生成规则 依赖属性值的类型，是可选的
    - 属性值 可以有@占位符，指定了最终值的类型和初始值
    - 例如：'name|rule': value
  - 数据占位符定义规范DPD(data placeholder definition)
  - 核心api
    - mock 将模版输出为最终的数据
    - random 生成随机数据
- concurrently
  - 同一终端同时运行多个npm命令，不管是否同一进程

## 代码检查
- eslint
  - npm install -D eslint eslint-loader eslint-plugin-vue babel-eslint eslint-friendly-formatter
  - plugins指定需要的插件名称，可以忽略eslint-plugin-
  - 默认使用espree解析器，因为有新的语言特性需要指定为babel-eslint
  - 换解析器需要在parserOptions中，防止全局替换导致其他插件失败
  - es6模块的sourceType为module
  - 禁用eslint规则
    - 文件开头/* eslint-disable */禁用整个文件的检查
    - 行// eslint-disable-line 禁用行检查
    - 行// eslint-disable-line no-console 禁用console规则
  - 自定义规则
    - extends: eslint:recommended
    - rules:{ no-console: off, quotes:["wanr","single"],indent:["error",2]}
    - 数组值第一项表示级别，默认是error
- stylelint
  - npm install -D stylelint stylelint-webpack-plugin
  - .stylelintrc.js
  - 禁用规则
    - /* stylelint-disable unit-whitelist*/ 禁用unit规则校验
    - /* stylelint-disable */ 禁用所有规则校验
  - 自定义规则
    -  extends: "stylelint-config-standard"
    -  rules: {"color-no-invalid-hex": true}

## 雪碧图
- npm install -D webpack-spritesmith
- resolve.modules如果是相对路径则按照规则一级一级向上查找，如果是绝对路径不会向上查找
- 使用
  - @import "~sprite.scss"
  - .icon-picName{@include sprite($picName)}
- 自定义生成2x样式表
  - 官网的templateFunction
  - customTemplates: {function_based_template: templateFunction},
  - target.css = [[],path.resolve(__dirname, "src/assets/generated/sprite.scss")]
  - 使用
    {% codeblock %}
    @import '~sprite2.scss';
    <li class="ico ico-picName"></li> 
    {% endcodeblock %}

## 按浏览器构建
- 新版本浏览器
  {% codeblock %}
  <script type="module"></script> 可直接加载ES6
  <link rel="modulepreload"></link> 预加载
  {% endcodeblock %}
- 老版本浏览器
  {% codeblock %}
  <script nomodule>加载旧版本js，新版本浏览器会忽略该引用
  {% endcodeblock %}

## 按环境构建
- development
- test
- production
- 插件
  - npm install --save-dev extract-text-webpack-plugin
  - npm install --save-dev optimize-css-assets-webpack-plugin
  - npm install terser-webpack-plugin --save-dev
  - HashedModuleIdsPlugin 避免不必要的hash变化

## 集成调试工具
- weinre / spy-debugger
- vconsole
  - npm install vconsole
  - DebugPlugin.js里面去实现 debugtool插件
  - vconsole.js 里面去new Vconsole
  - 可以单独发一个npm包 debugtool-webpack-plugin

## 单元测试
- chai作为断言库
- Mocha编写测试用例、测试框架
- Karma测试过程管理TestRunner及启动浏览器和生成测试报告
- npm install --save-dev karma mocha karma-mocha karma-chrome-launcher karma-webpack karma-sourcemap-loader karma-spec-reporter chai @vue/test-utils karma-coverage babel-plugin-istanbul cross-env
- 覆盖率
  - 语句覆盖率
  - 分支覆盖率
  - 函数覆盖率
  - 行覆盖率

## e2e测试
- npm install --save-dev nightwatch chromedriver
- npm install --save-dev geckodriver # firefox
- npm install --save-dev cross-spawn # 启动子进程
- npm install --save-dev nightwatch-html-reporter
- nightwatch接口
  - 断言相关
    - expect.element()
    - .value
    - .text
    - .equal(val)/.contain(val)/.match(val)
  - 协议映射相关
    - .click()
    - .url()
    - .setValue()
    - .pause()
    - .waitForElementVisible()

## 性能优化
- 缓存
  - 开启cache后，模块和生成的chunk如果内容不变则直接用cache，主要解决增量构建过程的性能
  - HardSourceWebpackPlugin，缓存编译过程中间结果 npm i -D hard-source-webpack-plugin
- 多线程
  - HappyPack
  - thread-loader #官方推荐
- 预先编译
  - DllPlugin 把基本不变的预先打包出单独dll文件
  - DllReferencePlugin 配置在文件中引用dll文件
  - 运行一次npm run dll后不需在运行除非dll包有更新
  - npm i -D add-asset-html-webpack-plugin dll文件提前插入html
  - 与splitChunks功能类似，可以去除splitChunks

## 部署
- ecs
  - const { spawn } = require("child_process");
  - ssh免密登陆
- oss
  - vinyl-fs
  - vinyl-ftp

# cli
## 聚合配置并模版化
- app.config.js 聚合用户自定义配置
- 相应的配置都要从app.config.js中获取

## handlebars模版化
- template 存放模版
- meta.js 配置入口
  - helpers
    - {% raw %}语法{{#helperName}}...{{/helperName}}{% endraw %}
    - 内置helper if,上面都语法可根据helperName的truthy进行判断
    - 自定义registerHelper
  - prompts
  - filters
  - completeMessage

## cli
- 工作流程
  - 运行命令 mc init weex pro
  - 下载模版 
  - 交互配置信息
  - 渲染模版
- mc
  - 1个主命令
  - 2个子命令
    - mc init
    - mc help 默认子命令
- 主命令开发
  - npm i commander
  - npm link #将包链接到全局
- 子命令模块
  - commander
  - chalk
  - inquirer
  - download-git-repo
  - rimraf
  - user-home
  - ora
  - metalsmith  
    - 文件处理工具,从哪里读，做什么处理，写到哪里去
    - use方法绑定插件
    - source设定源文件目录
    - destination指定文件写入的目录
    - clean(true|false)写入前是否删除原来已经存在的文件
    - build完成对文件的处理接收回调
    - metadata读取全局数据对象
  - handlebars 
  - async 
  - consolidate
    - 各种模版引擎的整合库
    - 还需要引用需要的模版引擎库

```
bin/mc

#!/usr/bin/env node
const program = require('commander');
program
  .version(require('../package').version)
  .usage('<command> [options]')
  .command('init', 'generate a new fe project');
program.parse(process.argv);


```