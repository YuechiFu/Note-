# webpack 第一节

## 一. entry & output

### 一对一 string

```javascript
const path = require("path");
const HtmlWebpackPlugin = require("html-webpack-plugin");
const entryPath = "./src/static/";
const outputPath = path.resolve(__dirname, "./build");

module.exports = {
  devServer: {
    port: 3001,
    progress: true,
    hot: true
  },
  entry: entryPath + "js/main.js",
  output: {
    path: path.resolve(outputPath, "js"),
    filename: "bundle.main.js"
  }
};
```

### 多对一 array public/

```javascript
const path = require("path");
const HtmlWebpackPlugin = require("html-webpack-plugin");
const entryPath = "./src/static/";
const outputPath = path.resolve(__dirname, "./build");
module.exports = {
  mode: "production",
  entry: [
    entryPath + "js/entry.01.js",
    entryPath + "js/entry.02.js",
    entryPath + "js/entry.03.js"
  ],
  output: {
    path: path.resolve(outputPath, "js"),
    filename: "bundle.entryAll.js"
  },
  devServer: {
    port: 3001,
    progress: true,
    hot: true,
    inline: true
  }
};
```

### 多对多 object

```javascript
const path = require("path");
const HtmlWebpackPlugin = require("html-webpack-plugin");
const entryPath = "./src/static/";
const outputPath = path.resolve(__dirname, "./build");

module.exports = {
  entry: {
    bundle_01: entryPath + "js/entry.01.js",
    bundle_02: entryPath + "js/entry.02.js",
    bundle_03: entryPath + "js/entry.03.js"
  },
  output: {
    path: path.resolve(outputPath, "js"),
    filename: "[name].js"
  }
};
```

## 二. plugin

### html-webpack-plugin

上述又说道 当有存在多对多的 entry & output 时，
我们每次都要单独去引入对应的 output.filename。
这样就很麻烦，那么有没有一种 将所有 output.filename 打包成一块的呢。

`npm install html-webpack-plugin --save -D`

### 实例

```javascript
const path = require("path");
const HtmlWebpackPlugin = require("html-webpack-plugin");
const entryPath = "./src/static/";
const outputPath = path.resolve(__dirname, "./build");
module.exports = {
  mode: "development",
  devServer: {
    port: 3001,
    hot: true,
    inline: true,
    contentBase: "./build",
    index: "bundle.html"
  },

  /*  entry: entryPath + "js/main.js",
  output: {
    path: outputPath,
    filename: "./js/bundle.main.js"
  }, */
  entry: [
    entryPath + "js/entry.01.js",
    entryPath + "js/entry.02.js",
    entryPath + "js/entry.03.js"
  ],
  output: {
    path: outputPath
    filename: "./js/bundle.entryAll.js"
  },
  plugins: [
    new HtmlWebpackPlugin({
      template: entryPath + "index.html", //模板html文件
      filename: "../bundle.html", // 打包后的文件
      title: "title",
      hash: true
    })
  ]
};
```
要注意的是 htmlWebpackPlugin中的 **filename** 属性 的路径是 根据 **output.path** 来决定的

**上面 例子里的ouput.path 是 ./build**

**所以此时的filename 应该是 "./bundle.html"**

另外，以下这两种写法，虽然结果都是 **./build/js/bundle.entryAll.js **,

但我们如果 在webpack-dev-server 按照第二种写法写， 此时 html-webpack-plugin 下的filename  理论上应该改成，**./bundle.html**

