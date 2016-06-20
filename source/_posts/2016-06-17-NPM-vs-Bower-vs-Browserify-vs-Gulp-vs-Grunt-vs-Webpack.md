---
layout: post
title: 【译】NPM vs Bower vs Browserify vs Gulp vs Grunt vs Webpack
date: 2016-06-17 14:35:33
category : 译文计划
tags: [翻译, Javascript, Front-End]
---

这是一篇在 Stack overflow 看到的一篇问题和回答，对于自己有一些解惑

<!-- more -->

[Source Link](http://stackoverflow.com/questions/35062852/npm-vs-bower-vs-browserify-vs-gulp-vs-grunt-vs-webpack)

## 作者问题

> 描述：我正在试着总结关于最流行的 Javascript 包管理器，打包器和任务执行器的知识。如果有错误请纠正我。

- `npm` & `bower` 是包管理工具。他们只是将依赖文件下载，并不知道在下载这些文件的基础上如何编译项目。他们知道的是在获取所有依赖之后去调用 `webpack`/`gulp`/`grunt` 。
- `bower` 很像 `npm` ，但是编译依赖树不在行（不像 `npm` 那样递归着进行）。意味着 `npm` 获取每一依赖（可能获取几次相同的文件），然而 `bower` 期望你手动去包含子依赖。有时候 `bower` 和 `npm` 可以一起被使用，分别作用前端和后端（在前端每一mb都很重要）。
- `glup` 和 `gulp` 是任务运行器，目的是将能够被自动化的所有事任务自动化执行。（比如编译css/sass，处理图片，打包还有最小化混淆代码）。
- `grunt` vs `gulp` （就像`maven` vs `gradle` 或是 配置 vs 编码）。Grunt 是基于分离独立的任务配置的，每一个任务开始／处理／关闭文件。Gulp 需要少量代码，并且基于 node streams，那允许它编译链（w/o重复打开相同文件）而且执行很快。
- `webpack` (`webpack-dev-server`) 对于我来说，它是任务执行器随着变化热加载，那些允许你忘记关于所有js/css的监视器。
- `npm`/`bower`+plugin 可以替代任务运行器。他们的能力经常交叉因此如果你需要使用 `gulp`/`grunt` 在 npm+plugin 之上时存在着不同的潜在影响。但是任务运行器处理复杂任务定义的更好（比如 “在每一个编译打包，从ES6转义成ES5，在所有浏览器仿真器上运行，制作镜像还有通过ftp部署到dropbox”）。
- `browserify` 允许为浏览器打包node模块。`browserify` vs `node's require` 就像 [AMD vs CommonJS](https://addyosmani.com/writing-modular-js/)

### Questions

+ 什么是 webpack 和 webpack-dev-server ？官方文档说它是模块打包器，但对于我而言，它知识任务运行工具。有什么不同？
+ 你在哪里用到 browserify ？我们能不能和 node／ES6 一样做？
+ 你什么时候在 npm + plugins 基础上使用 `gulp`/`grunt` ？

## Beat Answer

`Webpack` 和 `Browerify` 做了很多相同的工作，比如用于在一个浏览器环境钟打包你的模块。这个模块就是一个 Node 特征，它不在浏览器中存在，并且 ES6 中的 `import` 还没有在任何浏览器中实现，这就是为什么需要被打包。但是，他们在很多方式上是有区别的，`Webpack` 默认提供很多工具（比如代码拆分），`Browerify` 只能在下载插件之后才能做这些，但是使用这两种都能实现相似的效果。它取决于个人习惯（我常使用 `Webpack` ）。`Webpack` 不是一个任务运行器，它只是你通过CLI或是任务运行器直接运行文件的一个中间过程器。

`Webpack-dev-server` 提供一些类似于 `Browser-sync` -a server，你可以将你的 app 部署的地方，并且验证你的前端开发进程直接通过 dev-server 自动刷新浏览器或者在没有热部署的情况下传播变化（比如 React components）。

我为了项目的完整和简单的任务编写已经使用`Gulp`，但是我后来发现我既不需要`Gulp`也不需要`Grunt`。所有我需要的都可以使用`npm`组合脚本去运行第三方工具利用它们的 API 完成。在`Gulp`，`Grunt`和`npm script`之间选择取决于你的需要、JS 经验和你工作时的开发经验。

当然`Gulp`中的任务是易读的，甚至与JS不是很相似，它还是引用和学习另一个工具，并且我个人倾向于缩小我的依赖并且保持简单。另外一面，使用npm组合脚本和运行文件（配置和执行`Webpack`文件中函数）替代这些任务是更具有挑战性的。但是重要的是他们三个的结果是相同的。

举例说我建议你看一看 [react starter project](https://github.com/kriasoft/react-starter-kit) ，它可以向你很好的展示`npm`组合脚本，`Webpack`和`browser-sync`。即使你可以处理你的源文件，如果你愿意，你可以使用`Gulp`或者`Grunt`运行你的开发服务，我更喜欢第一个选项。