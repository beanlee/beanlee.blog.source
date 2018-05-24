---
title: Simple HTTP Server on OSX
date: 2016-05-31 14:18:32
category : [Mac]
---

很多项目在开发前期或者仅有 HTML/CSS/JS 文件，本地想要预览效果而不希望本地启动 Apache 或者 Nginx 难免有些麻烦，Mac OSX 其实内置Python，通过下面两条命令就可以快速启动一个简单 HTTP 服务。

```shell
$cd path/project/folder
$python -m SimpleHTTPServer 8080
```

Tada, thus you have got a HTTP server which port is 8080.

```
control + c // stop it
```
