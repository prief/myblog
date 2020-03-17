---
title: web-sec
date: 2020-02-22 12:36:13
tags:
---

## 基础
### 安全问题
- 服务端
  - SQL注入
  - 命令注入
  - 文件上传
  - 暴力破解
- 客户端
  - XSS
  - CSRF
  - 点击劫持
  - URL跳转
  - 钓鱼 仿冒官网进行信息获取
  - 暗链 植入灰黑产业的链接，用户不可见，但可以提高排名
  - webshell 通过网页部署后门程序，执行非法命令

### web基础
- URL规范
  - schema://
  - username:password@
  - hostname:port/
  - path?
  - querystring#
  - anchor
- HTTP报文
  - 头行
  - 头部
  - 空行
  - 主体
- SQL
  - order by FIELD/index ASC/DESC，默认升序
  - select * from t1 union select * from t2 默认合并重复
  - select * from t1 union all select * from t2 不合并重复
  - 注释 
    - # 内容 
    - -- 内容 
    - /* 内容 */
  - 内置命令
    - source file #导入sql文件
    - select current_user #显示当前用户
    - select load_file(PATH) #显示文件内容
- PHP
  - 脚本范围 <?php 内容 ?>
  - 注释
    - // 内容
    - # 内容
    - /* 内容 */
  - echo 可以用，一次可以输入多个变量
  - print 一次只能输出一个变量，有返回值
  - $var表示变量，区分大小写
  - 引入外部php文件
    - include 当脚本错误时，只是警告，继续执行
    - require 当脚本错误时，停止脚本执行

## 安全漏洞
### XSS
- cross site scripting 跨站脚本攻击
- 分类
XSS类型 ｜ 存储型 ｜ 反射型 ｜ DOM型
--- ｜ --- ｜ --- ｜---
触发过程 ｜ 黑客构造XSS后用户访问XSS页面 ｜ 用户访问XSS页面 ｜ 用户访问XSS页面
数据存储 ｜ 数据库 ｜ URL ｜ URL的hash中
谁来输出 ｜ 后端WEB程序 ｜ 后端WEB程序 ｜ 前端JS
输出位置 ｜ HTTP响应 ｜ HTTP响应 ｜ 动态构造的DOM

### CSRF
- cross site request forgery 跨站请求伪造
- 在用户登陆的情况下以不知情的方式执行非法操作

### 点击劫持
- 通过覆盖不可见的iframe引导用户点击操作的恶意行为
- 也称UI覆盖攻击

### URL跳转漏洞
- 借助未验证的URL跳转，把非法的URL嫁接到安全的URL后引导用户到非法网站
- 如img.alipay.com/sys/html/wait.html?goto=恶意网址
- 现在的短网址更隐蔽
- 方式
  - header头的location
  - meta标签的refresh,url
  - js的location.href 

### SQL注入
- SQL injection
- 万能密码
  - 利用' --进行字符串的闭合和注释进行攻击
  - 本质就是SQL注入的利用方式
- 原理
  - 数据当作了代码来执行
  - 本质就是数据和代码未分离

### 命令注入
- 必要条件
  - 调用可执行系统命令的函数(system/eval...)
  - 函数或函数的参数可控
  - 拼接命令注入

### 文件操作漏洞
- 上传漏洞
  - 正常的有上传头像、附件的上传功能
  - 上传webShell
  - 上传木马
- 下载漏洞
  - 下载系统任意文件
  - 下载程序代码