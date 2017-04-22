# 移动App开发第2天

## 复习
常见App开发方式的对比

1. Hbuilder :在线的
2. React-Native 和 Ewwx ：本地打包

## Webpack

## 网站中常见静态资源有哪些
1. 图片
 + jpg,png,gif,bmp

2. 字体
 + woff,woff2,eot,svg,ttf

3. JS
 + js,coffee

4. css
 + css,less,sass

5. 模板文件
 + .vue, .jade

## 大量静态资源文件引发的问题
1. 发起大量的二次请求
2. 需要维护组件之间的依赖关系

## 以上问题的解决方案
1. 发起大量的二次请求
 + 解决方案：
 + 图片：精灵图，压缩图片体积，base64编码（能把图转换为字符串）
 + JS，CSS：代码压缩，代码合并

2. 需要维护组件之间的依赖关系：之前是程序员自己维护这种依赖关系的；【使用一些工具帮我们解决这种依赖关系】

## webpack解决上述的问题
webpack的作用：合并文件，压缩文件，处理文件的依赖关系

## 列表各行变色的案例

### 安装webpack
1. 全局安装：运行`npm i webpack -g`
2. 项目安装：`npm i webpack --save-dev`
3. `--save和--save-dev的区别`：`--save-dev`的全称：'save develop'
 + 拿jquery和webpack进行比较

## 需求：
1. 借助于Jquery
2. 奇数行一个颜色
3. 偶数行一个颜色

1. 使用`npm init`初始化项目，使用npm来管理项目中的包
2. 新建HTML结构和main.js的逻辑代码
3. 安装webpack`cnpm i webpack --save-dev`
4. 运行`webpack src/js/main.js dist/bundle.js`进行打包构建，语法是：`webpack  入口文件   输出文件`

## webpack构建的过程
1. 判断用户是否传递了入口和出口两个路径，如果没有传递，那么就从项目根目录中查找`webpack.config.js`这个配置文件
2. 如果找到了这个配置文件，则根据配置文件中导出的配置对象，来进行打包构建！
3. **注意：在配置文件中不能使用ES6的语法！！！**
```
	// 1. 引入Path模块处理路径问题
    var path = require('path');

    module.exports = {
        entry: path.resolve(__dirname, 'src/js/main.js'), // 配置打包的入口文件
        output:{ // 指定输出文件
            path:path.resolve(__dirname, 'dist'), // 输出文件的路径
            filename:'bundle.js' // 输出文件的名称
        }
    }
```

## 使用webpack-dev-server来实现监视代码实时打包，自动刷新浏览器
1. 运行`cnpm i webpack-dev-server --save-dev`安装包
2. 由于命令行不是别`webpck-dev-server`这个命令，所以需要在`package.json`中配置一下命令
3. `package.json`中，script节点下的命令，可以通过`npm run 命令名称`的形式去运行
4. **注意:webpack-dev-server打包好的bundle.js文件，并没有存放到实际的硬盘上，而是存储到了内存中！！！**放到内存中最主要的原因是快，非常快，太TM快了！
5. 如何从内存中获取打包好的bundle.js文件呢？？？
6. 修改`webpack-dev-server`托管的根目录,通过`--contentBase`指定托管的根目录：
```
"dev": "webpack-dev-server --contentBase src"
```
7. 自动打开浏览器和配置端口号, `--open`自动打开浏览器， `--port 4321`指定端口号：
```
"dev": "webpack-dev-server --contentBase src --open --port 4321"
```
8. 在配置文件中设置自动打开浏览器：
```
devServer:{ // 这里的配置项会交给webpack-dev-server去读取
        //--contentBase src --open --port 4321
        contentBase:path.resolve(__dirname, 'src'), // 配置启动路径
        open:true, // 自动打开浏览器
        port:4321 // 指定端口号
    }
```

## 在内存中生成HTML页面
自动在内存中根据指定的模板，生成index页面，同时自动注入打包好的bundle.js文件
1. 运行`cnpm i html-webpack-plugin --save-dev`
2. 导入模块：
```
// 2. 引用自动生成HTML页面的模块
var htmlWebpackPlugin = require('html-webpack-plugin');
```
3. 在配置文件中添加`plugins`节点如下：
```
plugins:[ // 插件数组
        new htmlWebpackPlugin({ // 创建一个htmlWebpackPlugin插件
            template:path.resolve(__dirname, 'src/index.html'), // 指定模板页面
            filename:'index.html' // 指定在内存中生成的页面的名称
        })
    ]
```

