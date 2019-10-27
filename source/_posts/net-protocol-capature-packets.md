---
title: net protocol & capature packets
date: 2019-08-11 07:30:55
tags:
- wireshark
- tcp/ip
---

# 概述

## 协议栈
- 应用层
  - http/1.1
  - websocket
  - http/2.0
- 表示层
  - tls/ssl
- 传输层
  - tcp
- 网络层
  - ip
- 链路层
  - 以太网

## 工具
- chrome
- wireshark
- dig
- tcpdump

# http/1.1

## 基础
- RFC: request for comments
- http/1.1
  - 无状态
  - 请求/响应模式
  - 可扩展的语义
  - 自描述的消息格式
- ABNF: 扩充巴科斯-瑙尔范式
  - 空白字符SP来分割各个元素
  - /表示可供选择的规则
  - %x##-##表示值的范围
  - ()组合，视为一个元素
  - m*n表示重复m-n次
  - []表示可选序列
  - DIGIT表示数字
  - HEXDIG表示十六进制数字
  - SP表示空格
  - CRLF表示兼容的回车换行
- OSI: open system interconnection reference model概念模型
  - 应用层（http/email/dns/smtp/ftp/telnet）
  - 表示层（ssl）
  - 会话层（session）
  - 传输层（tcp/udp）
  - 网络层（ip，广域网路由器）
  - 数据链路层（mac，局域网交换机）
  - 物理层
- TCP/IP 事实实现方案
  - 应用层（http/email/dns/smtp/ftp/telnet）
  - 传输层（tcp/udp）
  - 网络层（ip/icmp/arp）
  - 物理层（ethernet）
- 评估web架构的关键属性
  - 性能performance
    - 网络性能 throughput吞吐量<=bandwidth
    - 用户感知性能
    - 网络效率 缓存/cdn/减少交互次数
  - 可伸缩性scalability
  - 简单性simplicity
  - 可见性visiable
  - 可移植性portablity
  - 可靠性reliability
  - 可修改性modifiability
    - 可进化
    - 可定制
    - 可扩展
    - 可配置
    - 可重用
- URI
  - URI是URL+URN的超集，统一掉概念
  - schema://user:pass@hostname:port/path?query#fragment
- method
  - GET
  - HEAD
  - POST
  - PUT
  - DELETE
  - CONNECT
  - OPTIONS
  - TRACE
- status
  - 1xx 请求服务器已经收到，需要进一步处理
    - 100 Continue常用上传大文件前
    - 101 Switch Protocols升级协议ws/h2
    - 102 Processing webdav协议
  - 2xx 成功处理了请求，当接收到不认识的2xx时默认用200
    - 200 OK
    - 201 Created成功创建
    - 202 Accepted
    - 206 Partial Content 使用range协议返回部分内容
  - 3xx 重定向
    - 301 Moved Permanently 永久重定向，可被缓存
    - 302 Found 临时重定向，不能被缓存
    - 304 Not Modified 可复用缓存
  - 4xx 客户端错误
    - 400 Bad Request
    - 401 Unauthorized未认证
    - 403 Forbidden
    - 404 Not Found
  - 5xx 服务端错误
    - 500 Internel Server Error 内部服务器错误，当接收到不认识的5xx时默认用500
    - 501 Not Implemented
    - 502 Bad Gateway
    - 503 Service Unavailable
    - 504 Gateway Timeout
- 长短连接 
  - Connection: Keep-Alive | Close
  - http/1.1默认支持长连接
  - 如果Connection后面跟header字段则表示对代理服务器要求不做此header转发
  - Connection只表示tcp连接的两端，不能表示跨中间的服务器
- header
  - X-Forwarded-For 用于传递ip，每走一层都会append一个ip
  - X-Real-IP 只记录用户外网ip
  - Referer 来源，通常做防盗链
  - User-Agent
  - From 通常是爬虫添加的后面跟邮件地址告诉server可通过邮件联系
  - Allow server支持的method
  - Accept* 协商资源表现形式
    - Accept: MIME q=0.8;
    - Accept-Language: zh-CN,zh q=0.8
    - Accept-Encoding: gzip | br | deflate
    - Accept-Range: bytes | none 是否支持range请求
  - Transer-Encoding: chunked | gzip 不定长包体传输格式
  - Content-Disposition: inline | attachment[; filename=xx.xx]
- MIME
  - Multipurpose Internet Mail Extensions
  - 格式：type/subType(;params)
  - type
    - text
    - image
    - audio
    - video
    - application
  - params
    - attribute=value
- Form关键属性
  - action表示uri
  - method表示方法
    - GET
    - POST
  - enctype
    - application/x-www-form-urlencoded 默认编码方式 k=v&k=v 需要encode
    - multipart/form-data 有boundary做分割，一般用于文件传输
