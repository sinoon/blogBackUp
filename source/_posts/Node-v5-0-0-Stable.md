title: Node v5.0.0(Stable)
date: 2015-11-06 12:35:38
tags:
---
对Node.js v5说你好！我们确实在最近发布了Node.js v4.0.0，然而，这次新版本的发布并不意味着v4的远去。事实上，v4将会比v5存活更长时间。

根据我们最新的LTS(Long-term Support，长期维护版本)计划，Node.js v4 阿尔贡(Argon)将会持续支持30个月，在2018年5月停止维护。然而，此次发布的node.js版本，将仅仅只会支持8个月，与此同时，新的主版本v6，将会在2016年8月发布。Node.js v6将会像v4的时间轴一样，最终会进入新的LTS版本(长期支持版本)。这样一来，我们将会每隔6月有一个~稳定~版本，并且，第二个版本将会进入长期支持版本。如果你刚刚知道LTS，那么这里将会有关于LTS是如何工作的，以此来确保你可以对选择Node.js版本做出正确的决定。

对于选择Node.js版本的一般规则如下：
+ 如果你需要稳定并且处于复杂的生产环境，那么升级或停留在Node.js v4.2.x版本。例如你是一个中型或大型企业。
+ 如果你有能力在不会影响你的环境的情况下，快速、轻松的升级到最近版本，并且，愿意在最新特性出现的时候去尝试它，那么可以升级到Node.js v5。

下面的发布说明是确保可以跳跃到v5版本的主要不兼容变化。请注意，因为此次Node.js新版本采用新版本的V8引擎，你所有的基于本地的插件都需要重新编译，不然你将会收到运行错误当你尝试去加载它们的时候。使用`npm rebuild`，或者简单的删除`node_modules`文件夹再重新获取。

###主要改动
+ buffer:(不兼容)从`Buffer`对象中移除`raw`和`raws`编码格式，它们已经被遗弃了很长时间[#2859][1]。
+ console:(不兼容)`console.time()`的返回值现在会精确到小数点后三位。[#3166][2]。
+ fs:
    - `fs.readFile*()`,`fs.writeFile*()`和`fs.appendFile*()`现在也可以接受一个文件描述符作为它们的第一个参数[#3163][3]
    - (不兼容)在`fs.readFile()`中，如果一个编码格式被指定了，内部方法`toString()`在错误时不再会`抛出(thrown)`错误，但是将会通过回调(callback)表示。[3485][4]
    - (不兼容)在`fs.read()`中，如果内部方法`toString()`失败报错时，同样不会抛出错误，而是通过回调来表示。像这样`fs.read(fd,length,position,encoding,callback)`
+ http:
    - 修正http请求的流中会阻塞的问题。[3342][5]
    - (不兼容)在解析HTTP时，将不再复制一下头信息：`Retry-After`,`ETag`,`Last-Modified`,`Server`,`Age`,`Expire`。


  [1]: https://github.com/nodejs/node/pull/2859
  [2]: https://github.com/nodejs/node/pull/3166
  [3]: https://github.com/nodejs/node/pull/3163
  [4]: https://github.com/nodejs/node/pull/3485
  [5]: https://github.com/nodejs/node/pull/3342
