---
layout: post
title: 移动端 HTTPS 抓包工具配置说明
date: 2018-03-29 18:53:55
category : 前端技术
tags: [移动开发]
---

投稿部门公众号，做了一些微调，增加了 Android 机型的一些说明。

2018-03-01 Beanlee

相信各位在移动端开发过程中一定遇到抓取数据包、拦截请求的场景，本文主要介绍移动端在针对 HTTPS 抓包时的几款软件的配置（包括[Charles](#Charles)、[Fiddler](#Fiddler)、[Whistle](#Whistle)），下面举例将已 iOS 11 为例，附带 Android 机型配置截图。

<!-- more -->

## Charles<b id="Charles"></b>

* [官网](https://www.charlesproxy.com/)
* 按照 license 收费，收费后终身享受升级，仅限 OS X 平台

### HTTP 抓包配置

#### 1.确保手机与主机处于同一局域网内。

查看本机 IP，使用 `ifconfig` 命令查看本机 IP；或者打开系统设置查看 IP 如截图；

例如：当前本机 IP `192.168.191.4`

![ifconfig](https://img20.360buyimg.com/uba/jfs/t16375/210/2520307643/222582/31ac0971/5ab0a175N3cbadb49.png.webp)

![wifi-config](https://img10.360buyimg.com/uba/jfs/t17530/312/915989884/252348/b1b61faa/5ab0a1e7N47455815.png.webp)

#### 2.设置手机 iOS 网络代理

`设置` -> `无线局域网` -> `HTTP 代理` -> `手动`

服务器：`192.168.191.4` 端口：`8888`

![ios&andoird-proxy-conf](https://img30.360buyimg.com/uba/jfs/t19171/267/1165626521/82168/a242486c/5abdf072Nd89a456f.png.webp)

#### 3.配置完成后，在手机端访问任何一个 App 或网页，电脑端 Charles 会弹出提示，点击 Allow

此时可以在应用的界面中看到 HTTP 的请求数据了。

接下来继续 HTTPS 的配置。

### HTTPS 抓包配置

#### 1.信任证书 本机

打开 Charles 后如下图操作，电脑端信任证书，**注意** 此处选择`system`

![charles-install-root-cer](https://img13.360buyimg.com/uba/jfs/t16189/305/2463904253/229838/13248112/5ab0a246N431365b3.png.webp)

![charles-install-root-cer-system](https://img30.360buyimg.com/uba/jfs/t15991/176/2548641977/433484/2e35454f/5ab0a26cN86d8f8c1.png.webp)

#### 2.信任证书 手机

打开 `Safari` 地址输入 `192.168.191.4:8888` 或 `chls.pro/ssl` 点击安装，此时会弹出要求需要输入手机密码，完成后证书安装成功。

此时查看 `通用` -> `描述文件` 中 charles proxy ca 已变成`已验证`。

**注意** 自 iOS 10.3 以上，还需要多一步操作，手动信任自定义根证书，才能确保证书安装并已启用；

`关于本机` -> `证书信任设置` 启用 charles proxy ca 截图详见文章底部[锦囊](#tips)

**注意** Android 手机不需要安装 Charles 证书 [ssl-certificates](https://www.charlesproxy.com/documentation/using-charles/ssl-certificates/)

下面截图中是 Android 仅安装了 Fiddler 和 Whistle 的证书截图。

![android-cert](https://img30.360buyimg.com/uba/jfs/t18598/270/1201930732/57036/69b112d/5abdf133N1ef09cfe.png.webp)


#### 3. Charles 设置启用 SSL Proxy

`Proxy`->`SSL Proxy setting` Enabled SSL Proxying

同时添加希望拦截到的域名，例如：`*.jd.com` 端口 `443`

![charles-ssl-config01](https://img14.360buyimg.com/uba/jfs/t17281/140/906435195/142935/49f55aa9/5ab0a4d4Nacbc29d6.png.webp)

![charles-ssl-config02](https://img10.360buyimg.com/uba/jfs/t19591/182/853494454/73346/71990549/5ab0a4acN9ee93450.png.webp)

**配置完成** : )

## Fiddler<b id="Fiddler"></b>

* [官网](https://www.telerik.com/fiddler)
* 微软出品，免费，由于需要 .Net 库仅 Windows 平台（也有一些 Fiddler For mac / linux 但仅限 beta 版且功能不完善，这里只推荐在 Win 平台使用）

### HTTP 抓包

配置与 ``Charles`` 配置相同端口略有不同，此处不再重复。

总结几大步骤：

1. 移动端和电脑同处同一局域网内
2. 手机或移动设备设置网络代理对应电脑端 IP 和 端口

![fiddler01](https://img14.360buyimg.com/uba/jfs/t14656/65/2745558691/37041/b26d6171/5ab0add4N3bd559a6.png.webp)

### HTTPS 抓包配置

* 勾选拦截 HTTPS 请求
* 安装证书（电脑端 & 手机端）

![fiddler02](https://img14.360buyimg.com/uba/jfs/t17401/70/901071906/48039/62c96f01/5ab0add7N4816f088.png.webp)

## Whistle<b id="Whistle"></b>

* [官网](https://avwo.github.io/whistle/)
* 国人开发，开源 MIT 协议，基于 NodeJS 跨平台
* **推荐** 功能强大，部门内也有小伙伴分享过一次使用经验

### 工具准备

* `nodejs v0.10.0+`
* `npm i -g whistle`
* `w2 start`

完成上面几步操作后，此时 `8899` 端口已经常驻后台。

### 界面

![whistle](https://img14.360buyimg.com/uba/jfs/t17362/179/900501459/1903822/f445defb/5ab0bcecNf4b0f762.png.webp)

### Chrome 插件

whistle 提供了一个基于 Chrome 的插件，可以方便切换代理，查看网络，编辑规则等，不过很遗憾作者已经将插件在 Web Store 下架，现在搜索不到，我把本地 0.10.0 版本打包成 crx 上传在 Google Driver 可以访问 [下载](https://drive.google.com/open?id=18RCuXBb_ml6OqLw-3PTSevcpJ81h-F27)

或者改用另外一个强大的插件 [Proxy SwitchyOmega](https://chrome.google.com/webstore/detail/proxy-switchyomega/padekgcemlokbadohgkifijomclgjgif) 该插件也拥有强大的代理功能，同时支持自定义规则，可以自动根据域名或端口等自动选择是否启用代理，以后有机会可以分享使用 Proxy SwitchOmega 的使用经验。

### HTTPS 拦截配置

[whistle 文档 https 设置部分](https://avwo.github.io/whistle/webui/https.html)

## HTTPS 抓包锦囊<b id="tips"></b>

**注意** 自 iOS 10.3 以后，无论使用上面哪一种工具抓包，安装完 ca 证书以后，还需要**多一步操作**，才能确保证书安装并已启用；

`通用` -> `关于本机` -> `证书信任设置` -> 启用相对应的证书，就可以看到工具中抓取到相应的 HTTPS 请求。

![ios-cer](https://img30.360buyimg.com/uba/jfs/t18550/32/1136654112/137090/91a83930/5abdf196N9bc228ff.png.webp)

这一点跟苹果的 **one more thing** 貌似有那么一点不谋而合。

**注意** Android 部分手机（如小米、华为）无法安装证书时候，不妨试试在电脑端将证书导出，打开手机 FTP 或者利用数据线传输到手机某个文件夹，点开 WLAN 配置 -> 高级设置 -> 安装证书 选择对应的证书安装即可。亦或是因为权限问题无法安装，打开安全隐私，选择信任 `未知来源`。

此办法对于安装测试版 APP 也管用 :)

have fun :D