# webpack （0）
## 准备

		npm install webpack webpack-dev-server 
		//相关命令
		webpack – 开发环境构建
		
		webpack -p – 生产环境构建(压缩混淆脚本)
		
		webpack --watch – 监听变动并自动打包
		
		webpack -d – 生成 map 映射文件
		
		webpack --colors – 构建过程带颜色输出
		
		webpace-dev-server --devtool eval-source-map --progess --colors
		打包时输入这一行可以直接找到文件如果出错时的位置
		
		npm list  // 查看本地已安装模块清单
		npm list [packageName] // 查看本地已安装模块版本
		npm info [packageName] //查看模块的详细信息 包括各版本号等
		npm view [packageName] version // 查看模块远程最新版本
		npm view [packageName] versions // 查看模块远程所有版本

		npm install [packageName] //安装模块
		npm install [packageName]@xxx.xx //安装模块的指定版本
		npm install [packageName]  -g //全局安装模块
		npm install [packageName] --save 安装好后写入package.json的dependencies中（生产环境依赖）
		npm install [packageName] --save-dev 安装好后写入package.json的devDepencies中（开发环境依赖）

		npm uninstall [packageName] // 删除模块
		npm uninstall [packageName] -g //卸载全局模块
		npm uninstall [packageName] --save  // 删除模块，同时删除模块留在package.json中dependencies下的对应信息
		npm uninstall [packageName] --save-dev  // 删除模块，同时删除模块留在package.json中devDependencies下的对应信

## 遇到的相关问题&解答
- include 表示哪些目录中的 .js 文件需要进行 babel-loader
- exclude 表示哪些目录中的 .js 文件不要进行 babel-loader
## 1.Entry file(入口文件)
### 项目文件说明
##### package.json
	

		{
		"name": "demo1",
		"version": "1.0.0",
		"description": "",
		"main": "main.js",
		"scripts": {
		"dev": "webpack-dev-server --open",   //启动webpack server
		"build": "webpack -p"				 //打包
		},
		"author": "Michele",
		"license": "ISC"
		}

##### webpack.config.js

		const path = require("path")
		module.exports = {
			entry:path.resolve(__dirname,'./main.js'),   //这就是 最重要的入口文件
			output:{
				filename:'./bundle.js'   //这是编译后生成的js文件。 
			}
		}
		
##### index.html
    <!DOCTYPE html>
    <html lang="en">
    <head>
    	<meta charset="UTF-8">
    	<title>Document</title>
    	<script src="./bundle.js"></script>
    </head>
    <body>
    	<div id="contianer">
    		
    
    	</div>
    </body>
    </html>
##### main.js
		document.write("<h1>hello world </h1>")
## 多入口文件
 多入口文件的配置 关键在于webpack.config.js中的 entry 采用了 key-value的格式去配置多个入口，输出文件的文件名改用 变量去命名。 

##### webpack.config.js

    const path = require("path");
    module.exports = {
    	entry:{
    		bundle1:'./main_01.js',     
    		bundle2:'./main_02.js'
    	},
    	output:{
    		filename:'[name].js'   //name对应上面entry对象下的key值
    	}
    }
##### index.html
    <!DOCTYPE html>
    <html lang="en">
    <head>
    	<meta charset="UTF-8">
    	<title>demo2 mutiple entry</title>
    	<script src="./bundle1.js"></script> 
    	<script src="./bundle2.js"></script>
    </head>
    <body>
    	
    </body>
    </html>

## Loader
### 坑

#### 1. webpack版本问题
这个坑踩了好久！
一开始遇到的版本问题 就是 webpack webpack-dev-server 的版本不匹配 ，导致报错。


	Cannot find module 'webpack/bin/config-yargs'



这里一定要注意啊 。 ！！

	npm install webpack webpack-dev-server -g

这里默认 安装最新版本。 这种情况下 是匹配的。 但是当我们回退版本的时候  一定先去看看 [版本的对应关系 ](https://www.npmjs.com/package/webpack-dev-server/v/2.11.3 "版本的对应关系 ")；

#### 2.babel的一些坑
#####  babel-loader @babel/core @babel/preset-env 版本问题。
这里遇到的坑就是、

###### 官方给出的



>  webpack 4.x | babel-loader 8.x | babel 7.x

`npm install -D babel-loader @babel/core @babel/preset-env webpack`

> webpack 4.x | babel-loader 7.x | babel 6.x

`npm install -D babel-loader@7 babel-core babel-preset-env webpack`


就是说 你如果要用这个版本的的babel-loader 以及 babel系列的 那你必须 得配合webpack4.0 

babel-loader 8.0 && babel 7.x，babel-loader 7.x && babel 6.x 必须 配对上。 不然会报错。 版本之间不匹配











