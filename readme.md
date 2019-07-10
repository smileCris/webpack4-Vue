# Webpack4 搭建 Vue 项目


### 项目运行
```bash
# 安装依赖
$ npm install

# 运行
$ npm start

# 打包
$ npm run build
```

### 项目搭建
1. 新建createVue文件夹，进入文件夹，`npm init`初始化项目
2. 安装webpack四件套 
`npm i webpack webpack-cli webpack-dev-server webpack-merge --save-dev`
3. 创建目录文件
```
createVue
  |--dist
  |--build
    |--webpack.prod.js
    |--webpack.dev.js
  |--src
    |--index.js
    |--app.vue
  |--index.html
```

```
// webpack.base.js
// 存放 dev 和 prod 通用配置
const webpack = require('webpack');
const path = require("path");
module.exports = {
  entry: './src/index.js', //入口
  module: {
    rules: []
  },
  plugins: [
    // 解决render后面的hash每次都改变
    new webpack.HashedModuleIdsPlugin(),
  ]// 插件
};
```

```
// webpack.dev.js
// 存放 dev 配置
const merge = require('webpack-merge');
const common = require('./webpack.base.js');
const path = require('path');

module.exports = merge(common, {
  devtool: 'inline-source-map',
  devServer: { // 开发服务器
    contentBase: '../dist'
  },
  output: { // 输出
    filename: 'js/[name].[hash].js', // 每次保存 hash 都变化
    path: path.resolve(__dirname, '../dist')
  },
  module: {},
  mode: 'development'
});
```

```
// webpack.prod.js
// 存放 prod 配置
const path = require('path');
// 合并配置文件
const merge = require('webpack-merge');
const common = require('./webpack.base.js');

module.exports = merge(common, {
  module: {},
  plugins: [],
  mode: 'production',
  output: {
    filename: 'js/[name].[contenthash].js', //contenthash 若文件内容无变化，则contenthash 名称不变
    path: path.resolve(__dirname, '../dist')
  }
});
```

```
// index.js
// 需 npm i vue --save
import Vue from 'vue';
import App from './App.vue'
import './index.scss'
new Vue({
  el: '#app',
  render: h => h(App)
});
```

```
<!-- app.vue -->
<template>
  <div id="app">
    hello world
  </div>
</template>

<script>
export default {
  name: 'app'
}
</script>
```

```
<!-- index.html -->
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <title>Suporka Vue App</title>
  </head>
  <body>
    <div id="app"></div>
  </body>
</html>
```
4. 安装vue核心解析插件
`npm i vue-loader vue-template-compiler --save-dev`
- 在基本配置base文件中配置vue的解析

```
// webpack.base.js

// ...
// vue-loader 插件
const VueLoaderPlugin = require('vue-loader/lib/plugin');
module.exports = {
  //...
  module: {
    rules: [
      {
        test: /\.vue$/,
        loader: 'vue-loader'
      }
    ]
  },
  plugins: [
    // 引入插件
    new VueLoaderPlugin(),
  ]
};
```
5. 安装 html 模板解析插件
`npm i html-webpack-plugin --save-dev`
- 在基本配置base文件中配置html模板解析
```
// webpack.base.js

// ...
// html插件
const HtmlWebpackPlugin = require('html-webpack-plugin');
module.exports = {
  //...
  plugins: [
    //...
    new HtmlWebpackPlugin({
      template: path.resolve(__dirname, '../index.html'),
    }),
  ]
};
```


参照[Webpack4 搭建 Vue 项目 - 掘金](https://juejin.im/post/5b7d350951882542f3278b11)
官网[Webpack官网](https://www.webpackjs.com/concepts/)