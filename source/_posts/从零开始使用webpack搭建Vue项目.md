---
title: 从零开始使用webpack搭建Vue项目
date: 2021-02-01 17:24:09
tags: webpack
---

# 项目初始化

1. 新建文件夹 webpack-test, 使用*npm init*初始化项目, 一路回车，生成 packge.json 文件

```js
// 初始化
npm init
```

# webpack 配置

## webpack 安装

```js
// 安装 webpack
npm install webpack webpack-cli --save-dev
```

## webpack 配置

1. 新建 built 文件夹
2. 在文件夹里面新建 webpack.config.js 文件

```js
const path = require('path');
module.exports = {
  entry: './src/main.js',
  output: {
    filename: 'main.js',
    path: path.resolve(__dirname, '../dist'),
  },
};
```

3. 新建 src 文件夹并在文件夹里面新建 main.js 文件

4. 然后执行

```js
npx webpack --config built/webpack.config.js
```

# Vue

## 安装 vue

```js
// 安装 vue
npm install vue
// 安装 vue-loader
npm install -D vue-loader vue-template-compiler html-webpack-plugin webpack-dev-server
```

# 配置热重载

```js
npm install -D webpack-dev-server
```

```js
const { resolve } = require('path');
const VueLoaderPlugin = require('vue-loader/lib/plugin');
const htmlWebpackPlugin = require('html-webpack-plugin');
module.exports = {
  // 配置打包模式 development 或者 production
  mode: 'development',
  // 将编译后的代码映射回原始源代码
  // https://webpack.docschina.org/configuration/devtool/
  devtool: process.env.NODE_ENV === 'production' ? 'false' : 'source-map', //配置构建额 sourcemap
  devServer: {
    contentBase: resolve(__dirname, '../dist'),
    port: 9000,
  },
  // 打包入口
  entry: './src/main.js',
  output: {
    filename: 'index.js',
    path: resolve(__dirname, '../dist'),
  },
  module: {
    rules: [
      // 使用 vue-loader 解析 vue 文件
      // https://vue-loader.vuejs.org/zh/guide/#%E6%89%8B%E5%8A%A8%E8%AE%BE%E7%BD%AE
      { test: /\.vue$/, loader: 'vue-loader' },
      // 它会应用到普通的 `.js` 文件
      // 以及 `.vue` 文件中的 `<script>` 块
      {
        test: /\.js$/,
        loader: 'babel-loader',
      },
      // 它会应用到普通的 `.css` 文件
      // 以及 `.vue` 文件中的 `<style>` 块
      {
        test: /\.css$/,
        use: ['vue-style-loader', 'css-loader'],
      },
    ],
  },
  plugins: [
    // 它的职责是将你定义过的其它规则复制并应用到 .vue 文件里相应语言的块。
    // 例如，如果你有一条匹配 /\.js$/ 的规则，那么它会应用到 .vue 文件里的 <script> 块。
    new VueLoaderPlugin(),
    // 使用自定义的 html, 并自动引入打包后的依赖
    new htmlWebpackPlugin({ template: 'public/index.html' }),
  ],
};
```
