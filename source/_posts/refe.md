---
title: refe
date: 2019-12-08 09:52:17
tags:
---

### 重学
#### 课程目录
- 重学
  - 学习方法
  - 构建知识体系
  - 工程体系
- 重学js
  - 编程语言通识与js语言设计
  - 词法/类型
  - 表达式/类型转换
  - 语句
- 重学浏览器工作原理
  - http协议/语法词法分析
  - css计算/排版/渲染/合成
- 重学css
  - css基本语法
  - 排版与排版相关属性/绘制与绘制相关属性
  - css动画
- 重学html
  - html语言与扩展
  - html语义
- 重学浏览器api
  - DOMAPI/事件机制
  - 其他API/总结
- 编程与算法训练
  - TicTacToe/井字棋
  - 寻路问题
  - 点击区域与括号匹配wildcard
  - promise与异步编程
  - 正则表达式与文本处理
  - proxy与双向绑定
  - 使用Range实现DOM操作
  - 使用CSSOM实现视觉交互
  - 解析一个四则运算的表达式
- 组件化
  - 组件的基本知识/轮播组件
  - 手势与动画
  - 为组件添加JSX
  - 轮播组件的改造(生命周期/状态/属性/事件)
  - Tab组件和List组件
  - vue风格的SFC
- 工具链
  - 整体理解工具链的设计
  - 目录结构和初始化工具
  - 设计并实现一个构建工具与调试工具
  - 设计并实现一个单元测试工具
- 发布系统
  - 实现一个线上web服务
  - 实现一个发布系统
  - gitHook与lint
  - 使用无头浏览器与DOM检查

#### 前端技能模型
- 基础能力(刻意练习)
  - 编程能力，解决业务难的问题
  - 架构能力，解决业务大的问题
  - 工程能力，解决人协作的问题
- 前端知识(建立知识体系)
  - html
  - css
  - js
- 领域知识(实践中学习)

#### 学习方法
- 整理法
  - 关系
    - 顺序关系
      - 编译1 词法分析
      - 编译2 语法分析
      - 编译3 代码优化
      - 编译4 代码生成
    - 组合关系(css rule)
      - 选择器
      - 属性
      - 值
    - 维度关系
      - 文法
      - 语义
      - 运行时
    - 分类关系
  - 完备性
- 追溯法
  - 源头(论文/杂志)
  - 标准和文档
    - https://www.w3.org/
    - https://whatwg.org/
    - https://developer.mozilla.org/
    - https://developer.apple.com/
    - https://docs.microsoft.com/
    - http://www.ecma-international.org/publications/standards/Ecma-262.htm
  - 大师

#### 面试
- 面试题
  - 广度
  - 深度
  - 区分度
- 面试过程
  - 打断，打断是一种提示
  - 争论，争论的技巧/也可能是压力面试
  - 难题，缩小规模/展现分析过程
- 题目类型
  - 项目性
  - 知识性
  - 开放性
  - 案例性
  - 有趣的
- 知识体系比知识点更重要

### 构建知识体系(前端技术)
#### HTML
- HTML as 通用的计算机编程语言
  - 词法
  - 语法