## 使用Webpack打包CSS文件
1. 运行`cnpm i style-loader css-loader --save-dev`
2. 在配置文件中添加`module`节点如下：
```
module:{ // 配置相关的loader模块
        rules:[ // 配置相关文件的匹配规则
            {test:/\.css$/,  use:['style-loader', 'css-loader']}, // 处理CSS文件的loader配置
        ]
    }
```

## 使用webpack打包sass文件
1. 运行`cnpm i sass-loader node-sass --save-dev`,sass-loader依赖于node-sass
2. 添加rules规则如下：
```
{ test: /\.scss$/, use: ['style-loader', 'css-loader', 'sass-loader'] }// 处理sass文件的loader配置
```

## 实现热更新
1. 修改`package.json`中的命令，添加`--hot`指令即可：
```
"dev": "webpack-dev-server --hot"
```
2. 第二种实现热更新的方式：
 + 第一种方式：修改配置文件的devServer节点：
 ```
 devServer: { // 这里的配置项会交给webpack-dev-server去读取
        //--contentBase src --open --port 4321
        contentBase: path.resolve(__dirname, 'src'), // 配置启动路径
        open: true, // 自动打开浏览器
        port: 4321, // 指定端口号
        hot:true // 启用热更新
    },
 ```
 + 修改plugins节点：
 ```
 plugins: [ // 插件数组
        new htmlWebpackPlugin({ // 创建一个htmlWebpackPlugin插件
            template: path.resolve(__dirname, 'src/index.html'), // 指定模板页面
            filename: 'index.html' // 指定在内存中生成的页面的名称
        }),
        new webpack.HotModuleReplacementPlugin() // 使用webpack下面的.HotModuleReplacementPlugin()实现热更新
    ]
 ```

## 实现处理图片路径
1. 运行`cnpm i url-loader file-loader --save-dev`
2. 添加loader配置项：
```
{ test: /\.(jpg|png|gif|bmp|jpeg)$/, use: 'url-loader' } // 处理URL路径的loader
```
3. url-loader默认能帮我们把图片转为base64编码；
4. 默认URL中的图片路径是经过哈希求值的，目的是为了防止两张一模一样的图片出现；
5. `limit`参数的作用：给定一个图片的大小，当图片超过给定值的时候：就证明你是一张大图片，不用进行base64编码；否则：是小图片，需要进行base64编码；
6. 计算机中存储的单位：Bit，Byte，KB，MB，GB，TB
7. `limit=1000`中单位是Byte，只要当图片小于给定值的时候，才会进行base64的编码！

## ES6中的class关键字
1. 静态属性和实例属性的区别：
 + 静态属性：直接通过类名就可以访问到，不需要创建类的实例；
 + 实例属性：必须先通过new关键字，创建一个类的实例，然后，在这个实例上通过点号访问的属性，叫做实例属性；
 + 由于webpack不识别静态属性static这个关键字，所以，需要借助于`babel-loader`来处理这个高级ES6语法！

## 在webpack中配置babel-loader
1. 运行`npm i cnpm -g`
2. 运行`cnpm i babel-core babel-loader babel-plugin-transform-runtime --save-dev`
3. 运行`cnpm i babel-preset-es2015 babel-preset-stage-0 --save-dev`
4. 添加相关loader模块：
```
{test:/\.js$/, use:'babel-loader', exclude:/node_modules/} // 最重要的一点：要把node_modules文件夹，添加到排除项，通过exclude排除这个文件夹【注意：一定要排除否则会报错！！！】
```
5. 在项目根目录中新建`.babelrc`配置文件，将来babel-loader运行的时候，会检查这个配置文件，并读取相关的语法和插件配置：
```
{
    "presets":["es2015", "stage-0"],
    "plugins":["transform-runtime"]
}
```

