# webpack 基础配置

## webpack 的打包

**webpack 可以实现零配置**

`npx webpack`

打包后的的文件在系统生成的**dist**文件夹中

**webpack.config.js 的命名**

**webpack.config.js**是 webpack 默认的配置文件名称，

如果想用其他文件名 那么 在执行打包命令的时候 应该这么写

`npx webpack --config webpack.newconfig.js`

**package.json 中 scripts 对象的配置**

```javascript
 "scripts": {
    "build": "webpack",
  },
> npm run build
```

如果我想在**build**之后 加上自己定义的 webpack 配置文件名，应该这么写

`> npm run build -- --config webpack.config.my.js`

## webpack-dev-server

### 零配置的 webpack-dev-server

`> npx webpack-dev-server`

这个时候 系统会启动一个 默认 8080 端口的服务器，它基于的是 expresss。

访问http://localhost:8080后进入的是一个 静态文件目录的页面。 里面会列出根目录下的所有文件

### webpack-dev-server 相关配置

```javascript
module.exports = {
  devServer: {
    port: 3000, // 端口
    progress: true, // 进度条显示
    contentBase: "./build", // 以当前文件夹 为入口
    compress: true //一切服务都启用 gzip 压缩：
    open:true,//打包完成后 自动打开浏览器到服务器地址
  },
  mode: "production", //模式 production || development
  entry: "./src/index.js", //入口文件
  output: {
    path: path.resolve(__dirname, "dist"),
    filename: "bundle.js"
  }
};
```
