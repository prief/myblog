---
title: vscode
date: 2019-08-15 22:44:49
tags:
---

# 命令面板
## 常用
- cmd + shift + P
  - shell && install code in PATH
  - transfrom upper|lower
- cmd + P
  - 快速搜索打开文件
- cmd
  - code --help
  - code -r reuseWindow folder|file
  - code -r -g package.json:128 #打开文件128行
  - code -r -d file1 file2 #对比2个文件
  - ls | code -r - #接受管道数据

# 快捷键
## 光标
- option + 左箭头 #单词为单位向左跳动
- option + 右箭头 #单词为单位向右跳动
- cmd + 左箭头  #直接跳到行首
- cmd + 右箭头  #直接跳到行尾
- cmd + 上箭头  #直接跳到文件头部
- cmd + 下箭头  #直接跳到文件尾部
- cmd + Enter  #下面开始一行
- cmd + option + 上下 # 创建多光标
- ctrl + g #跳到指定行
- cmd + shift + O #跳到指定符号

## 文本选择
- 移动光标的同时按住shift进行文本的选择
- cmd + D #多文本选择

## 移动
- option + 上下 #移动当前行

## 复制
- option + shift + 上下 #复制当前行

## 删除
- cmd + delete #删除光标到行首
- cmd + shift + K #删除当前行
- cmd + X #剪切

## 格式化代码
- option + shift + F 

# vscode相关
## 创建codeSnippets
- cmd + shift + P
- user snippets
```
 "Print to console": {
    "prefix": "log",
    "body": [
        "console.log(${1:i});",
        "console.log(${1:i} + 1); // ${1:i} + 1",
        "$2"
    ],
    "description": "Log output to console"
}
```

## .vscode
- settings.json
- tasks.json
- launch.json
- extensions.json

## 语言支持
- json
  - 可自己定义jsonSchema做智能提示
- markdown
  - open preview
- js
  - jsDoc(可做接口文档/编码提示)
  - d.ts(ts接口定义)
  - // @ts-check 开启ts校验逐步过度到ts
  - logpoint 把原本调试时需要的console.log与断点结合 { str + "" }
- emmet
  - emmet.triggerExpansionOnTab: true #输入后按tab自动展开
  - "emmet.includeLanguages": {"vue-html": "html"}
  - ui>li*3
  - div+p
  - div#list>li.list-item
  - #page>div.logo+ul#navigation>li*5>a{Item $}
  - 转到匹配对/删除节点(自动删除开闭节点)

# 插件
## 本质
- 是一个nodejs应用
- 可以操作vscodeAPI进行插件功能实现

## 创建插件
```
npm i -g yeoman generator-code
yo code myExtName
// extension.js
const vscode = require("vscode");
exports.activate = function activate(ctx){}
exports.deactivate = function deactivate(){}

//package.json
engines : { "vscode": "^1.29.0" }
"activationEvents": [ "onCommand:extension.sayHello" ]
"contributes": { "commands": [ {"command": "extension.sayHello", "title": "Hello World" }]}

```