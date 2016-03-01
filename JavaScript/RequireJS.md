#RequireJS

##开发环境


##生产环境

###代码压缩

####js 压缩

#####压缩整个项目

假设项目中所有的`js`文件都在 `js` 目录

![Alt text](./1441706668019.png)


项目中有`css，app，js` 三个目录，分别对应`css，app` 和 `js`文件。

>下载的 [r.js](http://requirejs.org/docs/download.html#rjs) 与`r_js`平行放置 或 `npm install –g requirejs`。

`js-build`目录放置压缩后的`js`文件，结构同 `js`，但会多生成一个`build.txt`文件。

>build.js文件是需要我们自己配置的，r.js 会根据它去找被压缩的目录和压缩后的放置目录

```js
({
    appDir: "js",
    baseUrl: "js",
    dir: "js-build"
})
```

可以看到，`js-build` 目前是空的。好了，开始压缩了。

1，cmd 进入命令行控制台，我的项目在 	`d:/r_js`
2，执行命令：
- 如直接下载的`r.js` ，应将`r.js`放在当前项目平行目录下，运行命令 	`node r.js -o js-build.js` 
- 如 `npm` 加载的 `r.js` 直接运行 `r.js -o js-build.js`

>执行命令部分效果如下，文件已经压缩

![Alt text](./1441707501577.png)  ![Alt text](./1441707355432.png)
