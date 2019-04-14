# webpack 样式处理

## css-loader/style-loader/sass-loader 的基本配置

css-loader 是用来解析 @import 语法

style-loader 用来把 css 文件 插入到页面中。

loader 顺序 ：**从右向左**,**从下到上**

loader 三种写法 String, Array, Object

```javascript
const path = require("path");
const HtmlWebpackPlugin = require("html-webpack-plugin");
const MiniCssExtractPlugin = require("mini-css-extract-plugin");
const entryPath = "./src/static/";
const outputPath = path.resolve(\_\_dirname, "./build");
const OptimizeCss = require("optimize-css-assets-webpack-plugin");
module.exports = {
  devServer: {
    port: 3001,
    progress: true,
    hot: true,
    inline: true,
    contentBase: "./build",
    index: "bundleIndex.html"
  },
  mode: "production",
  entry: entryPath + "js/main.js",
  output: {
    path: outputPath,
    filename: "./js/bundle.main.js"
  },

  module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          {
            loader: "style-loader",
            options: {}
          },
          "css-loader"
        ]
      },
      {
        test: /(\.scss|\.sass)$/,
        use: [
          {
            loader: "style-loader",
            options: {}
          },
          "css-loader",
          {
            loader: "sass-loader"
          }
        ]
      }
    ]
  },
  plugins: [
    new HtmlWebpackPlugin({
      template: entryPath + "index.html",
      filename: "./bundleIndex.html",
      hash: true,
      removeAttributeQuotes: true
    })
  ]
};
```

以上实现了 对 css scss 的文件处理。 但是 这些 css 内容 都是转成了 style 形式插入到 html 中。

如果想要把这些 scss/css 文件 去打包成 css 文件的话，就需要用到 下面你这个插件了。

## MiniCssExtractPlugin

### 基本用法

MiniCssExtractPlugin 输出的 css 文件是根据 **output.path** 属性来定位的。

所以默认生成文件的位置 就是在 **output.path** 下。

这里我们把它刚在 **./build/css/** 文件夹里

```javascript
const path = require("path");
const HtmlWebpackPlugin = require("html-webpack-plugin");
const MiniCssExtractPlugin = require("mini-css-extract-plugin");
const entryPath = "./src/static/";
const outputPath = path.resolve(\_\_dirname, "./build");
const OptimizeCss = require("optimize-css-assets-webpack-plugin");
module.exports = {
  devServer: {
    port: 3001,
    progress: true,
    hot: true,
    inline: true,
    contentBase: "./build",
    index: "bundleIndex.html"
  },
  mode: "production",
  entry: entryPath + "js/main.js",
  output: {
    path: outputPath,
    filename: "./js/bundle.main.js"
  },

  module: {
    rules: [
      {
        test: /(\.c|\.sc|\.sa)ss$/,
        use: [
          {
            loader: MiniCssExtractPlugin.loader
          },
          // {
          //   loader: "style-loader",
          //   options: {}
          // },
          "css-loader",
          {
            loader: "sass-loader"
          }
        ]
      }
    ]
  },
  plugins: [
    new MiniCssExtractPlugin({
      filename: "./css/[name].[hash].css"
    }),
    new HtmlWebpackPlugin({
      template: entryPath + "index.html",
      filename: "bundleIndex.html",
      hash: true,
      removeAttributeQuotes: true
    })
  ]
};
```

## css 文件的压缩(uglifyjs-webpack-plugin)

```javascript
const path = require("path");
const HtmlWebpackPlugin = require("html-webpack-plugin");
const MiniCssExtractPlugin = require("mini-css-extract-plugin");
const entryPath = "./src/static/";
const outputPath = path.resolve(\_\_dirname, "./build");
const OptimizeCss = require("optimize-css-assets-webpack-plugin");
module.exports = {
  devServer: {
    port: 3001,
    progress: true,
    hot: true,
    inline: true,
    contentBase: "./build",
    index: "bundleIndex.html"
  },
  mode: "production",
  entry: entryPath + "js/main.js",
  output: {
    path: outputPath,
    filename: "./js/bundle.main.js"
  },

  module: {
    rules: [
      {
        test: /(\.c|\.sc|\.sa)ss$/,
        use: [
          {
            loader: MiniCssExtractPlugin.loader
          },
          // {
          //   loader: "style-loader",
          //   options: {}
          // },
          "css-loader",
          {
            loader: "sass-loader"
          }
        ]
      }
    ]
  },
  //优化项
  optimization: {
    minimizer: [new OptimizeCss()]
  },
  plugins: [
    new MiniCssExtractPlugin({
      filename: "./css/[name].[hash].css"
    }),
    new HtmlWebpackPlugin({
      template: entryPath + "index.html",
      filename: "bundleIndex.html",
      hash: true,
      removeAttributeQuotes: true
    })
  ]
};
```
