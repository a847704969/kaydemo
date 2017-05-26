# kaydemo
这是一个小小的webpack例子。记录一下。
首先你要稍微懂一些node.js的知识。
当下最热门的前端资源模块化管理和打包工具,它能把各种资源，包括 jxs、coffeeJS、less／sass，甚至图片，当作模块来加载和使用。同样它需要一个 webpack.config.js 的配置文件，有专门针对于 css、js 和图片等插件，在 js 中直接通过 require 来使用模块，很方便。
webpack 可以将文件模块按照依赖打包成方便使用的前端资源，还可以将按需加载的模块进行异步加载。

怎么安装Webpack。
1安装node.js

首先需要安装 Node.js ，node自带了包管理工具 npm 。

2安装webpack

使用 npm install webpack -g ，webpack全局安装到了本地环境中，就可以使用 webpack 命令了。

3在项目中使用webpack

通过 npm init 实例化package.json文件。
通过 npm install webpack --save-dev 安装 webpack 到 package.json 文件中。
或者通过 npm install webpack@1.2.x --save-dev 安装指定版本的 webpack 到 package.json 文件中。
通过 npm install webpack-dev-server --save-dev 安装 dev tools 到 package.json 文件中，本地运行webpack服务。

怎么使用Webpack

1、安装 webpack 后，可以使用 webpack 这个命令行工具。主要命令： webpack <entry> <output> 。可以切换到包含webpack.config.js的目录运行命令：

webpack 执行一次开发时的编译
webpack -p 执行一次生成环境的编译（压缩）
webpack --watch 在开发时持续监控增量编译（很快）
webpack -d 让他生成SourceMaps
webpack --progress 显示编译进度
webpack --colors 显示静态资源的颜色


webpack --sort-modules-by, --sort-chunks-by, --sort-assets-by 将modules/chunks/assets进行列表排序
webpack --display-chunks 展示编译后的分块
webpack --display-reasons 显示更多引用模块原因
webapck --display-error-details 显示更多报错信息
2、每个项目下都必须配置有一个 webpack.config.js ，它的作用如同常规的 gulpfile.js/Gruntfile.js ，就是一个配置项，告诉 webpack 它需要做什么。

下面看一个简单的示例：

var webpack = require('webpack');

module.exports = {
    //插件项
    plugins: [
        //提公用js到common.js文件中
        new webpack.optimize.CommonsChunkPlugin('common.js'),
        //将样式统一发布到style.css中
        new ExtractTextPlugin("style.css", {
            allChunks: true,
            disable: false
        }),
        //使用ProvidePlugin加载使用频率高的模块
        new webpack.ProvidePlugin({
            $: "webpack-zepto"
        })
    ],
    //页面入口文件配置
    entry: {
        index : './src/main.js'
    },
    //入口文件输出配置
    output: {
        path: __dirname +'/dist/',
        filename: '[name].js'
    },
    module: {
        //加载器配置
        loaders: [
            { test: /\.css$/, loader: 'style-loader!css-loader' },
            { test: /\.scss$/, loader: 'style!css!sass?sourceMap'},
            { test: /\.(png|jpg)$/, loader: 'url-loader?limit=8192'}
        ]
    },
    //其它解决方案配置
    resolve: {
        extensions: ['', '.js', '.json', '.scss'],
        alias: {
            filter: path.join(__dirname, 'src/filters')
        }
    }
};
entry

entry 是页面入口文件配置，可以是一个文件或者多个入口文件，可以是对象格式或者数组格式。

entry: {
    index : './src/main.js'
} 
entry:['./src/main.js','./src/index.js']
output

output 是对应输出项配置,主要包括 path , filename 和 publishPath 属性。 path 代表输出的路径， filename 代表输出的文件名称， publishPath 代表静态资源发布后的前缀地址。

module.loaders

module.loaders 是最关键的一块配置。它告知 webpack 每一种文件都需要使用什么加载器来处理。 点击这里可以查看loader list 。

