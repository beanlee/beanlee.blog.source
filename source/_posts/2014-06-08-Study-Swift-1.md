---
layout: post
title : Swift Study 1
category : 技术
tags : [Apple, Swift]
description : Swift is an important chance to find coding passion back to me. So Do Not Lose It! Come on!
date: 2014-06-08 00:00:01
---

Swift is an important chance to find coding passion back to me. So Do Not Lose It! Come on!

<!--more-->

## Swift 学习笔记 一
从2014-06-09开始每日更新，督促自己学习坚持，一周为一篇周期，加油！

### 材料收集
[官方iBook版](https://itunes.apple.com/us/book/the-swift-programming-language/id881256329?mt=11)

IDE Xcode 6 Beta

### Hello World
```swift
    println("Hello World!")
```
### 2014-06-09记录
学习常量(let)、变量(var)定义的方法，字符串参数传递\()，Swift类型推导 var numValue = 123, strValue = "abc"，数组和字典定义方式等等。

函数的定义func，如:
```swift
    func hello(name: String, day: String) -> String {
         return "Hello \(name), Today is \(day) !"
    }
```
可变参数的函数定义，如：
```swift
    func sumOf(numbers: Int...) -> Int {
        var sum = 0
        for number in numbers {
            sum += number
        }
        return sum
    }
    sumOf()
    sumOf(42, 597, 12)”
```
正在看极客学院的 Weather App 的教学视频，看来需要恶补的一些交互还有很多，加油！

### 2014-06-10记录
看完做Weather App的Demo视频，熟悉一个可以解析JSON的工具API

    NSJSONSerialization

重新回头继续看文档，最近加班和参加Interview，阅读的时间少了，要坚持看！

[GitBook 中文版Swift文档](http://numbbbbb.github.io/the-swift-programming-language-in-chinese/)

#### 控制流

使用if和switch来进行条件操作，使用for-in、for、while和do-while来进行循环。包裹条件和循环变量括号可以省略，但是语句体的大括号是必须的。

在类型后面加一个问号来标记这个变量的值是可选的。
```swift
    var optionalString: String? = "Hello"
    optionalString == nil

    var optionalName: String? = "John Appleseed"
    var greeting = "Hello!"
    if let name = optionalName {
        greeting = "Hello, \(name)"
    }
```
if的条件如果是 nil 就是false。

switch case中一定要有default否则编译会报错，因为进入这个条件操作可能会没有出口。

循环中使用 .. 创建的范围不包含上界，如果想包含的话需要使用 ... ，如（0...10 表达从0开始到10，且包括10；0..10 则表单从0开始到10但不包括10）

#### 函数与闭包

前面在视频里面看到了函数的定义方法，这里通过文档再整理一下。

使用func来声明一个函数，使用名字和参数来调用函数。使用->来指定函数返回值。
```swift
    func greet(name: String, day: String) -> String {
        return "Hello \(name), today is \(day)."
    }
    greet("Bob", "Tuesday")
```
### 2014-06-11
枚举、闭包实践。

今天公司足球赛，太累就暂时依照视频教程做了个读取天气的demo，明天继续！

### 2014-06-15
遇上公司加班延误了三天，世界杯开幕熬夜看比赛又导致晚上需要稍微早一点睡，最近学习计划有些延后，今天反省一下！Swift学习今天暂缓停一下，整理了一下个人Resume，同时在这期间了解Mac一个很有用的软件[Dash](https://itunes.apple.com/us/app/dash-docs-snippets/id458034879?mt=12)（汇集SDK文档和Sheets快速查询查看工具），推荐给在Mac环境下做开发的各位朋友！

to be continue...



