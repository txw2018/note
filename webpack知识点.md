# 一、webpack知识点

# 二、mode
```
module.exports = {
  mode: 'production'
}
```
| 选项        | 描述                                                         |
| ----------- | ------------------------------------------------------------ |
| development | 会将 process.env.NODE_ENV 的值设为 development。启用 NamedChunksPlugin 和 NamedModulesPlugin。 |
| production  | 会将 process.env.NODE_ENV 的值设为 production。启用 FlagDependencyUsagePlugin, FlagIncludedChunksPlugin, ModuleConcatenationPlugin, NoEmitOnErrorsPlugin, OccurrenceOrderPlugin, SideEffectsFlagPlugin 和 UglifyJsPlugin. |
# 二、常见loader
### css-loader
让webpack能够识别css
```
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [ 'css-loader' ]
      }
    ]
  }
}

```
### style-loader
将css写入style标签并插入head中(下面loader顺序不能换，因为loader加载顺序从右到左)
```
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/,
        use: ['style-loader', 'css-loader' ]
      }
    ]
  }
}
```
### file-loader
用来处理文件
```
module.exports = {
  module: {
    rules: [
      {
        test: /\.(png|jpg|gif)$/,
        use: [
          {
            loader: 'file-loader',
            options: {}
          }
        ]
      }
    ]
  }
}
```
### url-loader
可以将小图片转为base64字符串，减小请求
```
module.exports = {
  module: {
    rules: [
      {
        test: /\.(png|jpg|gif)$/,
        use: [
          {
            loader: 'url-loader',
            options: {
              limit: 10240
            }
          }
        ]
      }
    ]
  }
}
```
### postcss-loader
自动给css属性添加浏览器前缀 
```
npm install --save-dev postcss-loader autoprefixer 

module.exports = {
    module: {
        rules: [
          {
            loader: "postcss-loader",
            options: {
                plugins: [
                    require("autoprefixer") /*在这里添加*/
                ]
            }
          }
       ]
    }
}

```
### babel-loader
将es6+编译成浏览器兼容的 JavaScript 代码
```
npm install -D babel-loader @babel/core @babel/preset-env

module.exports = {
    module: {
      rules: [
        {
          test: /\.js$/,
          exclude: /node_modules/,
          use: {
            loader: 'babel-loader',
            options: {
              presets: ['@babel/preset-env']
            }
          }
        }
      ]
    }
}
```


# 三、常用高效插件
### HtmlWebpackPlugin
该插件将为你生成一个 HTML5 文件， 其中包括使用 script 标签的 body 中的所有 webpack 包。下面的将会在dist下产生一个index.html并引入index.js
```
const HtmlWebpackPlugin = require('html-webpack-plugin');
const path = require('path');

module.exports  = {
  entry: 'index.js',
  output: {
    path: path.resolve(__dirname, './dist'),
    filename: 'index.js'
  },
  plugins: [new HtmlWebpackPlugin()]
};
```
### CleanWebpackPlugin
其作用就是每次打包前先先将输出目录中的内容进行清空，然后再将打包输出的文件输出到输出目录中
```
const {CleanWebpackPlugin} = require("clean-webpack-plugin");

module.exports = {
    plugins: [
        new CleanWebpackPlugin() // 打包前清空输出目录
    ]
}
```
### CopyWebpackPlugin
其作用就是打包的时候copy单个文件或整个目录一起输出到输出目录中。
```
module.exports = {
    plugins: [
        new CopyWebpackPlugin([
            {
                from: "./static", // 将项目根目录下的static文件夹一起拷贝到输出目录中
                to: "" // 属性值为空字符串则表示是输出目录
            }
        ])
    ]
}
```
### BundleAnalyzerPlugin
进行打包文件分析
```
const BundleAnalyzerPlugin = require('webpack-bundle-analyzer').BundleAnalyzerPlugin;
 
module.exports = {
  plugins: [
    new BundleAnalyzerPlugin()
  ]
}
```
# 四、文件指纹
### hash
和整个项目的构建有关，只要项目文件有修改，整个项目构建的hash值就会改变
### chunkhash 
和webpack打包的chunk有关，不同的entry会生成不同的chunkhash值
### contenthash
根据文件内容来定义hash，文件内容不变，则contenthash不变