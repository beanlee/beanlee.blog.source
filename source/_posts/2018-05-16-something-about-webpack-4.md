---
title: Webpack 4 实战
date: 2018-05-16 17:48:23
category : 前端技术
tags: [Webpack]
---

最近在参与小组内造“轮子”（具体内容另寻机会再详说）在过程中，了解并且学习到 Webpack v4 的一些内容，趁记忆还深，汇总成下文，鉴于 Webpack 在 Google 搜索有很多相关的文章，本文只介绍开发过程积累的 **4.0** 版本的内容。

PS. 暂时只结合 `React` 项目来说明，后面会找时间补充 `Vue` 项目。

PPS. 前半部分较为基础，有一定经验的同学可以直接跳过看[后半部分实战内容](#实战)。

<!-- more -->

以前也翻译过两篇关于 Webpack 的文章，感兴趣的同学可以点击链接查看：

- [【译】Google 出品 - 利用 webpack 做 web 性能优化](/2018/05/02/blog-translate-web-performance-optimization-with-webpack-from-google-webpack4/) !!! 强烈推荐 !!!
- [【译】Webpack 4 mode and optimization](/2018/04/18/blog-translate-webpack-4-mode-and-optimization/)

<!-- TOC -->

- [Webpack 4 从“零”开始](#webpack-4-从零开始)
- [Webpack 4.x & React 16.x](#webpack-4x--react-16x)
  - [项目初始化](#项目初始化)
  - [进入开发阶段](#进入开发阶段)
    - [dev-server](#dev-server)
    - [热更新 HMR](#热更新-hmr)
- [实战内容（经验积累）](#实战内容经验积累)
  - [提取公共依赖](#提取公共依赖)
  - [文件压缩](#文件压缩)
  - [proxy 接口代理](#proxy-接口代理)
  - [支持多入口](#支持多入口)
  - [预编译 sass、引入 postcss、处理 css 压缩 和 文件分离](#预编译-sass引入-postcss处理-css-压缩-和-文件分离)
  - [支持 CDN 路径替换](#支持-cdn-路径替换)
  - [生产环境 source-map](#生产环境-source-map)
  - [处理图片资源](#处理图片资源)
  - [其他插件](#其他插件)
- [小结](#小结)

<!-- /TOC -->

正文开始

## Webpack 4 从“零”开始

说起 `Webpack V4.0` 就不得不提到这个版本做出的一个大的改变，就是**“零”配置使用**，下面我们就先来看一下 `Webpack` 的“零”。

``` bash
mkdir webpack-4-demo
cd webpack-4-demo
yarn init -y
yarn add webpack --dev
```

首先，我们来创建一个 `demo` 文件夹，做一些简单的初始化信息，并本地安装 `webpack`，此时项目中没有 webpack 配置文件。修改 `package.json` 文件：

``` json
"scripts": {
  "build": "webpack"
}
```

运行

``` bash
yarn build
```

过程中会提示是否安装 `webpack-cli` 直接敲 `yes` 即可。

此时控制台执行结果会有如下报错：

``` bash
ERROR in Entry module not found: Error: Can't resolve './src' in '/Users/xxxx/webpack-4-demo'
npm ERR! code ELIFECYCLE
npm ERR! errno 2
npm ERR! webpack-4-demo@1.0.0 build: `webpack`
npm ERR! Exit status 2
```

> 注意：目前我们没有写配置文件，但 Webpack 会提示没有找到 `./src` 目录下 module。Webpack v4.0 已经可以自动在不配置 entry 的时候自动检索项目文件夹中 src 目录下的 js 文件进行编译了。

接下来我们按照错误提示，在目录下创建 `src` 文件夹，并且新建一个文件 `index.js` 并且输入内容 `console.log('hello webpack 4')`，再次运行 `yarn build`。

这时可以看到编译成功，项目目录下多出一个 `dist` 文件夹，我们事先也并没有配置 `output` 输出指向，`Webpack` 默认将 bundle 好的内容，放在了 dist 文件夹内。

在执行成功的过程中，有一处警告提示：

``` bash
WARNING in configuration
The 'mode' option has not been set, webpack will fallback to 'production' for this value. Set 'mode' option to 'development' or 'production' to enable defaults for each environment.
You can also set it to 'none' to disable any default behavior. Learn more: https://webpack.js.org/concepts/mode/
```

回过头查看刚刚编译好的 `main.js` 文件，此时文件内容已经直接被压缩好的，也就是执行时默认在生产模式下执行的。

Webpack 4 新增的一个 `mode` 配置，具体配置可以详见下面链接，以及笔者之前的翻译文章。

- [【原文】Webpack 4 mode and optimization](https://medium.com/webpack/webpack-4-mode-and-optimization-5423a6bc597a)
- [【译】Webpack 4 mode and optimization](/2018/04/18/blog-translate-webpack-4-mode-and-optimization/)

当然，也可以通过 `--mode` 选项来手动选择 bundle 的模式，比如 `webpack --mode development`。

自此，如果你的项目 src 目录下的内容需要 Webpack 帮你编译，输出在 dist 目录，Webpack 几乎零配置就可以直接“胜任”了。

## Webpack 4.x & React 16.x

上一节简单介绍了 V4.x **“零”**配置的基础应用的说明。当然，实际工作中我们的项目都会比较复杂，上面的内容远不能满足我们的需求，下面我们就以一个 `React 16 + Webpack 4` DEMO 项目为例，还原从零开始搭建基于 `Webpack` 打包编译项目的整个过程。

> Facebook 官方推出的 `create-react-app` 工具已经非常好用，做一些修改也可以满足实际项目上线的需求，但是我们仍希望更多个性化的设置来支持我们的项目，且截至到今天 cra 使用 webpack 3.8 与我们本文介绍 webpack 4 有出入，下面内容不再提及，对 `cra` 感兴趣的同学也可以自行搜索查看了解。

### 项目初始化

首先，重复上面介绍的步骤：（创建目录、安装 react、安装 webpack、安装 babel）

``` bash
mkdir react-webpack-demo
cd react-webpack-demo
## 初始化 package.json
yarn init -y
## 安装 react
yarn add react react-dom
## 安装 webpack
yarn add webpack webpack-cli --dev
## 安装 babel
yarn add @babel/core @babel/preset-env --dev
## 安装 babel-react
yarn add @babel/preset-react "babel-loader@^8.0.0-beta" --dev
```

> 注意：这里使用 babel 转义，此处既可以在项目根目录下创建 `.babelrc` 文件，也可以稍后在 webpack.config.js 中配置，这里我们选择在后者统一配置。

现在我们新建一个配置文件，`webpack.config.js` 代码：

``` javascript
module.exports = {
  module: {
    rules: [
      {
        test: /\.jsx?$/,
        exclude: /node_modules/,
        use: {
          loader: 'babel-loader',
          options: { // babel 转义的配置选项
            babelrc: false,
            presets: [
              require.resolve('@babel/preset-react'),
              [require.resolve('@babel/preset-env'), { modules: false }],
            ],
            cacheDirectory: true,
          },
        },
      },
    ]
  }
};
```

在 `src` 目录下创建 `App.jsx`：

``` javascript
import React from "react";
import ReactDOM from "react-dom";
const App = () => {
  return (
    <div>
      <p>Hello React and Webpack</p>
    </div>
  );
};
export default App;
ReactDOM.render(<App />, document.getElementById("app"));
```

在 `src` 下新建 `index.jsx` 内容如下：

``` javascript
import App from './App';
```

执行 `yarn build` 等待打包结果，此时目录 `dist` 下已经打包好 bundle。

我们接着创建 html 文件，在 src 下创建 `index.html` :

``` html
<!DOCTYPE HTML>
<html lang="zh-CN">
  <head>
    <meta charset="UTF-8">
    <title>Hello React Webpack</title>
  </head>
  <body>
    <div id="app">
    </div>
  </body>
</html>
```

修改 build 的配置，拷贝 html

``` bash
yarn add html-webpack-plugin
```

修改上面的 config：

``` javascript
const HtmlWebPackPlugin = require("html-webpack-plugin");

module.exports = {
  mode: 'production',
  resolve: {
    extensions: ['.js', '.jsx'],
  },
  module: {
    rules: [
      {
        test: /\.jsx?$/,
        exclude: /node_modules/,
        use: {
          loader: 'babel-loader',
          options: {
            babelrc: false,
            presets: [
              require.resolve('@babel/preset-react'),
              [require.resolve('@babel/preset-env'), { modules: false }],
            ],
            cacheDirectory: true,
          },
        },
      },
    ]
  },
  plugins: [
    new HtmlWebPackPlugin({
      template: "src/index.html",
      filename: "index.html"
    })
  ]
};
```

执行 `yarn build` 就可以看到已经打包好的 index.html 和 bundle js 。

### 进入开发阶段

#### dev-server

通过 `webpack-dev-server` 搭建本地 server 服务，目前是通用的解决办法

安装依赖：

``` bash
yarn add webpack-dev-server --dev
```

在 `package.json` 的 scripts 中增加脚本：

``` json
"scripts": {
  "start": "webpack-dev-server --mode development --open",
}
```

运行 `yarn start` 此时会运行 dev-server 服务以用于本地开发。

这里我们可以新建一个配置文件 `webpack.config.dev.js` ：

``` javascript
const path = require('path');

module.exports = {
  mode: 'development',
  devtool: 'cheap-module-source-map',
  resolve: {
    extensions: ['.js', '.jsx'],
  },
  devServer: {
    contentBase: path.join(__dirname, "./src/"),
    host: '127.0.0.1',
    publicPath: '/',
    host: '127.0.0.1',
    port: 3000,
    stats: {
      colors: true,
    },
  },
  module: {
    rules: [
      {
        test: /\.jsx?$/,
        exclude: /node_modules/,
        use: {
          loader: 'babel-loader',
          options: { // babel 转义的配置选项
            babelrc: false,
            presets: [
              require.resolve('@babel/preset-react'),
              [require.resolve('@babel/preset-env'), { modules: false }],
            ],
            cacheDirectory: true,
          },
        },
      },
    ],
  },
  plugins: [
    new HtmlWebPackPlugin({
      template: 'src/index.html',
      filename: 'index.html',
      inject: true,
    })
  ]
};
```

修改 `package.json` 中 scripts：

``` json
"scripts": {
  "start": "webpack-dev-server --config './webpack.config.dev.js'"
}
```

重新执行 `yarn start` 可以看到修改配置后的 dev-server

#### 热更新 HMR

配置热更新就可以实时将修改的代码，反应在 dev-server 跑的应用中，继续修改 config.dev.js 和 App.jsx 增加内容：

``` javascript
// webpack.config.dev.js config 部分
devServer: {
  ...
  hot: true,
  ...
},

// webpack.config.dev.js plugins 部分
...
plugins: [
  new webpack.HotModuleReplacementPlugin(),
  ...
]
...

// app.jsx 页面底部新增
...
if (module.hot) {
  module.hot.accept((err) => {
    if (err) {
      console.error('Cannot apply HMR update.', err);
    }
  });
}
```

执行 `yarn start` 重新启动 server，修改本地代码保存就可以看到控制台里重新编译的信息，浏览器中变化实际修改的内容了。

## 实战内容（经验积累）

下面部分就是在项目过程中积累的一些基于 Webpack 4.x 的优化实战经验汇总。

### 提取公共依赖

将公共依赖如 `react`、`react-dom`、`axio` 等等文件抽取出来独立打包成公共依赖文件，从而减小业务包大小。

`CommonsChunkPlugin` 在 4.0 被取代。

``` javascript
mode: 'production',
output: {
  ...
  chunkFilename: 'js/[name].[chunkhash:8].js',
  ...
},
...

optimization: {
  nodeEnv: 'production',
  ...
  runtimeChunk: {
    name: 'manifest', // 运行时文件
  },
  splitChunks: {
    cacheGroups: {
      commons: {
        test: /[\\/]node_modules[\\/]/,
        name: 'vendor', // 依赖第三方库要提取成的 vendor 的文件
        chunks: 'all', // 提取所有 chunks
      },
    },
  },
  ...
},
```

通过上面的配置，可以将公共依赖和业务代码隔离开来。但是，也会存在一些**隐患**：

- 随着项目复杂度增加，依赖库增多，`vendor.js` 的体积会越来越**臃肿**
- 多页面应用项目，不同页面仍然会加载到在本页面根本无用的公共依赖的冗余代码

所以具体项目需要通过具体的需求来抽离出不同的 chunks 来分别引用，按需引用。

### 文件压缩

配置 `mode: 'production'` Webpack 会使用默认插件 `[UglifyJs](https://github.com/webpack-contrib/uglifyjs-webpack-plugin)` 来进行压缩代码。

官网提到在 `Webpack v4.0.0` 以前内置 `webpack.optimize.UglifyJsPlugin` 的插件，在 Webpack 4 以后，开始使用 ^1.0.0 独立的版本。

``` javascript
const UglifyJsPlugin = require('uglifyjs-webpack-plugin');
const os = require('os');
...
// webpack config
optimization: {
  nodeEnv: 'production',
  minimizer: [
    new UglifyJsPlugin({
      cache: true, // node_modules/.cache/uglifyjs-webpack-plugin
      parallel: os.cpus().length, // 并行 default:true os.cpus().length - 1
      uglifyOptions: {
        ecma: 5,
        mangle: true,
      },
      sourceMap: false,
    }),
  ],
},
```

> 注意：4.0 版本压缩的代码已经放在 `optimization` 下 `minimizer` 节点下。
> 备注：关于 `parallel` 选项，新版的 uglifyjs 已经支持多核 CPU 并行执行，所以已经不需要 `webpack-parallel-uglify-plugin` 插件。

### proxy 接口代理

配合 dev-server 对代理本地启动的 server 某一域名进行代理，解决服务端接口暂时满足要求、本地请求跨域等问题。

``` javascript
devServer: {
  ...
  proxy: {
    "/api": "https://localhost:3000",
    changeOrigin: true, // 支持跨域请求
    secure: true, // 支持 https
  }
}
```

文档见[dev-server-proxy doc](https://webpack.js.org/configuration/dev-server/#devserver-proxy)

### 支持多入口

解决办法目前比较**粗暴**，当前有多少个入口 html 就创建多少个 `HtmlWebpackPlugin` 插件实例。

``` javascript
const fs = require('fs');
const path = require('path');
// 先找到项目指定目录下的所有 html 此处假设我们把入口 html 放在 src/html 下 app1.html、app2.html、app3.html
const appHtmlEntries = fs.readdirSync(resolveToAppRoot('./src/html/'))
  .filter(f => f.match(/\.html?$/))
  .reduce((acc, p) => Object.assign(acc, { [path.basename(p).replace(/\.html?$/, '')]: path.join(resolveToAppRoot('./src/html/'), p) }), {});
...
// 每一个 html 创建一个 HtmlWebpackPlugin 实例
Object.keys(appHtmlEntries).forEach((name) => {
  const pluginHtml = new HtmlWebpackPlugin({
    filename: `${name}.html`,
    template: `src/html/${name}.html`,
    chunks: [`${name}`, 'manifest', 'vendor'],
    inject: true,
  });
  webpackConfig.plugins.push(pluginHtml);
});
```

### 预编译 sass、引入 postcss、处理 css 压缩 和 文件分离

这里要注意 rules 中 loader 数组的顺序，由于 webpack 执行 rules 是从最后一个执行，所以我们配置的顺序也是

预编译 sass --> 处理 postcss (做一些 css 补丁) --> 处理 css --> 压缩独立 css 文件

升级到 4.0，已经不再使用 `extract-text-webpack-plugin` 插件来进行文件抽取，改用专门处理 `MiniCssExtractPlugin` 的插件，配合 `OptimizeCSSAssetsPlugin` 插件来压缩 css 文件。

``` javascript
const autoprefixer = require('autoprefixer');
const postcssFlexbugsFixes = require('postcss-flexbugs-fixes');
const MiniCssExtractPlugin = require('mini-css-extract-plugin');

// rules 配置
{
  test: /\.(css|sass|scss)$/,
  use: [
    {
      loader: MiniCssExtractPlugin.loader, // 这个 loader 放在最后一个执行，将编译好的 css 独立
    },
    require.resolve('css-loader'),
    {
      loader: require.resolve('postcss-loader'),
      options: {
        ident: 'postcss',
        plugins: () => [
          postcssFlexbugsFixes,
          autoprefixer({
            browsers: [
              '>1%',
              'last 4 versions',
              'Firefox ESR',
              'not ie < 9',
            ],
            flexbox: 'no-2009',
          }),
        ],
      },
    },
    require.resolve('sass-loader'),
  ],
},

// optimization 配置
const OptimizeCSSAssetsPlugin = require('optimize-css-assets-webpack-plugin');
const cssnano = require('cssnano');
...
optimization: {
  nodeEnv: 'production',
  minimizer: [
    new OptimizeCSSAssetsPlugin({
      assetNameRegExp: /\.css$/g,
      cssProcessor: cssnano, // 默认使用 cssnano 处理 css
      cssProcessorOptions: {
        reduceIdents: false, // 禁止将 keyframes 自动更名
        mergeIdents: false, // 禁止自动合并 keyframes
        discardUnused: false, // 禁止移除掉未使用的 keyframes
        autoprefixer: false, // 禁止默认删除掉一些前缀，以减少兼容性的问题
        zindex: false, // 禁止自动转换 z-index
        map: false,
      },
    }),
  ],
},
...
// 独立 css 文件
plugins: [
  new MiniCssExtractPlugin({
    filename: 'css/[name].[contenthash:8].css',
  }),
],
```

### 支持 CDN 路径替换

可以将 html 中 css 和 js 的相对引用路径自动替换成配置的前缀路径，用来支持静态资源上线到具体指定的 CDN 路径来增加 app 内静态资源的下载速度。

``` javascript
output: {
  ...
  publicPath: "https://...cdnpath.../assets/" // CDN 资源 URL 前缀
  ...
},
```

### 生产环境 source-map

``` javascript
mode: 'production',
devtool: 'source-map',
optimization: {
  nodeEnv: 'production',
  minimizer: [
    new UglifyJsPlugin({
      ...
      sourceMap: true,
    }),
    new OptimizeCSSAssetsPlugin({
      assetNameRegExp: /\.css$/g,
      cssProcessor: cssnano, // 默认使用 cssnano 这个处理 css，看了一个 clean-css 的方案，4.2 版本才可用，以后再说
      cssProcessorOptions: {
        ...
        map: { inline: false } ,
      },
    }),
  ],
},
```

### 处理图片资源

关键 `loaders` 这里就不罗列配置

- `file-loader` 文件无处理，直接拷贝
- `url-loader` 可以增加 base64 处理
- `svg-url-loader` 处理 svg 文件，也同样支持 base64
- `image-webpack-loader` 图片文件降质压缩

### 其他插件

- `HashedModuleIdsPlugin` 使用更稳定的 moudle id 生成方式
- `webpack.optimize.ModuleConcatenationPlugin` 插件已经不需要单独配置，Webpack 4 已经默认在生产模式下打包时内置开启优化

## 小结

> `并不是每一个人都想成为 Webpack 配置工程师！`

上面引用一句我们“造轮子”时使用的一句 slogan

目前来说： 虽然 `Webpack` 还没有办法达到开箱即用的程度，而且这也不是它的被创造出来的初衷，简单的配置无法满足项目中的实际需求，各种各样的插件和解决方案也层出不穷，相信很多读者和我一样头疼它的很多配置，做了很多尝试也无法达到最优的配置。

然而，它只是一个工具，也说不好再过多长时间会有新的工具来取代 webpack，我们可以共同拭目以待，当然既然你现在用到它，还是有必要花时间了解一下如何更好地让它为你和你的项目服务。

关于 webpack 4 的配置经验我也在摸着石头过这条小河，网上也有诸多优化和解决方案，文中并没有面面俱到的将所有配置都详细说明或者并不是所有配置都是最有选择，欢迎私信留言讨论。
