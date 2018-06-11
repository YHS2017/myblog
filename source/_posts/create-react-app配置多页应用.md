---
title: create-react-app配置多页应用
date: 2018-06-09 17:32:33
tags: 前端
category: JS
cover_picture: https://files-1255997619.cos.ap-shanghai.myqcloud.com/cover.jpg
---
最近有个项目使用react框架搭建的网站和管理后台，本人想要将其做成两个单独的页面单个项目，于是就研究了一下多页面的配置，其实主要是webpack的配置修改，为了更清楚，我就直接从新建项目开始。

>1.使用create-react-app 创建一个单页应用，并且创建成功之后运行 npm run eject 暴露配置
>2.修改paths中的所需文件的路径  

```js
module.exports = {
  dotenv: resolveApp('.env'),
  appBuild: resolveApp('build'),
  appPublic: resolveApp('public'),
  appHtml: resolveApp('public/index.html'),
  //-------此处有多少个页面就新建多少入口文件的路径-------
  app1IndexJs: resolveApp('src/App1/index.js'),
  app2IndexJs: resolveApp('src/App2/index.js'),
  //---------------------------------------------------
  appPackageJson: resolveApp('package.json'),
  appSrc: resolveApp('src'),
  yarnLockFile: resolveApp('yarn.lock'),
  testsSetup: resolveApp('src/setupTests.js'),
  appNodeModules: resolveApp('node_modules'),
  publicUrl: getPublicUrl(resolveApp('package.json')),
  servedPath: getServedPath(resolveApp('package.json')),
};
```

>3.在config中修改webpack.config.dev.js文件  

+ 修改entry
```js
//修改入口文件你需要几个页面就写几个，
entry: {
  App1: [
    require.resolve('./polyfills'),
    require.resolve('react-dev-utils/webpackHotDevClient'),
    paths.app1IndexJs,//此处文件路径需要在paths中修改，或者直接在此处进行字符串拼接，本人强迫症所以是在paths中进行修改的~
  ],
  App2: [
    require.resolve('./polyfills'),
    require.resolve('react-dev-utils/webpackHotDevClient'),
    paths.app2IndexJs,//同上
  ],
}
```
+ 修改output
```js
//需要修改出口文件的名称，不然会有现文件明冲突，网上看别人没有改这个，但是本人遇到了问题所以就改了，可能人品不行吧~
filename: 'static/js/[name].bundle.js',
```
+ 修改plugins中的HtmlWebpackPlugin
```js
//多少个页面就new 多少个 HtmlWebpackPlugin 并且在每一个里面的chunks都需要和上面的entry中的key匹配，例如上面entry中有App1和App2这两个。这里的chunks也需要是App1和App2
new HtmlWebpackPlugin({
  inject: true,
  chunks: ["App1"],
  template: paths.appHtml,//此处模板文件路径需要在paths中修改，或者直接在此处进行字符串拼接
  filename: 'App1.html',//打包生成的文件名，注意不要有重复的！！！！
}),
new HtmlWebpackPlugin({
  inject: true,
  chunks: ["App2"],
  template: paths.appHtml,//此处可以与其他页面使用同一模板文件，当然如果想用不同的也可以在public里面新建一个模板html文件，看个人需求
  filename: 'App2.html',//打包生成的文件名，注意不要有重复的！！！！
}),
```
>4.修改scripts中的start.js文件  

```js
//由于添加了新的货修改了入口文件的路径所以需要在此处修改相应的路径，不然文件检查会不通过
if (!checkRequiredFiles([paths.appHtml, paths.app1IndexJs, paths.app2IndexJs])) {
  process.exit(1);
}
```

到此开发环境的配置就修改完成了，不出意外的话npm run start 就可以打开页面了，不同的路径可以通过连接进行访问，例如我的App1就访问http://localhost:3000/App1.html ，App2就访问http://localhost:3000/App2.html 。接下来是生产环境的配置修改。  

>5.修改config下的webpack.config.prod.js文件  

+ 修改entry
```js
//与开发环境修改一样，入口文件你需要几个页面就写几个，
entry: {
  App1: [
    require.resolve('./polyfills'),
    paths.app1IndexJs,//同开发环境
  ],
  App2: [
    require.resolve('./polyfills'),
    paths.app2IndexJs,//同开发环境
  ],
}
```
+ 修改plugins中的HtmlWebpackPlugin
```js
//多少个页面就new 多少个 HtmlWebpackPlugin 并且在每一个里面的chunks都需要和上面的entry中的key匹配，例如上面entry中有App1和App2这两个。这里的chunks也需要是App1和App2
new HtmlWebpackPlugin({
  inject: true,
  chunks: ["App1"],
  template: paths.app1Html,
  filename: 'App1.html',
  minify: {
    removeComments: true,
    collapseWhitespace: true,
    removeRedundantAttributes: true,
    useShortDoctype: true,
    removeEmptyAttributes: true,
    removeStyleLinkTypeAttributes: true,
    keepClosingSlash: true,
    minifyJS: true,
    minifyCSS: true,
    minifyURLs: true,
  },
}),
new HtmlWebpackPlugin({
  inject: true,
  chunks: ["App2"],
  template: paths.app2Html,
  filename: 'App2.html',
  minify: {
    removeComments: true,
    collapseWhitespace: true,
    removeRedundantAttributes: true,
    useShortDoctype: true,
    removeEmptyAttributes: true,
    removeStyleLinkTypeAttributes: true,
    keepClosingSlash: true,
    minifyJS: true,
    minifyCSS: true,
    minifyURLs: true,
  },
}),
```
>6.修改scripts中的build.js文件  

```js
//同开发环境也需要在此处修改相应的路径，不然文件检查会不通过
if (!checkRequiredFiles([paths.appHtml, paths.app1IndexJs, paths.app2IndexJs])) {
  process.exit(1);
}
```

到此生产环境的配置就修改完成了，用npm run build命令就可以在build文件夹看到生成的多个页面了~
至于还有一些网上说的需要修改webpackDevServer.config.js中的historyApiFallback来达到可以访问不同页面的目的，貌似本人并没有遇到无法访问的问题~本人能力有限~附上GitHub的项目案例（https://github.com/YHS2017/ReactMultiPage.git ）如有问题希望大家提出来一起讨论讨论~
