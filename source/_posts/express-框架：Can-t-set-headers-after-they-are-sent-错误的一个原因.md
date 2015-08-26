title: "express 框架：Can't set headers after they are sent 错误的一个原因"
date: 2015-08-25 10:33:34
tags: express 服务器开发 nodejs
---

今天在写接受github的Webhooks服务时，使用了shelljs库，用来执行github发来的仓库更新信息。
```javascript
var shell = require('shelljs')
var express = require('express')
var router = express.Router()

router.post('/',function(req,res){
    // 打印出github发来的所有信息
    console.log(req)

    res.end('ok')

    shell.cs('../') // 注意这行
    shell.exec('git pull')
	shell.exec('pm2 restart all')
})
```

结果怎么都提示`Can't set headers after they are sent`，通常这个原因都是已经使用过res.send等方法之后，又去设置header或者status导致的，但是我已经结束了请求，然后执行脚本，按说不可能出现这种问题。

使用`postMan`自己模拟请求，发现确实返回`ok`，但是程序依然报错崩溃。

后来发现第`11`行的一个代码写错了，应该是`shell.cd('../')`，导致后面代码直接报错，不过我用`pm2`看日志的时候没有发现`undefiend is not a function`之类的信息，反而直接是`express`报错。

当我修正这个之后，就没有问题了。通常看着`Can't set headers after they are sent`这样的错误，都是直接去找处理请求方面的问题，没想到在之后代码报错也会产生这样的错误。

留下此篇，给看到的人提个醒。
