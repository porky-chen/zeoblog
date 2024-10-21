---
title: Development快速切换线上资源
author: zeo
date: 2023-12-21 19:46:00 +0800
categories: [小工具, 提效, 快速切换资源]
tags: [提效, webpack, NodeJS]
render_with_liquid: false
---

# 开发环境快速切换线上资源Vue+webpack

## 一、**tips**：

你们的**后端资源**是否区分（**dev**）开发环境、（**test**）测试环境、（**grayscale**）预发布环境、（**prod**）生产环境呢，这是一个我们在开发协作过程中很常见的。那么你们是否有过，在test环境下开发着，此时有一个紧急任务是别的环境出现了问题或线上Bug，此时没办法要重新跑其他环境的资源呢，有因为项目依赖性过多，导致需要等待！**如果你有这个苦恼，那么我的小工具可能可以帮到你**。

## 二、原理：

利用了NodeJS的能力，我们启用了本地快速切换资源，避免了依赖包重新去输出的时间等待。



## 三、在build文件下需要新建几个文件：

### 1、proxy-change-middleware.js

````javascript
const getTable = require('./proxy-table.js')  // 这是存放你们环境资源代理的文件
const path = require('path') // node自带
const fs = require('fs')  // node自带

module.exports = (app, instance, compiler) => {
	// 创建跳板文件
	fs.writeFileSync('./build/proxy-flag-file.js', '')
	app.use('/changeProxy', (req, res, next) => {
    const proxyTable = getTable(req.query.target)
    if (!proxyTable[0].target) {
      res.json({
        status: 'error',
        message: '找不到对应的target环境'
      })
      res.end()
      return
    }
    for (let i = 0; i < app._router.stack.length; i++) {
      if (app._router.stack[i].name === 'handle') {
        app._router.stack.splice(i, 2)
        i--
      }
    }
    instance.options.proxy = proxyTable
    instance.setupProxyFeature()
    process.env.PROXY_TARGET = proxyTable[0].target
    trigger()
    res.json({
      status: 'success',
      message: `已切换到${req.query.target}环境`
    })
    res.end()
  })
}
let data = ''
const trigger = () => {
  data = data ? '' : '1'
  const file = path.resolve(__dirname, './proxy-flag-file.js')
  fs.writeFileSync(file, data)
}
````



### 2、proxy-flag-file.js

空白文件



### 3、proxy-table.js

代理资源集合文件

````javascript
// 需要代理的实际域名环境资源
const API_MAP = {
  '/micro': {
    dev: 'https://dev_xxxx',
    test: 'https://test_xxxx',
    grayscale: 'https://grayscale_xxxx',
    prod: 'https://xxxxx'
  }
}

module.exports = (proxy) => {
  return [
    {
      context: ['/micro'],
      target: API_MAP['/micro'][proxy],
      changeOrigin: true,
      secure: false
    }
  ]
}
````



### 4、该需要在哪里使用呢？在你们写打包输出config配置文件里面

webpack.dev.config.js

````javascript
const proxyChangeMiddleware = require('./proxy-change-middleware') // 引入我们的文件

// 然后找到你们的devServer对象

devServer: {
  //...
  before: proxyChangeMiddleware  //引入使用
}
````



## 四、我们大功告成啦！



### 1、接下来如何使用呢！
![change-proxy](/assets/img/posts/changeProxy.png)

### 2、本地运行的IP/changeProxy?target={env}



## 五、成果：

如果没有切换成功，看看是否资源正确使用到了哦！

是否可以帮助到你提高开发效率呢！

领导都要夸你高效呢！