打包后你就会发现，我们访问的时候  会显示 **not Get/** 这是因为，**ouput.path** 就已经决定了输出目录为 **./build/js**,

而你的bundleIndex.html 是在 **./build**下。所以 server 不会去处理 ，所以当 html 和 js 不在同级目录下时 推荐 的一种写法 

如果只是 ```npx webpack``` 去做静态打包，两种没有区别 

```javascript
output: {
    path: outputPath
    filename: "./js/bundle.entryAll.js"
  },
  
  output: {
    path: path.resolve(outputPath,"./js")
    filename: "bundle.entryAll.js"
  },

```
### 参数介绍

##### title

**设置生成的 html 文件的标题。**
记住要在模版文件中加上 `<title><%= htmlWebpackPlugin.options.title %></title>`

##### filename

**打包后的 html 文件名。 默认是 index.html**
有一点要注意。就是路径问题。
在以下实例中 我们需要将 html 文件直接放在父文件夹中。
所以会有 filename:'../outputIndex.html'

##### template

**根据自己的指定的模板文件来生成特定的 html 文件。**
这里的模板类型可以是任意你喜欢的模板，可以是 html, jade, ejs, hbs, 等等，
但是要注意的是，使用自定义的模板文件时，需要提前安装对应的 loader，
否则 webpack 不能正确解析。以 jade 为例。

##### inject

**注入选项。有四个选项值 true, body, head, false.**
true:默认值，script 标签位于 html 文件的 body 底部
body:同 true
head:script 标签位于 head 标签内
false:不插入生成的 js 文件，只是单纯的生成一个 html 文件

##### favicon

**给生成的 html 文件生成一个 favicon。**
属性值为 favicon 文件所在的路径名。
favicon: 'path/to/yourfile.ico'

##### minify

**minify 的作用是对 html 文件进行压缩**
minify 的属性值是一个压缩选项或者 false 。
默认值为 false, 不对生成的 html 文件进行压缩。来看看这个压缩选项

    minify: {

    	caseSensitive: true, //是否对大小写敏感，默认false

    	collapseBooleanAttributes: true,//是否简写boolean格式的属性如：disabled="disabled" 简写为disabled  默认false

    	collapseWhitespace: true,//是否去除空格，默认false

    	minifyCSS: true,//是否压缩html里的css（使用clean-css进行的压缩） 默认值false；

    	minifyJS: true,//是否压缩html里的js（使用uglify-js进行的压缩）

    	preventAttributesEscaping: true,//Prevents the escaping of the values of attributes

    	removeAttributeQuotes: true,	//是否移除属性的引号 默认false


    	removeComments: true,//是否移除注释 默认false

    	removeCommentsFromCDATA: true,//从脚本和样式删除的注释 默认false

    	removeEmptyAttributes: true,//是否删除空属性，默认false

    	removeOptionalTags: false, //  若开启此项，生成的html中没有 body 和 head，html也未闭合

    	removeRedundantAttributes: true, //删除多余的属性

    	removeScriptTypeAttributes: true,//删除script的类型属性，在h5下面script的type默认值：text/javascript 默认值false

    	removeStyleLinkTypeAttributes: true,//删除style的类型属性， type="text/css" 同上

    	useShortDoctype: true,//使用短的文档类型，默认false
    }

##### hash

hash 选项的作用是 给生成的 js 文件一个独特的 hash 值，
该 hash 值是该次 webpack 编译的 hash 值。
默认值为 false 。

    plugins: [
    	new HtmlWebpackPlugin({
    		hash: true
    	})
    ]

##### cache

**默认是 true 的，表示内容变化的时候生成一个新的文件。**

##### chunks

主要用于多入口文件，

当你有多个入口文件，那就回编译后生成多个打包后的文件，那么 chunks 就能选择你要使用那些 js 文件

而如果没有指定 chunks 选项，默认会全部引用。

```javascript
    entry: {
    	index: path.resolve(__dirname, './src/index.js'),
    	devor: path.resolve(__dirname, './src/devor.js'),
    	main: path.resolve(__dirname, './src/main.js')
    }

    plugins: [
    	new httpWebpackPlugin({
    		chunks: ['index','main']
    	})
    ]
```

##### excludeChunks

**排除掉一些 js,**

```javascript
    entry: {
    	index: path.resolve(__dirname, './src/index.js'),
    	devor: path.resolve(__dirname, './src/devor.js'),
    	main: path.resolve(__dirname, './src/main.js')
    }

    plugins: [
    	new httpWebpackPlugin({
    	 excludeChunks: ['devor.js']//和的等等效
    	})
    ]
```

### 实例

这里我们来创建一个实例。我们创建了三个 html 模版，

**/public/mask,list,test.html**

前面有提到，多对多的情况下。 我们希望 各自的 js 能被 各自的引用。
也就是说，利用 html-webpcak-plugin 将三个 html 分别打包，并且分别引用对应的打包后的 js 文件。
打包的 html 文件 放在 **build/...html** ，
js 文件放在 **build/js/...js**

##### html 模版

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <title><%= htmlWebpackPlugin.options.title %></title>
    <!-- 这里不需要引入 脚本文件  -->
    <!-- 打包后的html文件中 会自己添入对应的js -->
  </head>
  <body>
    <h2>list</h2>
  </body>
</html>
```
