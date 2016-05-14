title: Questions Of Front-End
category : 技术分享
date: 2015-06-27 08:32:19
tags: [Javascript, HTML, CSS, Front-End]
---

前端问题总结整理，持续更新和学习，找到想做的就加油做个行动派吧！
<!--more-->
> 千万不要为了当前不好而跳槽，一定是要因为将来会更好而跳槽！

# HTML

+ 盒子模型
margin - border - padding - content - padding - border - margin
上下两个元素之间会有margin合并问题

+ 块级元素和行内元素区别
http://blog.csdn.net/chen_zw/article/details/8713205
http://segmentfault.com/a/1190000000654770

# CSS

+ 实现一个叉号有几种实现方式
1.切图 2.先用一个span画一个长方形，圆角，然后使用transform倾斜 3.CSS3中使用Webfont 4.伪类
```css
TODO 代码实现
```

+ CSS常用选择器
ID、类、伪类、父子选择器等等

# Javascript

+ 原型链 prototype
[MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Inheritance_and_the_prototype_chain)

实现的本质是重新原型对象。

+ 原生ajax写法
XMLHttpRequest, XMLHttpResponse

+ 事件模型和事件传播机制问题
浏览器中事件可以分为三种：鼠标事件；键盘事件；HTML事件；
捕获和冒泡的顺序在不同浏览器中不同，IE下事件流采用的是冒泡，基于DOM标准兼容浏览器通常采用先捕获后冒泡方式。

``addEventListener() & removeEventListener()``
所有 DOM 节点都包含这两个方法，都接受三个参数：要处理的事件名，作为事件处理程序的函数，一个布尔值。最后这个参数布尔值，``true``表示在捕获时调用事件处理函数，``false``表示在冒泡时调用时间处理函数。

```javascript
var btn = document.getElementById("myBtn");
btn.addEventListener("click", function(){
  alert("click");
}, flase);
```

+ 跨域 JSONP 实现原理
原理：回调函数
JSONP 由两部分组成：回调函数和数据。

For Example:
```javascript
function handleResponse(response) {
  alert("You're at IP " + response.ip + ", which is in " + response.city);
}
var script = document.createElement("script");
script.src = "http://freegeoip.net/json/?callback=handleResponse";
document.body.insertBefore(script, document.body.fisrtChild);
```

+ ``<script>``中defer和asyc区别和加载顺序
  - asyc: ``异步脚本``虽然立即下载脚本，不应该方案页面中其他操作，比如下载其他资源或等待加载其他脚本。只对外部脚本有效。不能保证按照指定的先后顺序执行，建议异步脚本不要在加载期间修改 DOM。
  - defer: ``延迟脚本``可以延迟到文档完全被解析和显示之后再执行。只针对外部脚本有效。在现实当中，延迟脚本不一定按照顺序执行，不一定会在 DOMContentLoaded 事件触发前执行，因此最好只包含一个延迟脚本

+ 闭包：指有权访问另一个函数作用域中的变量和函数。

+ 作用域：当代码在一个环境执行时，会创建变量对象的一个作用域。
理解：类似 Java 的成员变量
```javascript
var color = "blue";
function changeColor() {
  var anotherColor = "red";

  function swapColor() {
    var tempColor = anotherColor;
    anotherColor = color;
    color = tempColor;
    // 这里可以访问 color, anotherColor 和 tempColor
  }
  // 这里可以访问 color, anotherColor
  swapColor();
}
// 这里只能访问 color
changeColor();
```