- HTML as SGML(Standard Generalized Markup Language 标准通用标记语言)
  - DTD 
    - Document Type Definition 文档类型定义 
    - https://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd
  - ENTITY 
    - 实体(html下就是&符号后面的字符)
    - 两种形式
      - &entity_name;  // Character mnemonic entities
        - HTMLlat1.ent(https://www.w3.org/TR/html4/HTMLlat1.ent)
        - HTMLsymbol.ent(https://www.w3.org/TR/html4/HTMLsymbol.ent)
        - HTMLspecial.ent(https://www.w3.org/TR/html4/HTMLspecial.ent)
      - &#entity_number; 
- HTML as XML
  - namespace
    - svg
    - mathml
    - aria (Accessible Rich Internet Application) 通过role属性实现
  - tag
    - a
    - abbr
    - address
    - area
    - article
    - aside
    - audio
    - b
    - base
    - bdi
    - bdo
    - blockquote
    - body
    - br
    - button
    - canvas
    - caption
    - cite
    - code
    - col
    - colgroup
    - data
    - datalist
    - dd
    - del
    - details
    - dfn
    - dialog
    - div
    - dl
    - dt
    - em
    - embed
    - fieldset
    - figcaption
    - figure
    - footer
    - form
    - h1
    - h2
    - h3
    - h4
    - h5
    - h6
    - head
    - header
    - hgroup
    - hr
    - html
    - i
    - iframe
    - img
    - input
    - ins
    - kbd
    - label
    - legend
    - li
    - link
    - main
    - map
    - mark
    - menu
    - meta
    - meter
    - nav
    - noscript
    - object
    - ol
    - optgroup
    - option
    - output
    - p
    - param
    - picture
    - pre
    - progress
    - q
    - rp
    - rt
    - ruby
    - s
    - samp
    - script
    - section
    - select
    - slot
    - small
    - source
    - span
    - strong
    - style
    - sub
    - summary
    - sup
    - table
    - tbody
    - td
    - template
    - textarea
    - tfoot
    - th
    - thead
    - time
    - title
    - tr
    - track
    - u
    - ul
    - var
    - video
    - wbr

#### JS
- Grammar 语法
  - Lex 
    - WhiteSpace
      - 空格
      - 零宽空格\uFEFF 'var\uFEFFa=1'
    - LineTerminator
    - Comment
    - Token
      - Identifier
      - Keywords
      - Punctuator 标点符号
      - NumericLiteral 
      - StringLiteral
      - RegularExpressionLiteral
      - Template
  - Syntax
    - Atom
    - Expressions
    - Structure
    - Scripte&Module
- Semantics 语义
- Runtime 运行时
  - Type
    - Null
    - Undefined
    - String
    - Number
    - Boolean
    - Object
    - Symbol
    - 内部类型
      - Reference
      - CompletionRecord
  - 执行过程
    - Job
    - Script&Module
    - Promise
    - Function
    - Statement
    - Expression
    - Literal
    - Identifier

#### CSS
- 语法/词法
- @规则
- 普通规则
  - 选择器
    - 简单选择器
      - *
      - tagname
      - #id
      - .cls
      - [attr]
    - 复合选择器
      - 简单选择器的各种组合
    - 复杂选择器
      - 空格
      - >
      - +
      - ~
    - 选择器列表
      - , 
  - Property
  - Value
- 机制
  - 排版
  - 伪元素
  - 动画
  - 优先级

#### API
- browser
  - BOM（进化为WebPlatformAPI）
  - DOM
    - Nodes
      - Document
      - DocumentType
      - DocumentFragment
      - Element
      - Text
      - Comment
      - ProcessingInstruction <?
    - Ranges
    - Events
- node
- 小程序

#### 其他
- 参考链接
  - https://www.ecma-international.org/
  - https://www.ecma-international.org/publications/files/ECMA-ST/ECMA-262.pdf
  - https://www.caniuse.com/
- 规范制定过程
  - WD WorkingDraft工作草案
  - CR CandidateRecommendation候选推荐标准
  - PR ProposedRecommendation建议推荐标准
  - REC Recommendation推荐标准
  - Retire 退休
- 前端兼容性
  - 主要是测试
  - 实践中测试TOP30

### 工程体系
#### 优秀工程师
- 领域知识
- 能力/潜力
  - 编程能力
  - 架构能力
  - 工程能力
- 职业规划
- 成就

#### 职业规划
- 成长
- 成就
  - 业务型成就
    -  业务目标/指标
    -  技术方案/指标
    -  实施方案/进度
    -  结果评估/总结
  - 技术型成就
    - 目标：技术难题
    - 方案与实施：依靠编程/架构能力形成解决方案
    - 结果：难题解决
    - 案例：浏览器插件识别图片内容
  - 工程型成就
    - 目标：质量和效率
    - 方案与实施：规章制度/工具/库/系统
    - 结果：线上监控
    - 案例：XSS攻击的预防
- 晋升

#### 数据驱动的思考方式
- 目标
  - 分析业务目标
  - 制定数据指标
- 现状
  - 采集数据(performance/window.onerror)
  - 建立数据展示系统(echart/antV)
- 方案
  - 设计技术方案
  - 预估数据/效果
- 实施
  - 小规模实验
  - 形成制度推广落地
- 结果
  - 统计最终结果
  - 总结汇报

#### 工具链
- 作用
- 分类
  - 脚手架 init/add
  - 本地调试 run/build
  - 单元测试 test
  - 发布 publish
- 体系设计
  - 版本问题
  - 数据统计

#### 持续集成
- 客户端软件CI
  - DailyBuild
  - BuildVerificationTest
- 前端CI
  - Check-in Build
  - Lint + RuleCheck

#### 技术架构
- 服务端架构解决大量用户访问带来的复杂性
- 客户端架构解决软件需求规模带来的复杂性
  - 复用，解决重复劳动的问题
    - 库(URL/AJAX/ENV)可复用的代码，代码层面
    - 组件(Tab/轮播)UI上多次出现的元素，UI层面
    - 模块(登录)经常被业务使用的区块，业务层面

#### 其他
- UV（unique visitor 独立访客,一般以cookie为标示）
- PV（page view）
- ctr（click-through rate）
- 判断用户活跃度算法:日活/月活
- 前端推荐项目
  - mocha
  - spritejs 跨平台的高性能图形系统
  - eslint
  - phantomjs
- 前端图片处理
  - 前端引起性能问题的主要就是图片(可通过devtool的memory分析)，可以客户端承接图片的处理
  - 压缩50%+锐化
  - 根据设备(1x/2x/3x)和网络(3G/4G/WIFI)做不同策略
- URI标准组织https://tools.ietf.org/html/rfc3986
- AJAX库
  - 裸体库不安全
  - 防重放/窃取/跨域
  - 带时间戳/hash
  - 拉起登录
- AB测试
  - AB发布(cookie标识)
    - 服务端AB分桶
    - 前端AB入口
  - AB埋点

### 编程语言通识与js语言设计
#### 编程语言通识
- 语言按语法分类
  - 非形式语言
    - 中文
    - 英文
  - 形式语言（乔姆斯基谱系）
    - 计算机科学中刻画形式文法表达能力的一个分类谱系
    - 层次
      - 0-型文法：无限制文法或短语结构文法
      - 1-型文法：上下文相关文法
      - 2-型文法：上下文无关文法
      - 3-型文法：正则文法 
    - 用途
      - 数据描述语言 JSON
      - 编程语言
    - 表达方式
      - 声明式 JSON
      - 命令式     
- 产生式（BNF）
  - 用尖括号扩起来的名称表示语法结构名
  - 语法结构
    - 基础结构（也称终结符，引号和中间的字符）
    - 复合结构（也称非终结符）
  - 可以有括号
  - * 表示重复多次
  - | 表示或
  - + 表示至少一次
  - 运算优先级高的先定义，先定义的也可以引用后续定义的语法结构
- 产生式理解乔姆斯基谱系（?表示任意）
  - 0-型文法：无限制文法或短语结构文法 ?::=?
  - 1-型文法：上下文相关文法 ?<A>?::=?<B>?
  - 2-型文法：上下文无关文法 <A>::=?
  - 3-型文法：正则文法 <A>::=<A>? (左结合,js中**为右结合即从右到左算)
- 图灵完备性（世界上一切不都可被计算）
  - 命令式-图灵机
    - goto
    - if/while
  - 声明式-lambda
    - 递归
- 类型系统
  - 动态类型系统与静态类型系统
  - 强类型与弱类型
  - 复合类型
    - 结构体
    - 函数签名(入参和返回值必须匹配(T1,T2)=>T3)
  - 子类型
    - 逆变
    - 协变
- 一般的命令式编程语言分5层
  - atom
    - identifier
    - literal
  - expression
    - atom
    - operator
    - punctuator
  - statement
    - expression
    - keyword
    - punctuator
  - structure
    - function
    - class
    - process
    - namespace
  - program
    - program
    - module
    - package
    - library 

#### 参考名词
- 巴科斯诺尔范式
  - 巴科斯诺尔范式Backus-Naur Form(BNF)，由两位首先引入描述计算机语法的符号集
  - 表示上下文无关文法的语言
- 图灵
  - 图灵机
    - TuringMachine
    - 将人的计算行为抽象掉的数学逻辑机
    - 可以等价于任何有限逻辑数学过程的终极强大逻辑机器
  - 图灵完备性
    - 如果一系列的操作数据的规则可以用来模拟单带图灵机则是图灵完全的
    - 简单来说能完全模拟图灵机的就是完备的
    - 大部分编程语言都是图灵完备
    - 某些语言为了解决特定问题就不是图灵完备(sql/正则)
    - 通常支持如下功能即为图灵完备
      - 分支/循环/跳转/递归
      - 数组状数据结构
- 名词
  - 终结符：最终在代码中出现的字符
  - 产生式：计算机中编译器将源程序经过词法分析LexicalAnalysis和语法分析SyntaxAnalysis后得到的一系列符合文法规则(BNF)的语句
  - 动静态
    - 动态语言是运行时确定数据类型 runtime
    - 静态语言是编译时确定数据类型 compiletime
  - 强弱类型
    - 强类型语言是一个变量被指定了某个数据类型则不会隐式转化，如果不强制转换则类型不变，是类型安全的语言
    - 弱类型语言是可以隐式转换，但执行效率和严谨性差
  - 协变与逆变
    - 允许一个函数类型中，返回值类型是协变的，参数类型是逆变的
    - 返回值类型是协变的意思是A<=B 就意味着(T->A) <= (T->B)
    - 参数类型是逆变的意思是A<=B 就意味着(B->T) <= (A->T)
  - 自举
    - 自己的编译器可以自行编译自己的编译器
    - 第一个编译器肯定是用别的语言写的如Python,后面的版本才能谈及自举，如python的解释器pypy就是自举
    - 自举越早对编程语言自身发展越有利，最好自身定型前完成自举

### js词法与类型
#### 预习
- unicode
  - https://www.fileformat.info/info/unicode/
  - https://home.unicode.org/
- 浮点数计算误差
  - https://github.com/camsong/blog/issues/9
  - js的浮点数和整数都只有一个类型Number，都会转成二进制存储
  - 遵循IEEE754标准，使用64位固定长度来表示即标准的double双精度浮点数（相关的是float32单精度）
  - 好处是可以归一化处理整数和小数，节省存储空间
  - 64bit分为三部分
    - 符号位S：第一位是符号位sign，0表示正数，1表示负数
    - 指数位E：中间的11位存储指数exponent，用来表示次方数
    - 尾数位M：最后的52位存储尾数mantissa，超出的部分自动进一舍零
  - 科学技术法
    - 其中指数部分可以为负
    - 所以11位即0-2047为表示负数取中间数1023，前面的-1023即为负数，后面的-1023即为正数
    - 科学计数法整数部分只能是1所以可以省去，只保留后面的小数部分，在公式中再加上1
    - 所以最终的公式 v=(-1) ** S * 2 ** (E-1023) * (M+1)
  - 大数危机
    - 产生的大数运算也会有大数危机所以会有Infinity
    - 确有需求可以当作字符串来处理，重新实现计算逻辑，但效率会低（已经实现的库bignumber.js）
    - 所以js标准出现了BigInt类型，希望彻底解决大数危机问题
  - 两个函数（计算过程中不要使用，只在最后把结果展示为字符串时使用）
    - toPrecision 处理精度，从左到右第一个不为0的数开始
    - toFixed 小数点后指定位数取整，从小数点开始
  - 解决方案
    - 数据展示类可用 +parseFloat(num.toPrecision(precision=12))
    - 数据运算类要转成整数后进行计算
    - https://github.com/dt-fe/number-precision
- 正则
  - ? 出现在量词*?+{}后面表示非贪婪匹配
  - (?:x) ?:表示不捕获
  - x(?=y) 先行断言，即x后面有y才匹配x
  - (?<=y)x 后行断言，即x前面有y才匹配x
  - x(?!y) 正向否定查找，即x后面不是y才匹配x
  - (?<!y)x 反向否定查找，即x前面不是y才匹配x
  - 正则中的\1反向引用产生回溯，会造成原本是m+n的时间复杂度变更很高的复杂度
- 字符编码
  - 字符集把字符编码指定为集合中的某个对象以便存储和网络传输
  - unicode
    - 万国码，对世界上大部分文字系统做了编码，可容纳100多万个符号
    - 只是一个符号集，规定了符号的二进制代码即码点code point（正整数），却没有规定二进制代码如何存储
    - unicode规范定义每一个文件的最前面分别加入一个表示编码顺序的字符(FEFF|FFFE)来作为BOM(Byte Order Mark)从而区分字节序
      - 头两个字节是FEFF就表示采用大头方式 big endian(BE)
      - 头两个字节是FFFE就表示采用小头方式 little endian(LE)
      - 前面3个字节是EF BB BF表示采用UTF-8 with BOM 编码
  - ASCII American Standard Code for Information Interchange美国信息交换标准代码，基于拉丁字母
  - 空格
    - NBSP no-break space禁止html中两个字之间的断行,unicode为U+00A0
    - ZWSP zero width no-break space不可打印的字符,unicode为U+FEFF，主要用作上面的BOM
  - notepad.exe编码方式
    - ANSI是windows默认的编码方式，针对英文用ASCII，简体中文用GB2312，繁体中文用BIG5
    - Unicode是使用UCS-2(Universal Character Set-2即用2个字节编码)编码，即直接用两个字节存入字符的unicode码值，采用little endian
    - Unicode big endian
    - UTF-8(UCS Transformation Format-8即以8位为单元对UCS进行编码)

#### 词法与类型
- 词法
  - js中可以写任意unicode码点（code point 正整数），但最佳实践推荐ASCII内
  - 常用码点方法
    - CJK(chinese japanese korean) U+4E00-U+9FFF(常用的中文字，但也不全)
    - String.fromCodePoint(码点的十进制数字)
    - '字符'.codePointAt()返回码点的十进制数字
    - '字符'.codePointAt().toString(16)返回16进制数字，可用U+FFFF或\uFFFF表示
- InputElement
  - WhiteSpace
    - <TAB> tabulation
    - <VT> virtical tabulation
    - <FF> form feed
    - <SP> space
    - <NBSP> no break space
    - <ZWNBSP> zero width no break space
    - <USP> unicode space
  - LineTerminator
    - <LF> line feed U+000A
    - <CR> carriage return U+000D
  - Comment
    - //
    - /**/
  - Token
    - Punctuator 标点符号
    - IdentifierName
      - Keywords
      - Identifier
      - FutureReservedKeywords
    - Literal
      - Number
      - String
      - Boolean
      - Object
      - Null
      - Undefined
      - Symbol
    - Template
- Number
  - float64
    - IEEE754标准中规定可使用double float双精度浮点数float64
    - float64
      - 1位的sign
      - 11位的exponent
      - 52位的fraction
    - 由于小数点是个合法的数字标示，所以97.toString(2)中会把97.当作数字从而报错，需要97 .toString()或(97).toString()
  - DecimalLiteral 
    - 0
    - 0.
    - .2
    - 1e3
  - BinaryLiteral
    - 0b10
  - OctalLiteral
    - 0o10
  - HexLiteral
    - 0x10
- String
  - encoding
    - ASCII
    - Unicode
      - UCS Transformation Format
      - UTF-8 字符用1-4个字节表示
      - UTF-16 字符用2个字节或4个字节表示
      - UTF-32 字符用4个字节表示
      - {% asset_img utf8.png utf8转换 %}
      
      ```
      var bits = '美'.codePointAt().toString(2); // "111111110001110"
      
      bits.padding(); // "0111111110001110"

      utf8 = [0b11100111,0b10111110,0b10001110].map(e=>e.toString(16)) // ["e7", "be", "8e"]

      // 字符串转成blob
      const json = { hello: "world" };
      const blob = new Blob([JSON.stringify(json, null, 2)], { type: 'application/json' }); 

      // base64编码转成blob
      const base64 = 'iVBORw0KGgoAAAANSUhEUgAAAAEAAAABAQMAAAAl21bKAAAABlBMVEUAAP+AgIBMbL/VAAAACXBIWXMAAA7EAAAOxAGVKw4bAAAACklEQVQImWNgAAAAAgAB9HFkpgAAAABJRU5ErkJggg==';
      const byteCharacters = atob(base64);
      const byteNumbers = new Array(byteCharacters.length);
      for (let i = 0; i < byteCharacters.length; i++) {
        byteNumbers[i] = byteCharacters.charCodeAt(i);
      }
      const array = Uint8Array.from(byteNumbers);
      const blob = new Blob([array], {type: 'image/png'});  


      // ArrayBuffer转成文件上传
      // 在浏览器中，每个字节以十进制的方式存在
      const bufferArrary = [137,80,78,71,13,10,26,10,0,0,0,13,73,72,68,82,0,0,0,1,0,0,0,1,1,3,0,0,0,37,219,86,202,0,0,0,6,80,76,84,69,0,0,255,128,128,128,76,108,191,213,0,0,0,9,112,72,89,115,0,0,14,196,0,0,14,196,1,149,43,14,27,0,0,0,10,73,68,65,84,8,153,99,96,0,0,0,2,0,1,244,113,100,166,0,0,0,0,73,69,78,68,174,66,96,130];
      const array = Uint8Array.from(bufferArrary);
      const blob = new Blob([array], {type: 'image/png'});

      // 服务端node

      var b = 'e7 be 8e'; // buffer字符串
      Buffer.from(b.split(' ').map(x=>'0x'+x)).toString() // 美

      // base64 to buffer
      const b64string = /* whatever */;
      const buf = Buffer.from(b64string, 'base64');

      // stream to buffer
      function streamToBuffer(stream) {  
        return new Promise((resolve, reject) => {
          const buffers = [];
          stream.on('error', reject);
          stream.on('data', (data) => buffers.push(data))
          stream.on('end', () => resolve(Buffer.concat(buffers))
        });
      }
                        

      ```

    - UCS 
      - Unicode Character Set
      - UCS-2 2个字节进行编码
        - 在UCS-2的2个字节前面加上2个零字节就得到了UCS-4的BMP
      - UCS-4 4个字节进行编码 
        - 实际上只用了31位，最高位必须为0
        - 根据最高位为0的最高字节分成2^7=128个group
        - 每个group根据次高字节分成2^8=256个plane
        - 每个plane根据三高字节分成2^8=256个row
        - 每个row包含2^8=256个cell
        - group0的plane0被称作BMP(Basic Multilingual Plane)即UCS-4中高2个字节为0 
    - GB
      - GB2312
      - GBK
      - GB18030
    - ISO-8859
    - BIG5
  - 其他
    - 转义
      - \xFF 后面跟2个16进制数字表示一个字符
      - \uFFFF 后面跟4个16进制数字表示一个字符
    - 字符串模版
- 正则表达式
  - 由于/也是除法的符号，所以/a/g正则字面量前面有标示符时可能会出错
  ```
  var a,g;
  a
  /a/g

  ```

### js表达式与类型转换
#### js数字精度问题
- float内存布局展示
  - IEEE754标准中规定二进制有效数字的开头一定是1，所以第13位有个隐藏的1
  - 下面的代码中把隐藏的1展示了出来方便理解
  - js中0和-0是相等的只是符号位不同，判断zero是正0还是-0可以用 1/zero，如果返回Infinity则是正0，返回-Infinity则是-0;
  - 由于精度问题，尽量减少对浮点数的运算，运算后要考虑浮点数本身和运算符号带来的精度丢失问题，如1.1+1.3-2.4的问题

```
<template>
  <div id="app">
    <span v-for="v,i of bits">
      <input :class="i>0 ? i>11 ? 'fraction':'exponent':'sign'" v-model="bits[i]" />
      <input v-if="i==11" value="1" disabled="disabled"/>
      <br v-if="i==31" />
    </span>
    <br />
    <input style="width:5em;" v-model="value" />
  </div>
</template>

<script>
  window.vm = new Vue({
    el:"#app",
    data:{
      bits: Array(65).join(0).split("").map(v => Number(v)),
      value: 0
    },
    watch:{
      bits(val){
        console.log("bits",val);
        const bytes = new Uint8Array(8);
        const memory = new Float64Array(bytes.buffer);
        for(var i=0; i<8; i++){
          var byte=0;
          for(var j=0; j<8; j++){
            byte = byte <<1;
            byte |= Number(val[i * 8 + j])
            console.log(byte,val[i * 8 + j]);
          }
          console.log("byte",byte);
          bytes[7-i] = byte;
        }
        this.value = memory[0];
      },
      value(val){
         const bytes = new Uint8Array(8);
         const memory = new Float64Array(bytes.buffer);
         memory[0] = (val);
         console.log("******");
         for(var i=0; i<8; i++){
          var byte= bytes[i];
          console.log(byte);
          for(var j=0; j<8; j++){
           this.bits[(8-i) * 8 - j - 1] = byte & 1;
           byte= byte>>1;
          }
        }
      }
    }
  })
</script>

<style>
input::-webkit-outer-spin-button,
input::-webkit-inner-spin-button{
  -webkit-appearance: none;
}
span {
  padding:0;
  margin:0;
}
input {
  width:1em;
  height:2em;
  text-align:center;
}
.sign {
  background-color:lightblue;
}
.exponent {
  background-color: orange;
}
</style>

```
#### expression
- Grammar
  - Grammar Tree vs Priority
  - Left-Hand-Side && Right-Hand-Side (=左右两边)
- Expression
  - Member访问
    - a.b 属性访问
    - a[b] b可以是变量，类比java的反射
    - super.b 构造函数中访问super父类的静态属性
    - super['b'] 同上
    - new.target 函数中可以通过此属性判断是否new调用,非new调用返回undefined, new调用返回构造函数
    - fun`str${var}` 会把字符串模版中分成几部分(str数组,$var列表)作为参数调用fun函数
  - New操作符
    - new Foo
    - new Foo() 优先级更高，即如果后面跟()则把参数传入new的构造函数里
    - new Foo['b'] 先访问b属性后进行new调用
    - new Foo()['b'] 先进行Foo()的new调用后进行b属性访问
  - Left-Hand-Side && Right-Hand-Side (=左右两边)
  - Update
    - a++
    - a--
    - --a
    - ++a
  - Unary
    - delete a.b
    - void foo(); 推荐在IIFE中使用void，避免IIFE前后没有插入分号导致多个文件合并时产生的()()()的bug
    - typeof a; null/function
    - await a
    - +a
    - -a
    - ~a
    - !a  !!a转换为同true/false的布尔类型
  - Exponental
    - **  右结合的运算符，从右往左计算
  - Multiplicative
    - * 
    - /
    - %
  - Additive
    - +
    - -
  - Shift
    - <<
    - >>
    - >>>
  - Relationship
    - <= 
    - >=
    - instanceof
    - in
  - Equality
    - ==
    - !=
    - ===
    - !==
  - Bitwise
    - &
    - ^
    - |
  - Logical 会有短路逻辑
    - &&
    - ||
  - Conditional
    - ? : 
  - Comma 返回最后一个表达式的值
    - , 
- Runtime
  - Type Convertion
    - Boxing & Unboxing
      - String() 不作为new调用时是强制类型转换,作为new调用时则返回对象
      - Number() 不作为new调用时是强制类型转换,作为new调用时则返回对象
      - Boolean() 不作为new调用时是强制类型转换,作为new调用时则返回对象
      - Symbol() 不能用new调用，但可以用Object(Symbol("1"))装箱
      - Object() new调用和非new调用都一样，都返回一个Object
      - Null和Undefined都不能装箱
    - toString vs valueOf vs Symbol.toPrimitive
      - 类型转换时如果有[Symbol.toPrimitive](){}则完全按照此函数的返回值进行转换
      - 否则按照默认的toPrimitive顺序执行
        - 优先valueOf
        - 如果拿不到合适的原始值再调toString

```

function convertStringToNumber(string,radix){
  if(arguments.length < 2){
    radix = 10;
  }
  var chars = string.split('');
  var number = 0;

  var i = 0;
  while(i < chars.length && chars[i] != '.'){
    number *= radix;
    number += chars[i].codePointAt(0) - '0'.codePointAt(0);
    i++;
  }
  if(chars[i]==='.'){
    i++;
  }
  var fraction = 1;
  while(i < chars.length){
    fraction /= radix
    number += (chars[i].codePointAt(0) - '0'.codePointAt(0)) * fraction;
    i++;
  }
  return number;
}
convertStringToNumber("123")
convertStringToNumber("100.0123",10)

function convertNumberToString(number,radix){
  var integer = Math.floor(number);
  var fraction = number - integer;
  var string = '';

  while(integer > 0 ){
    string = integer % radix + string;
    integer = Math.floor(integer / radix);
  }

  return string;
}
convertNumberToString(100,10)

```

### js语句与对象
#### 预习
- ASI
  - Automatic Semicolon Insertion
  - 按照ECMAScript标准，特定语句必须以分号结尾
  - 有时候书写方便可以省略分号
  - 解释器会自己判断语句该在哪里终止，实际上并没有真正的插入分号，只是个形象的说法
- var最好写在函数内最前面或第一次出现的地方来作为最佳实践或放弃var
- void function(){}(); void来表达IIFE成为最佳实践

#### 语句
- 简单语句
  - ExpressionStatement  a=1+2;
  - EmptyStatement  ;
  - DebuggerStatement  debugger;
  - ThrowStatement  throw a;
  - BreakStatement  break LABEL;
  - ContinueStatement  continue LABEL;
  - ReturnStatement  return a+b;
- 复合语句
  - BlockStatement  {}
  - IfStatement
  - SwitchStatement
  - IterationStatement
    - while(){}
    - do{}while()
    - for(;;){}
      - let/const声明过后的变量不能再被声明
      - let i = 0;for(let i=5;i<10;i++){let i=0;console.log(i)}; 
      - 上面的i会产生3层作用域,for语句中的()里的声明部分会对let/const产生一层作用域
    - for(in){}
    - for(of){} 遍历iterator的每个值
    - for await(of){} 遍历异步iterator的每个值
  - WithStatement
  - LabelledStatement
  - TryStatement
    - try{}catch(){}finally{}
    - catch(e){}语句块也会对e生成和后面的{}一起的独立的作用域
- 声明
  - FunctionDeclaration
  - GeneratorDeclaration
  - AsyncFunctionDeclaration
  - AsyncGeneratorDeclaration
  - VariableStatement
    - 主要是变量声明语句的hoist
    - 只提升声明的部分，初始化赋值的部分不提升
    - 执行过程中的hoist称为BoundNames，会被预处理
  - ClassDeclaration
  - LexicalDeclaration
    - const/let
    - 声明过后不允许重复声明
    - 产生块级作用域

#### runtime
- CompletionRecord 语句执行完成后的记录，有三个属性
  - [[type]]: normal | throw | break | continue | return 
  - [[value]]: Types ｜ empty
  - [[target]]: label
- LexicalEnvironment
  - 作用域一般指静态代码中的区域
  - 上下文一般指运行时内存中的对象

#### 对象
- 三要素
  - 唯一性，每个对象都是唯一的
  - 状态，用属性描述对象
  - 行为，用方法改变对象的状态
- 面向对象
  - 三特性
    - 封装/复用/解耦/内聚
    - 继承
    - 多态
  - 基于类的面向对象
    - 归类派，多重继承是其特性，如c++
    - 分类派，单继承结构，会有一个基类Object，但向上归类时就会产生Interface和Mixin
  - 基于原型对象的面向对象
    - 采用相似的方式去描述对象，不做严谨的分类
    - 任何对象只需要描述自己与原型对象的区别即可
- js对象
  - 一般对象的描述方式非常简单，只需要关注属性和原型即可(原型不是属性，js方法也属于属性)
    - 访问属性时如果当前对象没有，则会访问原型对象上的同名属性，直到null，形成原型链
    - 增加属性时则是增加到自身的属性上，不会影响原型链的上层
    - 属性机制保证了对象只需要描述自己与原型对象的区别即可
  - 属性kv对
    - key
      - String
      - Symbol
    - value
      - DataProperty
        - [[value]]
        - writable
        - enumerable
        - configurable
      - AccessorProperty
        - get
        - set
        - enumerable
        - configurable
  - ObjectApi
    - 基本对象能力api
      - {}
      - .
      - []
      - Object.defineProperty
    - 原型api（加上基本对象能力api就是基于原型的OOP）
      - Object.create
      - Object.setPrototypeOf
      - Object.getPrototypeOf
    - 类api（加上基本对象能力api就是基于类的OOP）
      - new 
      - class
      - extends
    - 原型模拟类的api（在未实现class前的模式，现在应该被废弃）
      - new
      - function
      - prototype
  - Function Object
    - 函数对象是一种特殊的对象
    - 除了一般对象的属性和原型外，函数对象还有一个行为[[call]]
    - function关键字/箭头函数/Function构造器创建的对象都有[[call]]行为
    - 用f()这样的语法把对象当作函数使用时就会访问[[call]]行为
    - 如果对象上没有[[call]]行为，使用()调用时就会报错

### js结构化程序设计
#### js引擎
- js引擎接收js代码的三种方式
  - 普通的script['src']或内联代码片段
  - script[type='module']
  - setTimeout/setInterval
- 宏任务
  - script
  - UI交互
  - setTimeout/setInterval
- promise微任务执行顺序
  - 一个宏任务中只有一个微任务队列，可以有多个微任务，所有微任务执行完毕后此宏任务才执行完毕
  - promise.resolve和promise.reject都会产生微任务，插入当前task的宏任务后面继续执行
  - promise.resolve.then的回调函数里尽量不要再有Promise
  - 如果嵌套里promise,里面promise.resolve.then()执行后外面后续的then也会被触发
  - 所以可能后面的then执行后又返回里面promise.resolve.then().then()执行
  - promise.then的参数如果不是对应resolve/reject的函数,其他语句会同步执行，并且执行中间产生的错误不会立即中断执行，而是等同步任务执行完后回调给promise.catch或报错，所以需要注意then()里的参数是否是函数来决定是否会产生微任务
  - await 语句也会产生微任务，和promise一起执行微任务入队操作
  - throw new Error()或运行时产生的报错只会影响当前宏任务，不影响已入队的微任务，更不影响异步的宏任务
  - promise微任务执行顺序和实现有关，safari和chrome可能不一致
  - 浏览器中只有promise和MutationObserver会产生微任务

#### 结构化程序设计
- js执行粒度
  - JSContext => Realm
  - 宏任务
  - 微任务 Promise
  - 函数调用 ExecutionContext
  - 语句/声明
  - 表达式
  - 变量/直接量/this

- 函数调用
  - 调用过程产生调用栈ExecutionContextStack
  - 栈顶的上下文称为RunningExecutionContext
  - ExecutionContext
    - Generator
      - 有Generator的就是GeneratorExecutionContext
      - 没有Generator就是普通的ECMAExecutionContext,包括下面6种
    - Realm
      - js中函数表达式和对象直接量都会创建在Realm中
      - 使用.做隐式转换时也会创建在Realm中
      - 这些对象也有原型，如果没有Realm，就不知道他们的原型是什么
      - 前端产生不同的Realm一般只有在不同的iframe中
    - Script or Module
    - CodeEvaluationState (generator函数记录执行到哪一步)
    - Function
    - LexicalEnvironment
      - this
        - 箭头函数的this是和变量一起进入环境
        - 普通函数是是调用时进入环境
      - new.target
      - super
      - 变量
    - VariableEnvironment
      - 是个历史包袱
      - 主要是处理eval/with语句里的var声明的问题

```
// Realm里的全局对象
var globalObjectArray = [
    // Infinity,
    // NaN,
    // undefined,
    "eval",
    "isFinite",
    "isNaN",
    "parseFloat",
    "parseInt",
    "decodeURI",
    "encodeURI",
    "decodeURIComponent",
    "encodeURIComponent",
    "Array",
    "ArrayBuffer",
    "Boolean",
    "DataView",
    "Date",
    "Error",
    "EvalError",
    "Float32Array",
    "Float64Array",
    "Function",
    "Int8Array",
    "Int16Array",
    "Int32Array",
    "Map",
    "Number",
    "Object",
    "Promise",
    "Proxy",
    "RangeError",
    "ReferenceError",
    "RegExp",
    "Set",
    "SharedArrayBuffer",
    "String",
    "Symbol",
    "SyntaxError",
    "TypeError",
    "Uint8Array",
    "Uint8ClampedArray",
    "Uint16Array",
    "Uint32Array",
    "URIError",
    "WeakMap",
    "WeakSet",
    "Atomics",
    "JSON",
    "Math",
    "Reflect"
];

var queue = [];
for(let p of globalObjectArray){
    queue.push({
        path:[p],
        object:this[p]
    })
}

var set = new Set();
var current;

while(queue.length){
    current = queue.shift();

    console.log(current.path.join('.'))
    if(set.has(current.object)){
        continue;
    }
    set.add(current.object);
    
    let proto = Object.getPrototypeOf(current.object);
    if(proto){
        queue.push({
            path:current.path.concat(["__proto__"]),
            object:proto
        })
    }

    for(let p of Object.getOwnPropertyNames(current.object)){

        var property = Object.getOwnPropertyDescriptor(current.object,p);
        if(property.hasOwnProperty("value") && 
            ((property.value != null) && (typeof property.value == "object") || (typeof property.value =="function")) && 
            property.value instanceof Object){
            queue.push({
                path:current.path.concat([p]),
                object:property.value
            })
        }

        if(property.hasOwnProperty("get") && ( typeof property.get =="function")){
            queue.push({
                path:current.path.concat([p]),
                object:property.get
            })
        }

        if(property.hasOwnProperty("set") && ( typeof property.set =="function")){
            queue.push({
                path:current.path.concat([p]),
                object:property.set
            })
        }
    }
}



```

### 浏览器工作原理
#### 总论
- 工作过程
  - url->http
  - html->parse
  - DOM->css computing
  - DOM with CSS->layout
  - DOM with position->render
  - Bitmap
- 网络模型
  - OSI七层网络模型
    - 应用
    - 表示
    - 会话
    - 传输
    - 网络
    - 数据链路
    - 物理
  - 四层网络模型
    - 应用层
    - 传输层
    - 网络层
    - 物理层
- tcp/ip
  - tcp
    - 流
    - 端口
    - require('net')
  - ip
    - 包
    - IP地址
    - libnet/libpcap

#### http
- Request
  - RequestLine
  - Headers
  - Body
- Response
  - StatusLine
  - Headers
  - Body

```
// client.js
const net = require("net");

class Request {
    constructor(options){
        this.method = options.method || "GET";
        this.host = options.host || "localhost";
        this.port = options.port || 8080;
        this.path = options.path || "/";
        this.headers = options.headers || {};
        this.body = options.body || {};

        if(!this.headers["Content-Type"]){
            this.headers["Content-Type"] = "application/x-www-form-urlencoded"
        }

        if(this.headers["Content-Type"] == "application/json"){
            this.bodyText = JSON.stringify(this.body)
        }else if(this.headers["Content-Type"] =="application/x-www-form-urlencoded"){
            this.bodyText = Object.keys(this.body).map(k=>encodeURIComponent(k) +"="+encodeURIComponent(this.body[k])).join("&")
        }
        this.headers["Content-Length"] = this.bodyText.length;

    }

    toString(){
        return `${this.method} ${this.path} HTTP/1.1\r
${Object.keys(this.headers).map(k=>`${k}: ${this.headers[k]}`).join("\r\n")}\r
\r
${this.bodyText}`
    }

    send(conn){
        return new Promise((res,rej)=>{
            if(conn){
                conn.write(this.toString())
            }else{
                conn = net.createConnection({
                    host:this.host,
                    port:this.port
                },()=>{
                    conn.write(this.toString())
                })
            }

            conn.on("data",(data)=>{
                res(data.toString())
                conn.end();
            })
            conn.on("end",()=>{
                console.log("disconnected from server")
            })
            conn.on("error",(error)=>{
                rej(error);
                conn.end();
            })
        })
        
    }   
}

class ResponseParser {
    constructor(){
        this.WAITING_STATUS_LINE = 0;
        this.WAITING_STATUS_LINE_END = 1;
        this.WAITING_HEADER_NAME = 2;
        this.WAITING_HEADER_VALUE = 3;
        this.WAITING_HEADER_LINE_END = 4;
        this.WAITING_HEADER_LINE_BLOCK_END = 5;

        this.current = this.WAITING_STATUS_LINE;
        this.statusLine = "";
        this.headers = {};
        this.headerName = "";
        this.headerValue = "";
    }

    receive(string){

    }

    receiveChar(char){
        
    }
}


void async function(){
    let request = new Request({
        method:"GET",
        host:"localhost",
        port:8080,
        path:"/",
        headers:{
            "geek":"time"
        },
        body:{
            name:"GeekTime"
        }
    })
    let res = await request.send();
    console.log(res);
}();

// const client = net.createConnection({
//     host:"localhost",
//     port:8080
// },()=>{
//     console.log("connected to server");
//     // client.write("GET / HTTP/1.1\r\n");
//     // client.write("\r\n")

//     let request = new Request({
//         method:"GET",
//         host:"localhost",
//         port:8080,
//         path:"/",
//         headers:{
//             "geek":"time"
//         },
//         body:{
//             name:"GeekTime"
//         },

//     })

//     console.log(request.toString())
//     client.write(request.toString())
// })

// client.on("data",(data)=>{
//     console.log("on data \r\n",data.toString())
//     client.end();
// })
// client.on("end",()=>{
//     console.log("disconnected from server")
// })


// server.js
const http = require("http");

const server = http.createServer((req,res)=>{
    console.log("server received a request");
    console.log(req.headers);
    
    res.setHeader("Content-Type","text/html");
    res.setHeader("X-Foo","bar");
    res.writeHead(200,{"Content-Type":"text/plain"});
    res.end("GeekTime");
})

server.listen(8080,()=>{
    console.log("server is running...")
})

```