---
title: 从0到1构建,webpack4持久化缓存优化方案,小白也能看懂的构建过程
date: 2021-04-20
categories:
- 前端工程化
tags:
- webpack
---

### 之前一直对webpack只是了解到一些皮毛，最近抽空学习了一些持久化缓存的必要性以及方案，并自己进行了一次从0到1的项目配置，花了几天的的时间梳理了一下，在此写下分享，方便大家一起交流学习，有写错或理解错的地方还请大佬多多指点。

## 开题
###### 1. 缓存的作用
>每次代码需要更新时，服务器必须重新部署，客户端也必须重新下载资源。因为从网络中获取资源会很慢，这显然非常低效。这就是为什么浏览器会缓存静态资源的原因。但是这样做有一个弊端:如果在部署新的版本中不修改文件名，浏览器会认为它没有更新，就会使用缓存中的版本。
#####  2.webpack配置持久化缓存解决问题
>webpack通过hash配置可以帮我们构建出文件名不同的静态资源文件,这样浏览器就能获取到最新的静态资源了。但是这样每次浏览器对静态资源的请求是全量的，如果可以只修改发生变化的文件的hash值，这样就能进行增量请求，可以大大提高我们的web性能，这就我们所说的持久化缓存。
## 开始构建过程
##（搭建webpack过程）

#####  1.安装webpack
```
npm init
//全局安装
npm install -g webpack
//安装到你的项目目录
npm install --save-dev webpack
```
#####  2.新建文件配置文件及打包项目
1.在空目录下新建两个文件
>将webpack.config.js拆分为webpack.development.config.js、webpack.production.config.js。【在webpack4中,添加了mode('development','production','none')配置项,同时会自动同步process.env.NODE_ENV的值。】(同命令行 --mode development)

