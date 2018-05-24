---
layout: post
title: 【译】Webpack 4 mode and optimization
date: 2018-04-18 14:26:11
category : 前端技术
tags: [构建工具, Webpack]
---

原文 [https://medium.com/webpack/webpack-4-mode-and-optimization-5423a6bc597a](https://medium.com/webpack/webpack-4-mode-and-optimization-5423a6bc597a)

作者 [Tobias Koppers](https://medium.com/@sokra)

webpack 增加了一个模式配置（mode option）。下面来解释一下具体内容。

<!-- more -->

![](https://img14.360buyimg.com/uba/s750x500_jfs/t17446/171/1782320374/354853/1568df4e/5ad6eac5Na2c1d8ee.jpg!cc_2x1)

webpack 4 增加了一个`模式`选项（`mode` option）。**并且这个配置是必须存在的**。（实际上它不是必须的配置项，但如果你省略掉没有配就会有警告。）

webpack 4 目前有**两个默认配置**，`development` 和 `production` 。这两个值可以在 `mode` 中设置。

设置 `development` 将会带给你最好*的开发体验，它专注于以下几点：

* 浏览器 debug 工具
* 开发周期内的快速增量编译
* 运行时有用的错误信息

（*实际上这里指最好的体验取决于你使用的方式，我们尽力在这里囊括了大多数通用部分）

设置 `production` 将会带给你在发布应用时的一系列有用的默认设置，它专注于以下几点：

* 更小的输出包尺寸
* 运行时高效的代码
* 忽略掉只在开发时启用（`development-only`）的代码
* 不会暴露源码或者文件路径
* 简化使用打包后资源过程

最后一点非常重要。它基本意味着 `production` 会提供给你优化后的资源包，但这不是完美的优化有的资源包。这里还存在着很多可优化点，但是它们会让结果更难使用。这些优化点是有意被忽略的，因为在这里，我们更看重入门的体验高于完美的优化。大部分优化点也只在大型应用上起作用。

***

随着这个 `mode` 配置的引入，我们尝试去减少 build 时必要配置。我们尝试着去用一些默认项去覆盖通用的配置。

然而从我们的经验来看我们也知道默认配置并不适用于所有人。不同的团队有不同的需求，有时候会因为遗留代码、有时候会因为个人喜好、有时候会因为特殊的应用或者有时候使用者会认为这并不是最好通用解决方案。许多人确实想要修改默认配置以适应他们自己的项目。We got you covered. 增加 `mode` 并不意味这我们移除掉了这些配置。这里面仍然是一切皆可配置。实际上我们使内部大多数优化都是可配置的（你可以禁用掉他们）。

`mode` 选项是通过在配置中设置默认项实现的。这个不会被其他配置执行的 `mode` 也不会做任何行为。

下一部分就会更深入地讨论因为`mode`或其他选项而影响的配置。

***

### devtool

在 `development` 模式下默认是 `eval`。否则不使用 devtool。

`eval` devtool 没有提供最佳的质量，但是拥有很好的性能。这就是我们选择的折中方案。看一看这些配置的文档，这可以获取更高质量的 sourcemap。

* 📉📉📉缺点：慢，bundle 体积大
* 📈📈📈优点：优化 debug 的体验

### cache

只在 `development` 模式下启用，否则禁用缓存。

缓存模块可以避免在没有改变时重建。

在内存缓存只在 `watch` 模式下有用，并且我们假设你在开发时正在使用 `watch` 模式。不用缓存时，内存占用率更低。

* 📉缺点：内存占用
* 📈📈📈优点：更快的增量编译

### module.unsafeCache

只在 `cache` 启用时启用，否则禁用。

缓存机械依赖项可以避免重新解析它们。

* 📉缺点：内存占用，缓存入口可能错误
* 📈📈📈优点：更快的增量编译

### output.pathinfo

只在 `development` 模式下启用，否则禁用。

这些额外的注释对于 debug 很有作用，尤其是使用 `eval` devtool。

* 📉缺点：bundle 体积大，暴露路径信息
* 📈优点：提升生成代码的可阅读性

### performance

只在 `production` 模式下启用，否则禁用。

体积限制只对最小化资源起作用，同时伴随着性能开销。因此它只在生产模式下启用。

* 📉缺点：算法消耗
* 📈优点：对打包 bundle 体积大小产出警告

### optimization.removeAvailableModules

总是开启。

当他们在父级 chunk groups 中都可用时，这些模块会被移除掉。它可以减少资源包的体积。因为有更少的代码生成，更小的资源包就意味着更快的 build 过程。

* 📉📉缺点：算法消耗
* 📈📈📈优点：bundle 体积优化

### optimization.removeEmptyChunks

总是开启。

空 chunks 会被移除掉。这些在文件系统中减少 load 会导致更快的 build。

* 📉缺点：算法消耗
* 📈📈📈优点：更少的请求

### optimization.mergeDuplicateChunks

总是开启。

相等的 chunks 会被合并。结果就是更少的生成代码，更快的 build。

* 📉缺点：算法消耗
* 📈📈📈优点：更少的请求和下载

### optimization.flagIncludedChunks

只在 `production` 模式下开启，否则禁用。

确定作为其他 chunks 子集的 chunks，并且这样方式对齐标记，即当加载较大 chunks 时，不必加载子集。

* 📉缺点：算法消耗
* 📈📈📈优点：更少的请求和下载

### optimization.occurrenceOrder

只在 `production` 模式下开启，否则禁用。

提供更常用的 ids 更小（更短）的值。

* 📉缺点：算法消耗
* 📈优点：bundle 体积优化

### optimization.providedExports

总是开启。

尽可能地确定每一个 module 的 exports。这个信息被用于其他优化或者生成代码。为了消除歧义：为 `export * from` 生成更有效率的代码。

* 📉缺点：算法消耗
* 📈优点：bundle 体积优化，其他优化需求

### optimization.usedExports

只在 `production` 模式下开启，否则禁用。

尽可能地确定每一个 module 的 exports。它依赖于 `optimization.providedExports`。这个信息被用于其他优化或者生成代码。消除歧义：导出不会生成无用的 exports， 当所有的用途都被兼容的时候导出 names 就是零碎的单独字符定义。在最小化中 DCE 会有一处，同时移除掉无用的 exports。

* 📉📉缺点：算法消耗
* 📈📈优点：bundle 体积优化

### optimization.sideEffects

只在 `production` 模式下开启，否则禁用。

认可在 `package.json` 或 `rules` 中的 `sideEffects` 标志去消除 modules。 它依赖 `optimization.providedExports`  和 `optimization.usedExports`。这些依赖都有所开销，但是消除依赖通过减少生成代码在性能上有积极的影响。它也依赖于你自己的代码。为了更好的性能，去尝试吧。

* 📉缺点：算法消耗
* 📈📈📈优点：bundle 体积优化，更少生成代码

### optimization.concatenateModules

只在 `production` 模式下开启，否则禁用。

尝试查找模块图中可以安全连接到单个模块中的段。它依赖于`optimization.providedExports` 和 `optimization.usedExports`。

* 📉📉📉缺点：额外的语法分析，作用域分析和定义重命名
* 📈📈📈优点：运行时性能、bundle 体积优化

### optimization.splitChunks

总是开启。

查找在 chunks 之间哪些 module 被共享，同时将他们拆分到独立的 chunks 中，目的是减少重复或者从 application modules 中分离 vendor modules。

* 📉缺点：算法消耗，额外的请求
* 📈📈📈优点：更少生成代码，更好的缓存，更少的下载请求

### optimization.runtimeChunk

总是开启。

为 webpack 运行时和 chunk manifest 创建一个独立的 chunk。这个 chunk 应该内联到 HTML 中。

* 📉缺点：更大的 HTML 文件
* 📈优点：更好的缓存

### optimization.noEmitOnErrors

只在 `production` 模式下开启，否则禁用。

当发生编译错误的时候不输出资源包。

* 📉缺点：无法使用应用程序的起作用的部分
* 📈优点：没有损坏的 bundles

### optimization.namedModules

只在 `development` 模式下开启，否则禁用。

取代数值型 ID，提供给 module 更有用的命名。

* 📉缺点：bundle 体积增加
* 📈优点：更好的错误报告和 debug

### optimization.nodeEnv

`mode` 值得默认项：`development` 或者 `production`。

定义 `process.env.NODE_ENV` 成为编译时常量值。这就允许移除掉 development only 的代码。

* 📉缺点：开发环境的代码与生产环境的代码不同
* 📈📈优点：减小 bundle 体积，提高运行效率

### optimization.minimize

只在 `production` 模式下开启，否则禁用。

使用最小化工具来压缩输出的资源包，比如（`optimization.minimizer`默认使用的`uglify-js`）。

* 📉📉📉缺点：编译速度降低
* 📈📈📈优点：减小 bundle 体积

### optimization.portableRecords

在文件内记录时启用，否则禁用。

记录中使用的标识与上下文目录有关。

* 📉缺点：影响速度降低
* 📈优点：记录与目录无关