module: {
    //加载器配置
    loaders: [
        //.css 文件使用 style-loader 和 css-loader 来处理
        { test: /\.css$/, loader: 'style-loader!css-loader' },
        //.scss 文件使用 style-loader、css-loader 和 sass-loader 来编译处理
        { test: /\.scss$/, loader: 'style!css!sass?sourceMap'},
        //图片文件使用 url-loader 来处理，小于8kb的直接转为base64
        { test: /\.(png|jpg)$/, loader: 'url-loader?limit=8192'}
    ]
}
loader主要有3种使用方式：

1、在页面里面引用资源使用

require("url-loader?mimetype=image/png!./file.png");

2、在webpack.config.js文件夹中使用

{ test: /.png$/, loader: "url?mimetype=image/png" };

3、在命令行中编译使用

webpack --module-bind "png=url-loader?mimetype=image/png";

如上，"-loader"其实是可以省略不写的，多个loader之间用“!”连接起来。

注意所有的加载器都需要通过 npm 来加载，并建议查阅它们对应的 readme 来看看如何使用。

拿最后一个 url-loader 来说，它会将样式中引用到的图片转为模块来处理，使用该加载器需要先进行安装：

npm install url-loader -save-dev

配置信息的参数“?limit=8192”表示将所有小于8kb的图片都转为base64形式（其实应该说超过8kb的才使用 url-loader 来映射到文件，否则转为data url形式）。也可以使用file-loader来加载资源文件。

plugins

plugins 是插件项，这里我们使用了一个 CommonsChunkPlugin 的插件，它用于提取多个入口文件的公共脚本部分，然后生成一个 common.js 来方便多页面之间进行复用。 点击这里可以查看plugins list 。

plugins: [
    //提公用js到common.js文件中
    new webpack.optimize.CommonsChunkPlugin('common.js'),
    //将样式统一发布到style.css中
    new ExtractTextPlugin("style.css", {
        allChunks: true,
        disable: false
    }),
    //使用ProvidePlugin加载使用频率高的模块
    new webpack.ProvidePlugin({
        $: "webpack-zepto"
    })
]
如上，包含两种：

1、第一种webpack自带的一些插件： webpack.ProvidePlugin 、 webpack.optimize.CommonsChunkPlugin ，

2、另外一种则通过 npm 包安装的： ExtractTextPlugin 。

resolve

最后是 resolve 配置，这块很好理解，直接写注释了：

resolve: {
    // require时省略的扩展名，如：require('module') 不需要module.js
    extension: ['', '.js'],
    //别名
    alias: {
        filter: path.join(__dirname, 'src/filters')
    }
}
使用Webpack-dev-server

webpack-dev-server 是基于node.js Express服务，它同时包含了一个基于 Socket.IO 轻量的运行时环境。

'use strict'
var webpack = require('webpack');
var WebpackDevServer = require('webpack-dev-server');
var config = require('./webpack.config');
// 配置代码自动编译和热替换插件
config.entry.unshift('webpack-dev-server/client?http://localhost:9090', "webpack/hot/dev-server");
config.plugins.push(new webpack.HotModuleReplacementPlugin());
// 这里配置：请求http://localhost:9090/index.php，
// 相当于通过本地node服务代理请求到了http://testapi.uhouzz.com/index.php
var proxy = [{
    path: "/index.php/*",
    target: "http://pc.uhouzz.com",
    host: "pc.uhouzz.com"
}]
//启动服务
var app = new WebpackDevServer(webpack(config), {
    publicPath: config.output.publicPath,
    hot:true,
    historyApiFallback: true,
    proxy:proxy
});
app.listen(9090);
如上，引用 webpack 和 webpack-dev-server 模块，通过WebpackDevServer启动服务，通过 HotModuleReplacementPlugin 插件启动代码自动编译页面自动刷新。这样，当你修改了html、js或者样式文件，页面会自动编译刷新。

html页面使用

直接在页面引入 webpack 最终生成的页面脚本和样式文件即可。

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="utf-8">
    <title>webpack test</title>
    <link rel="stylesheet" href="/dist/style.css">
</head>

<body>
    <div id="app"></div>
    <script src="/dist/common.js"></script>
    <script src="/dist/build.js"></script>
</body>
</html>
总结

基于 webpack 的入门教程就到这里，希望本文能对你有所帮助，你也可以参考阮一峰的例子https://github.com/ruanyf/webpack-demos#demo01-entry-file-source 进行深入的了解。