- Range请求
  - 做多线程下载/断点续传/点播视频等
  - 请求头部标明
    - Range: bytes=0-499[,500-1000[,1001-]]
    - Range: bytes=-4 最后3个字节
    - If-Range: Etag | httpDate 验证已经下载的部分是否有变动
  - 响应头部标明
    - Content-Range: bytes 0-100/2000 表示共2000长度，返回了前101字节
    - Content-Range: bytes 0-100/* 表示不知道总长度但返回了前101字节  
    - Content-Range: multipart/byterages; boundary=...
- cookie
  - Set-Cookie
    - 每一个Set-Cookie只能设置一个name=value，如需多个需要多个Set-Cookie
    - name=value; expires= httpDate; max-age=X; domain=X; path=/; secure; httpOnly;
  - Cookie
    - 可以把多个Set-Cookie返回的name组合成一个进行请求
    - name=value;[ name=value;]
- 同源三要素
  - schema
  - hostname
  - port
- 跨域
  - 可跨域的组件（满足可扩展可用性）
    - script
    - link
    - img
    - iframe
    - video
    - audio
  - 攻击与防护
    - csrf 
      - cross-site request forgery
      - 通过referer可做一层防护
      - 通过csrfToken即服务器响应表单时添加隐藏的token信息已备后续验证也可做防护
    - xss cross-site scripting
  - cors
    - cross origin resource sharing
    - 简单请求三要素
      - GET/HEAD/POST之一
      - Accepte/Accept-Language/Content-Type安全header之一
      - Content-Type是text/plain|application/x-www-form-urlencoded|multipart/form-data之一
    - 复杂请求
      - 简单请求之外的都是复杂请求
      - 复杂请求必须先走一个OPTIONS请求
    - 请求相关头部
      - Origin
      - Access-Control-Request-Method
      - Access-Control-Request-Headers
    - 响应相关头部
      - Access-Control-Allow-Origin
      - Access-Control-Allow-Methods
      - Access-Control-Allow-Headers
      - Access-Control-Allow-Credentials
      - Access-Control-Max-Age
- 缓存
  - 优先级
    - s-maxage
    - max-age
    - Expires
    - 预估过期时间（浏览器实现实现的"10%算法"）
  - Cache-Control
    - 请求中
      - max-age=t | max-stale=t | min-fresh=t 
      - no-store | no-cache | no-transform | only-if-cached
    - 响应中
      - must-revalidate | proxy-revalidate
      - public | private 
      - no-cache | no-store | no-transform
- 重定向
  - 301 http/1.0 永久重定向，可以被缓存，重定向后的请求可以改变method，通常使用get
  - 302 http/1.0 临时重定向，不可被缓存，重定向后的请求可以改变method，通常使用get
  - 307 http/1.1 临时重定向，不可被缓存，重定向后的请求不能改变method
  - 308 http/1.1 永久重定向，可以被缓存，重定向后的请求不能改变method
- 隧道
  - tunnel通过http连接来传输非http协议格式的数据
  - 常用于穿越防火墙/传递ssl消息
  - 通过connect建立连接后变为双向传输，不必遵守http协议
- 爬虫
  - robots.txt
    - User-Agent 允许哪些机器人
    - Disallow 禁止哪些目录
    - Crawl-delay 访问间隔防止流量过大
    - Allow 允许哪些目录
    - Sitemap 站点地图

## dns
- 域名与ip之间的映射数据库
- 查询方式
  - 迭代查询
  - 递归查询
- query
  - questions
    - QNAME
      - .分割多段，每段字节数打头再跟其ASCII编码
      - 最终以 00 结尾
    - QTYPE
      - 1 A ipv4地址
      - 2 NS 权威域名服务器
      - 5 CNAME 别名
      - 28 AAAA ipv6地址
    - QCLASS
      - IN 表示internet
- response
  - NAME
  - TTL time to live
  - RDLENGTH RDATA length
  - RDATA 查询值，如ip或CNAME值

# wireshark

## BPF过滤器
- Berkeley Packet Filter
- 在设备驱动级别提供抓包接口
- 表达式
  - 原语
    - Type: host port portrange...
    - Dir: dst src...
    - proto: tcp udp...
  - 操作符
    - && and
    - || or
    - ! not

## 独有的显示过滤器
- view -> internels -> support protocols
- 操作符
  - eq ==
  - ne !=
  - gt >
  - ge >=
  - lt <
  - le <=
  - contains
  - matches ~
- 函数
  - upper
  - lower
  - len
  - count
  - string

# websocket
## 基础
- demo页面：http://www.websocket.org
- 客户端发送数据要进行掩码处理防止针对代理服务器的缓存污染攻击
- pingpong心跳保持会话
- 优点
  - 浏览器支持率比较高
  - 支持服务器推送
- 不足
  - 设计比较简单，很多功能和性能上的问题没有真正解决

## 握手
{% asset_img handshake.png websocket握手包头 %}

## 解密tls
- 通过浏览器DEBUG日志获得tls握手阶段生成的密钥
- 步骤
  - 配置环境变量SSLKEYLOGFILE=path2log使chrome输出DEBUG日志
  - wireshark配置解析DEBUG日志(编辑/首选项/protocols/tls)

# http2
## 基础
- http1.1的问题
  - 高延迟
    - 带宽在增加，延迟却没办法降低
    - 浏览器并发限制
    - 同一连接串行处理请求
  - 无状态特性带来了巨大的http头部
  - 不支持服务器推送
- h2主要特性
  - 传输数据量大幅减小
    - 标头压缩
    - 二进制传输
  - 多路复用并支持优先级
  - 服务器推送
  - 必须使用tls
- tls握手通讯过程
  - 验证身份
  - 达成安全套件共识
  - 传递密钥
  - 加密通讯
- h2核心概念
  - 连接connection
  - 数据流stream
    - 通过stream实现多路复用
  - 消息message
    - header帧
    - data帧
  - 数据帧frame

## frame
- 帧头部
{% asset_img frameHeader.png 帧头部 %}
- 作用
  - 多路复用(同一stream内的frame必须是有序的，接收端根据streamID并发组装消息)
  - 客户端建立的流必须是奇数，服务端建立的是偶数
  - 状态管理的约束(ID不能复用，只能递增，超出最大值后必须断掉tcp连接重新建立)
- 帧类型type
  - DATA
  - HEADERS
  - PRIORITY
  - RST_STREAM
  - SETTINGS
  - PUSH_PROMISE
  - PINNG
  - GOAWAY
  - WINDOW_UPDATE
  - CONTINUATION
- hpack
  - 头部压缩算法
  - 三种压缩方式相结合
    - 静态字典
    - 动态字典
    - 压缩算法huffman编码

## 服务推送
- 每一条推送都基于一个请求
- 在请求的响应中可以恢复即将推送的资源
- 再开一个stream进行资源的推送，不同的stream可以实现并发

## 流控
- 应用层和tcp层都可以做
- 发送和接口流量控制是可以分开作用的
- 只有DATA帧才受流控限制
- 流控不能被禁用

## h2现存问题
- tcp+tls建链握手过多
  - 必须建立在tls之上
- 多路复用与tcp队头阻塞问题
  - 本质还是在tcp流中是串行的
  - 串行对头中丢失一个包就会阻塞后面的包
- tcp是由操作系统内核实现的，内核更新慢

## http3
- 基于quick协议与h2协议共同成为h3
- quick协议基于udp/ip协议之上，h2协议之下
- quick协议实现了
  - 多路复用
  - tls
  - 拥塞控制
  - 丢包重发
- 优势
  - 可以连接迁移
  - 解决了队头阻塞问题
  - 1RTT完全就握手完毕

# tls
## 基础
- 概念
  - ssl secure sockets layer
  - tls transport layer security
- 发展历史
  - ssl3.0
  - tls1.0
  - tls1.1
  - tls1.2
  - tls1.3
- 设计目的
  - 身份验证
  - 保密性
  - 完整性
- 主要功能
  - 握手
  - 交换密钥
  - 告警
  - 对称加密数据
  - 记录
- tls握手通讯过程
  - 验证身份
  - 达成安全套件共识
  - 传递密钥
  - 加密通讯
- 安全加密套件
  - TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
  - ECDHE 密钥交换算法
  - RSA 身份验证算法
  - AES 对称加密算法
  - 128 对称加密强度
  - GCM 对称加密模式
  - SHA256 签名hash算法

## 对称加密
- 工作原理
  - XOR运算 速度快
  - padding填充 
    - 会把明文进行blockCipher分组进行加密
    - 当分组后最后一个block可能长度不够XOR运算，需要padding
    - 填充时可以按bit位进行填充也可按byte字节填充
    - RFC中常用按byte字节进行填充，填充内容是多少个字节就填充多少个数字，即PKCS7填充算法
  - 工作模式
    - 常用GCM工作模式
    - Galois/Counter Mode
- AES
  - advanced encryption standard加密算法
  - 常用填充算法PKCS7
  - 常用工作模式GCM
  - 三种密钥长度
    - AES-128密钥长度16字节，加密轮数10
    - AES-192密钥长度24字节，加密轮数12
    - AES-256密钥长度32字节，加密轮数14
  - 加密步骤
    - 明文按照16字节拆分成若干块，每个块是4*4矩阵
    - 按填充算法进行最后一块的填充
    - 每一个明文块用AES加密算法和密钥进行加密
    - 拼接所有密文块
  
## 非对称加密
- RSA 
- openssl
  - 生成私钥 openssl genrsa -out private.pem
  - 提取公钥 openssl rsa -in private.pem -pubout -out public.pem
  - 加密文件 openssl rsautl -encrypt -in hello.txt -inkey public.pem -pubin -out hello.en 
  - 解密文件 openssl rsautl -decrypt -in hello.en -inkey private.pem -out hello.de