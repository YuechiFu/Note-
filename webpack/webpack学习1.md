
# webpack (一)

## 一. entry & output 

总结下来就是 打包分为

#### 一对一  string 
	entry:['./public/list.js','./public/test.js','./public/index.js'], 
    output:{
		path : path.resolve(__dirname,'build'),
		filename : 'bundle.js'
	}

#### 多对一  array public/
 	entry:['./public/list.js','./public/test.js','./public/index.js'], 
   	output:{
		path : path.resolve(__dirname,'build'),
		filename : 'bundle.js'
	}

#### 多对多  object
	entry:{
		bundle1:'./public/index.js',
		bundle2:'./public/list.js',
		bundle3:'./public/test.js'
	},
		output:{
		path : path.resolve(__dirname,'build'),
		filename : '[name].js'
	}
#### 


## 二. plugin 

### html-webpack-plugin 

上述又说道  当有存在多对多的entry & output 时，
我们每次都要单独去引入对应的 output.filename。
这样就很麻烦，那么有没有一种 将所有output.filename 打包成一块的呢。 
html-webpack-plugin 就这样来了

###参数介绍

##### title
> 设置生成的 html 文件的标题。
记住要在模版文件中加上 `<title><%= htmlWebpackPlugin.options.title %></title>`

##### filename
> 打包后的html文件名。 默认是 index.html 
有一点要注意。就是路径问题。
在以下实例中 我们需要将html文件直接放在父文件夹中。 
所以会有  filename:'../outputIndex.html'

##### template 
> 根据自己的指定的模板文件来生成特定的 html 文件。
这里的模板类型可以是任意你喜欢的模板，可以是 html, jade, ejs, hbs, 等等，
但是要注意的是，使用自定义的模板文件时，需要提前安装对应的 loader， 
否则webpack不能正确解析。以 jade 为例。

##### inject 
注入选项。有四个选项值 true, body, head, false.
> true:默认值，script标签位于html文件的 body 底部
body:同 true
head:script 标签位于 head 标签内
false:不插入生成的 js 文件，只是单纯的生成一个 html 文件

##### favicon
> 给生成的 html 文件生成一个 favicon。
属性值为 favicon 文件所在的路径名。
favicon: 'path/to/yourfile.ico'

##### minify
> minify 的作用是对 html 文件进行压缩
minify 的属性值是一个压缩选项或者 false 。
默认值为false, 不对生成的 html 文件进行压缩。来看看这个压缩选项

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
hash选项的作用是 给生成的 js 文件一个独特的 hash 值，
该 hash 值是该次 webpack 编译的 hash 值。
默认值为 false 。

	plugins: [
		new HtmlWebpackPlugin({
			hash: true
		})
	]

##### cache
> 默认是true的，表示内容变化的时候生成一个新的文件。

##### chunks
> 主要用于多入口文件，
当你有多个入口文件，那就回编译后生成多个打包后的文件，那么chunks 就能选择你要使用那些js文件
而如果没有指定 chunks 选项，默认会全部引用。

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


##### excludeChunks
> 排除掉一些js,

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

### 实例

这里我们来创建一个实例。我们创建了三个html模版，**/public/mask,list,test.html**
前面有提到，多对多的情况下。 我们希望 各自的js 能被 各自的引用。 
也就是说，利用 html-webpcak-plugin 将三个html 分别打包，并且分别引用对应的打包后的js文件。 
打包的html文件 放在 **build/...html** ，
js文件放在 **build/js/...js**

代码如下。 

###### webpack.config.js

	module.exports = {

	//多个入口文件 分别打包对应的 打包文件， 对象写法，key  对应 filename的变量[name]
	 entry:{
		bundleMask:'./public/js/mask.js',
		bundleList:'./public/js/list.js',
		bundleTest:'./public/js/test.js'
	},
	output:{
		path : path.resolve(__dirname,'build/js'),  

		//生成多个打包文件  匹配entry 中的 key
		filename:'[name].js'
	},
	plugins:[
		new HtmlWebpackPlugin({
			title:"MASK webpack  html",
			hash:true,
			filename:"../outputMask.html", //打包生成的html文件名
			template:'./public/mask.html', // 依赖于那个文件。 就是需要打包的文件
			inject:"head", //打包生成的js应该放在页面的哪个位置。 默认是 footer,
			chunks:['bundleMask']
		}),
		new HtmlWebpackPlugin({
			title:"TEST webpack  html",
			hash:true,
			filename:"../outputTest.html",
			template:'./public/test.html',
			inject:"head",
			chunks:['bundleTest']
		}),
		new HtmlWebpackPlugin({
			title:"LIST webpack  html",
			hash:true,
			filename:"../outputList.html",
			template:'./public/list.html',
			inject:"head",
			chunks:['bundleList']
		}),

		]
	}


##### html模版
```html
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title><%= htmlWebpackPlugin.options.title %></title>
	<!-- 这里不需要引入 脚本文件  -->
	<!-- 打包后的html文件中 会自己添入对应的js -->
</head>
<body>
	<h2>list</h2>
</body>
</html>
```