webpack.development.config.js
```
module.exports = {
    mode: 'deveopment',
	entry: 	__dirname + "/src/index.js",  //入口
	output: {     //出口
		path:__dirname + "/public",
		filename: "bundle.js"
	},
	devtool:'eval-source-map'   //使得编译后的代码可读性更高，也更容易调试
}
```
webpack.production.config.js
```

module.exports = {
    mode:'production',
	entry: 	__dirname + "/src/index.js",  //入口
	output: {     //出口
		path:__dirname + "/public",
		filename: "bundle.js"
	},
	devtool:'source-map'   //使得编译后的代码可读性更高，也更容易调试
}
```
package.json  修改为:
```
{
  "name": "document",
  "version": "1.0.0",
  "description": "",
  "main": "webpack.development.config.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "start": "webpack --mode development --config webpack.development.config.js",
    "build": "webpack --mode production --config webpack.production.config.js"
  },
  "author": "",
  "license": "ISC"
}

```
2.在空目录下新建public,src目录
index.html  放在public目录下
```
<!-- index.html -->
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <title>Webpack Sample Project</title>
  </head>
  <body>
    <div id='root'>
    </div>
    <script src="bundle.js"></script>
  </body>
</html>
```
index.js  放在src目录下
```
const greeter = require('./Greeter.js');
document.querySelector("#root").appendChild(greeter());
```
greeter.js  放在src目录下
```
module.exports = function() {
  var greet = document.createElement('div');
  greet.textContent = "Hi there and greetings!";
  return greet;
};
```
##### 3.command -1及plugin配置
```
npm run start
```
如出现
![这里写图片描述](https://img-blog.csdn.net/20180720095751875?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0RhaXZvbl9VcA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
选择**webpack-cli**

>打包成功后,public目录打包出bundle.js,浏览器打开index.html

![这里写图片描述](https://img-blog.csdn.net/20180719192702555?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0RhaXZvbl9VcA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
```
npm run build
```
.map为源码映射文件,此时查看bundle.js可以看出js代码已被压缩，因为webpack4在production环境下默认配置了压缩plugin：uglifyjs-webpack-plugin,其它默认插件有：
>development环境
```javascript

const webpack = require('webpack')
module.exports = {
    mode: 'development',
	entry: 	__dirname + "/src/index.js",  //入口
	output: {     //出口
		path:__dirname + "/public",
		filename: "bundle.js"
	},
    devtool:'eval-source-map',   //使得编译后的代码可读性更高，也更容易调试
    plugins:[
        // new webpack.NamedModulesPlugin(),    //已默认配置
        // new webpack.NamedChunksPlugin(),   //已默认配置  
        // new webpack.DefinePlugin({ "process.env.NODE_ENV": JSON.stringify("development") }),  //已默认配置
    ]
}
```
>production环境
```javascript
const webpack = require('webpack')	
module.exports = {
    mode:'production',
	entry: 	__dirname + "/src/index.js",  //入口
	output: {     //出口
		path:__dirname + "/public",
		filename: "bundle.js"
	},
    devtool:'source-map',   //使得编译后的代码可读性更高，也更容易调试
    plugins:[
        // new UglifyJsPlugin(/* ... */),     //已默认配置   js压缩
        // new webpack.DefinePlugin({ "process.env.NODE_ENV": JSON.stringify("production") }),  //已默认配置   定义变量
        // new webpack.optimize.ModuleConcatenationPlugin(),  //已默认配置  作用域(scope hoisting)提升 
        // new webpack.NoEmitOnErrorsPlugin()   //已默认配置   打包过程中报错不会退出 
    ]
}
```
mode 为 none时，默认无plugin配置
##### 4.构建本地服务器
```
npm install  webpack-dev-server --save
```
webpack.development.config.js 添加
```
...
	devServer:{
		contentBase:'./public',   //设置本地服务器目录
		historyApiFallback:true,    //设置为true，所有的跳转将指向index.html
		inline:true, //实时刷新
		port:3000, //端口
	},
...
```
package.json 添加
```
"scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "start": "webpack --mode development --config webpack.development.config.js",
    "build": "webpack --mode production --config webpack.production.config.js",
    "dev": "webpack-dev-server --mode development --config webpack.development.config.js --watch "
},
```
```
npm run dev
```
浏览器打开localhost:3000
![这里写图片描述](https://img-blog.csdn.net/20180720095911218?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0RhaXZvbl9VcA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
此时我们的本地服务器就构建成功了
##### 5.现在我们尝试配置react开发环境,添加loader、babel对文件内容进行处理 (development、production公用配置)
1.添加config.json文件
```
{
    "greetText": "Hello World!"
  }
```
2.修改greeter.js
```

import React, {Component} from 'react'
import config from './config.json';

class Greeter extends Component{
  render() {
    return (
      <div>
        {config.greetText}
      </div>
    );
  }
}

export default Greeter
```
3.修改index.js
```
import React from 'react';
import {render} from 'react-dom';
import Greeter from './greeter';

console.log(document.getElementById('root'))
render(<Greeter />, document.getElementById('root'));
```
4.安装webpack所需loader、babel
```
npm install --save-dev babel-core babel-loader babel-preset-env babel-preset-react
```
5.安装react所需库
```
npm install --save react react-dom
```
6.配置webpack 
```
...
module: {
        rules: [
            {
                test: /(\.jsx|\.js)$/,
                use: {
                    loader: "babel-loader",
                    options: {
                        presets: [
                            "env", "react"    //babel
                        ]
                    }
                },
                exclude: /node_modules/
            }
        ]
    }
...
```
7.
```
npm run dev
```
![这里写图片描述](https://img-blog.csdn.net/20180720101547364?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0RhaXZvbl9VcA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

此时react运行环境已配置好
##### 6.配置css运行环境(development、production公用配置)
1.src新建greeter.css文件
```
.root {
    background-color: #eee;
    padding: 100px;
    border: 3px solid #ccc;
  }
```
2.修改greeter.js
```
...
	import styles from './greeter.css';
...
```
3.安装css loader
```
npm install css-loader style-loader
```
4.配置loader
```
...
	module: {
        rules: [
            {
                test: /(\.jsx|\.js)$/,
                use: {
                    loader: "babel-loader",
                    options: {
                        presets: [
                            "env", "react"    //babel
                        ]
                    }
                },
                exclude: /node_modules/
            },
            {
                test: /\.css$/,
                use: [
                    {
                        loader: "style-loader"
                    }, {
                        loader: "css-loader",
                    }
                ]
            }
        ]
    },
...
```
5.
```
npm run dev
```
>可以看到css已经可以打包运行了

![这里写图片描述](https://img-blog.csdn.net/20180720104111624?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0RhaXZvbl9VcA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

## 持久化缓存优化 (一般production才配置)
##### 1.提取css并压缩
>webpack4中已经remove了 extract-text-webpack-plugin,官方推荐使用mini-css-extract-plugin
```
npm install --save mini-css-extract-plugin optimize-css-assets-webpack-plugin uglifyjs-webpack-plugin
```
##### 2.配置
```
const MiniCssExtractPlugin = require("mini-css-extract-plugin");   
const OptimizeCSSAssetsPlugin = require("optimize-css-assets-webpack-plugin");  
const UglifyJsPlugin = require("uglifyjs-webpack-plugin"); 

...
	optimization: {
        minimizer: [
            new UglifyJsPlugin({     
                cache: true,
                parallel: true,
                sourceMap: false, // set to true if you want JS source maps
                uglifyOptions: {
                    compress: true,
                }
            }),
            new OptimizeCSSAssetsPlugin({})
        ],
    },
    module: {
        rules: [
            {
                test: /(\.jsx|\.js)$/,
                use: {
                    loader: "babel-loader",
                    options: {
                        presets: [
                            "env", "react"    //babel
                        ]
                    }
                },
                exclude: /node_modules/
            },
            {
                test: /\.css$/,
                use:[MiniCssExtractPlugin.loader,'css-loader']
            }
        ]
    },
    plugins[
	    new MiniCssExtractPlugin({
            filename: "[name]-[contenthash].css",    
            chunkFilename: "[id].css"
	    }),
    ]
...
```
```
npm run build
```
>可以看到css已经被单独提取出来，并且js、css都已经被压缩

![这里写图片描述](https://img-blog.csdn.net/20180720110546471?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0RhaXZvbl9VcA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

>不过此时我们可以看到react仍然被打包到bundle中去了,导致bundle.js仍然很大，像这种npm包一般不会有修改，所以我们也应该提取出来
在webpack4中,commonsChunkPlugin已被removed,官方推荐使用[SplitChunksPlugin](https://webpack.js.org/plugins/split-chunks-plugin/)
##### 3.提取公共模块
```
...
	output: {     //出口
		path:__dirname + "/public",
		filename: "[name]-[contenthash].bundle.js",
        chunkFilename: '[name].[chunkhash:5].chunk.js'
	},
...
	optimization: {
        minimizer: [
            new UglifyJsPlugin({     
                cache: true,
                parallel: true,
                sourceMap: false, // set to true if you want JS source maps
                uglifyOptions: {
                    compress: true,
                }
            }),
            new OptimizeCSSAssetsPlugin({})
        ],
        splitChunks: {
            chunks: "all",   //initial、async和all
            minSize: 30000,   //形成一个新代码块最小的体积
            minChunks: 1,  //在分割之前，这个代码块最小应该被引用的次数
            maxAsyncRequests: 5,   //按需加载时候最大的并行请求数
            maxInitialRequests: 3,   //最大初始化请求数
            automaticNameDelimiter: '~',   //打包分割符
            name:'commons',
              //打包后的名字
            cacheGroups: {
                vendors: {
                    test: /[\\/]node_modules[\\/]/,   //用于控制哪些模块被这个缓存组匹配到、它默认会选择所有的模块
                    priority: -10,   //缓存组打包的先后优先级
                    name: 'vendors', 
                    minSize:3000
                },
                default: {
                    minChunks: 2,
                    priority: -20,
                    minSize:3000
                }
            }
        }
    },
...
```
##### 4.配置化生成index.html (自动更新导入的css、js文件)
```
npm install html-webpack-plugin
```
```
const HtmlWebpackPlugin = require('html-webpack-plugin')
...
	plugins:[
        ...
        new HtmlWebpackPlugin({
            template: __dirname + "/src/index.tmpl.html",//new 
        }),
        ...
    ]
...
```
> src目录新建index.tmpl.html   
index.tmpl.html
```
<!DOCTYPE html>
	<html lang="en">
	<head>
	    <meta charset="UTF-8">
	    <meta name="viewport" content="width=device-width, initial-scale=1.0">
	    <meta http-equiv="X-UA-Compatible" content="ie=edge">
	    <title>Document</title>
	</head>
	<body>
	    <div id="root"></div>
	</body>
</html>
```
##### 5.查看成果
```
npm run build
```

##总结
>此时我们可以发现react已被打包到vendors中,bundle.js小了很多,并且多次打包，文件hash值不再修改

![这里写图片描述](https://img-blog.csdn.net/20180720112858730?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0RhaXZvbl9VcA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

>修改.css文件

![这里写图片描述](https://img-blog.csdn.net/20180720112923305?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0RhaXZvbl9VcA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

>可以发现只有css的hash值发生改变，其它文件名并未发生变化,当发布静态文件时，浏览器就对未变化的文件仍然使用缓存，这样就符合我们要实现持久化缓存的预期。




