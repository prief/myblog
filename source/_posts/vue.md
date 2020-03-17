---
title: vue
date: 2020-02-17 12:10:54
tags:
---

## cli
- features
  - babel
  - ts
  - pwa
  - router
  - vuex
  - cssPreProcessor
  - linter/formatter
  - unitTesting
  - e2eTesting
- dir
  - node_modules
  - public
    - favicon.ico
    - index.html
  - src
    - assets
    - components
    - router
    - store
    - views
    - main.js
    - App.vue
  - tests
    - unit
    - e2e
  - .gitignore
  - .postcssrc.js
  - .eslintrc.js
  - .browserslistrc        
  - babel.config.js
  - package.json
  - package-lock.json
  - README.md
- ext
  - npm/yarm
  - webpack
  - 环境配置
  - 接口配置
  - 单页应用
  - 多页应用
  - 实用工具
  - 开发技巧

## browserlist
- 目标浏览器列表，给所有插件共享如autoprefixer等 
- 可以写单独的.browserslistrc文件也可写在package.json.browserslist中
- 最终的数据来源于CanIUse
- 可以在项目中运行npx browserslist进行查看

## env环境
- 环境
  - 公共配置
  - 开发环境配置
  - 测试环境配置
  - 生产环境配置
- 推荐使用js文件进行config配置，可以支持动态参数/变量等

## 单页应用配置
- 路由配置
  - 如果路由存在二级目录，需要添加base属性值否则默认/
  - 默认路由模式为hash，可修改history
  - 路由组件可以进行懒加载 component:()=>import(/* webpackChunkName: "name" */ "path2vue")
- vuex配置
  - 通过 actions 异步提交 mutations 去 修改 state 的值并通过 getter 获取
  - 大型项目目录
    - index.js #组装模块并导出store
      - state
      - mutations
      - actions
      - getters
      - modules
    - actions.js #根级别的action
    - mutations.js #跟级别的mutation
    - modules
      - moduleA.js A模块
        - state
        - mutations
        - actions
        - getters
      - moduleB.js B模块
- 接口配置
  - 目录
    - src
      - services
        - index.js #接口封装
        - moduleA.js #模块A接口
        - moduleB.js #模块B接口
  - devServer配置proxy
    - moduleX.js中一般不配置host
    - devServer.proxy.PATTERN.target代理目标地址
- 公共设施配置
  - 封装公共的方法等
  - 目录
    - src
      - common
        - index.js #公共配置入口，统一向外暴露
        - validate.js #表单验证
- 多页应用配置
  - entries
  - htmlWebpackPlugin
  - 上面2个分开的配置也可以统一使用pages配置
  - 多页间路由跳转必须使用location的原生方法，可以封装到Vue原型链上
  - 多页间路由跳转需要注意historyApi前后端配置
  - 模版配置时需要注意inject的值防止重复注入
  - 模块配置可以通过htmlWebpackPlugin.options添加自定义配置
- webpack配置
  - alias解决复杂路径问题，css/html中需要~
  - CompressionWebpackPlugin进行压缩如gzip

## 编码
- 基础
  - 过多的if/switch判断可以用对象代替
  - 路由跳转使用name而不是path，方便维护
  - key值推荐使用数组中不会变化且唯一的值
  - 使用computed代替watch
  - 统一管理缓存变量，增加types.js把字符串变量化
  - 避免for/in遍历数组，防止数组原型被修改
- api
  - Vue.config.performance=true;开启性能监控，配合VuePerformanceDevtool插件分析
  - 捕获异常
    - try/catch
    - window.onerror
    - Vue.config.errorHandler=(err,vm,info)=>{}
    - watch:{obj:{handler(){},deep:b,immediate:b}}
- 可复用
  - 步骤
    - 出现重复代码
    - 封装成一个变量
    - 封装函数
    - 封装组件
    - 封装插件
  - 组件
    - 封装
      - 全部封装
      - 插槽封装
    - 分类
      - 容器组件，主要处理组件逻辑
      - 展示组件，主要处理组件样式
  - 插件
```

/* toast.js */
import ToastComponent from './toast.vue' // 引入组件

let $vm

export default {    
    install(Vue, options) {
        
        // 判断实例是否存在
        if (!$vm) {            
            const ToastPlugin = Vue.extend(ToastComponent); // 创建一个“扩展实例构造器”
            
            // 创建 $vm 实例
            $vm = new ToastPlugin({                
                el: document.createElement('div')  // 声明挂载元素          
            });            
            
            document.body.appendChild($vm.$el); // 把 toast 组件的 DOM 添加到 body 里
        } 
        
        // 给 toast 设置自定义文案和时间
        let toast = (text, duration) => {
            $vm.text = text;
            $vm.duration = duration;
            
            // 在指定 duration 之后让 toast 消失
            setTimeout(() => {
                $vm.isShow = false;  
            }, $vm.duration);
        }
        
        // 判断 Vue.$toast 是否存在
        if (!Vue.$toast) {            
            Vue.$toast = toast;        
        }        
        
        Vue.prototype.$toast = Vue.$toast; // 全局添加 $toast 事件
    }
}



# 入口文件注册
import Toast from '@/widgets/toast/toast.js'

Vue.use(Toast); // 注册 Toast

# 使用
this.$toast('Hello World', 2000);
```

- 开发工具
  - vueDevtool
  - vuePerformanceDevtool
  - pageSpeedInsights
  - JSONViewer
  - webpack-bundle-analyser
  - safari调试ios页面
  - chrome://inspect调试安卓页面