## Webpack发布策略
1. 开发期间的配置：实时刷新浏览器，实时编译，热更新，index.html，bundle.js;
2. 在开发期间：测试文件、测试数据、测试相关的工具（好处：方便快速开发调试）
3. 部署期间：剔除（测试文件、测试数据、测试相关的工具）这些文件，目的：减少部署文件的体积；方便用户安装；
4. 在部署时候，我们也需要一个webpack的配置文件，只是这个配置文件中，删除了开发时候的一些工具；
5. 在项目中新建`webpack.publish.config.js`
    ```
	// 删除devServer选项：
    devServer: { // 这里的配置项会交给webpack-dev-server去读取
        //--contentBase src --open --port 4321
        contentBase: path.resolve(__dirname, 'src'), // 配置启动路径
        open: true, // 自动打开浏览器
        port: 4321, // 指定端口号
        hot: true // 启用热更新
    },
    ```
    ```
    // 删除热更新
        new webpack.HotModuleReplacementPlugin() // 使用webpack下面的.HotModuleReplacementPlugin()实现热更新
    ```
    ```
    // 改造URL-loader
    { test: /\.(jpg|png|gif|bmp|jpeg)$/, use: 'url-loader?limit=43959&name=images/imgs-[hash:7].[ext]' }, // 处理URL路径的loader
    ```

## 每次重新发布的时候，自动删除`dist`目录
1. 运行`cnpm i clean-webpack-plugin`
2. 在部署的配置文件中导入这个插件：
```
var cleanWebpackPlugin = require('clean-webpack-plugin');
```
3. 在plugins节点下，创建这个么一个插件,把需要删除的目录通过数组传递进去：
```
new cleanWebpackPlugin(['dist']) // 创建一个删除文件夹的插件，把dist目录传递进去
```

## 分离第三方包
目的：将公共的第三方包，抽离为一个单独的包文件，这样防止重复打包！
1. 改造entry：
```
entry: {
        app:path.resolve(__dirname, 'src/js/main.js'), // 自己的代码入口文件
        vendors:['jquery'] // 第三方包的入口
    }, // 配置打包的入口文件
```
2. 改造plugins:
```
new webpack.optimize.CommonsChunkPlugin({
            name:'vendors', // 指定抽取公共模块的名称
            filename:'vendors.js' // 指定抽取出来的文件真实名称
        })
```

## 压缩JS代码
在Plugins下面添加：
```
new webpack.optimize.UglifyJsPlugin({ // 优化压缩JS
    compress:{
        warnings:false // 移除警告
    }
}),
new webpack.DefinePlugin({ // 设置为产品上线环境，进一步压缩JS代码
    'process.env':{
        'NODE_ENV':JSON.stringify('production')
    }
})
```

## 抽取CSS文件到单独的样式表
1. 运行`cnpm install --save-dev extract-text-webpack-plugin`
2. 引入插件：
```
var ExtractTextPlugin = require("extract-text-webpack-plugin");
```
3. 修改sass和css的loader如下：
```
{
        test: /\.css$/, use: ExtractTextPlugin.extract({// 使用插件来处理CSS样式
            fallback: "style-loader",
            use: "css-loader"
        })
    }, // 处理CSS文件的loader配置
    {
        test: /\.scss$/, use: ExtractTextPlugin.extract({
            fallback: 'style-loader',
            use: ['css-loader', 'sass-loader']
        })
    },// 处理sass文件的loader配置
```
4. 在plugins下面添加新插件：
```
new ExtractTextPlugin("styles.css") // 抽离CSS样式名称
```

## 压缩抽取出来的CSS 样式表
1. 运行`cnpm i optimize-css-assets-webpack-plugin --save-dev`安装插件
2. 导入插件：
```
var OptimizeCssAssetsPlugin = require('optimize-css-assets-webpack-plugin');
```
3. 在plugins节点下new上面导入的插件即可：
```
new OptimizeCssAssetsPlugin() // 创建一个压缩CSS文件的插件
```

## 压缩HTML页面
1. 修改plugins节点下面的htmlWebpackPlugin这个插件，添加`minify`属性;
```
new htmlWebpackPlugin({ // 创建一个htmlWebpackPlugin插件
        template: path.resolve(__dirname, 'src/index.html'), // 指定模板页面
        filename: 'index.html', // 指定在内存中生成的页面的名称
        minify:{ // 压缩优化HTML页面
            collapseWhitespace:true, // 合并空白字符
            removeComments:true, // 移除注释
            removeAttributeQuotes:true // 移除属性上的引号
        }
    })
```
2. 详细的配置可以参考[html-minifier](https://github.com/kangax/html-minifier#options-quick-reference)