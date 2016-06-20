title: Notes Of 360 Front-End Lesson
category : Note
date: 2015-11-22 23:32:19
tags: [Javascript, ReractJS, NodeJS, Front-End]
---

360前端训练营笔记
<!-- more -->

# 360前端训练营课堂笔记 － 奇舞团与Facebook畅聊大前端

## HTTP/2 深入浅出

* 屈光宇 Ququ老师 [JerryQu 的小站](https://imququ.com)

### Before HTTP/2

应用层协议，基于TCP
HTTP 0.9 没有请求头，GET
HTTP 1.0 增加请求／响应头，可以响应media，content-type，refere来源
HTTP 1.1 支持持久连接，keep-alive，支持传输编码，content-length，请求范围range（断点续传），cache-control,expires
SPDY协议截止2015

统计：页面大小和连接数，同一个页面多域名
TCP一个连接对应一个请求
keep-alive、管道pipelining（合并请求）、域名散列（多域名增加并发连接数）、协议开销、合并请求（改写Ajax，雪碧图、cssjs合并、cssjs内联、图片和音频内联base64、阻塞渲染，样式内联（多用于移动端，感知缓存）

input标签type＝text是默认值
H2O，can I use、cleartext协商，HTTPS，mozilla免费证书，全球已经开通HTTPS网站占63%

chrome在非HTTPS禁用关于设备使用的API，ALPN
HTTP／2-enabled，net-internals/#http2、Wireshark
优化，TLS，非对称加密，证书减少层级，ECC证书（很小，但是兼容性问题，vista才开始支持，移动端可以考虑）
HSTS，强制HTTPS、TLS安全清单SSL

### HTTP/2

* 优化

优化：减少DNS查询，减少域名、启用预读，减少重定向，使用CDN，压缩（PNGout，pngcrush）、HTTP缓存
不需要：域名散列，资源合并，资源内联

* 最佳实践

对HTTP/2优化的域名散列，多域名指向同一IP
感知缓存资源内联或者通过 server－push，cookie 标记内联过资源 server-push
基于node的node-http2

Google QUIC协议，基于UDP协议


## Why React Matters?

* 為什麼 React 牛？
* Facebook 黄士旗

+ Predictable/Immutable
+ React Native For iOS & Android
+ ImmutableJS

## ES6/ES7 Node Project, ThinkJS

* 如何使用ES6/ES7开发NodeJS项目
* 李成银 ThinkJS 作者 @360齐舞团

+ callback、promise，async functions
+ babel javascript compile
+ 更多特性，class，Arrows，enhanced object literals，template
+ 性能问题，class，generators
+ 自动更新，fs.watch（重启node服务），chokidar，nodemonitor；问题，无法保存临时数据
+ 解决方案，热更新，依赖文件更新
+ require.cache，解决依赖和缓存，重写require方法
+ ThinkJS解决方案[ThinkJS](https://www.thinkjs.org)