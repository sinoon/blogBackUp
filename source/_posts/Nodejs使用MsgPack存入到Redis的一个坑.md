title: "Nodejs使用MsgPack存入到Redis的一个坑"
date: 2015-04-01 21:44:06
tags: node redis msgpack messagepack
---

最近在做的项目中，需要用到往Redis中存入一些信息。之前都是用 `JSON.stringify()` 成字符串，再保存到Redis中，同时需要在Redis脚本中需要使用 `cjson.decode()` 命令，变成lua的table，再进行一些操作。
既然这样，那就不如用 `MessagePack` [官网](http://msgpack.org)。
`MessagePack` 简称 `msgpack` ，按照官网上所说，它 `like` Json，但是 `fast` and `small` ，我一看，矮油，这个很好啊，正好我需要多次调用json对象的压缩和解析。

先安装它在node上的包 `msgpack5`
```
npm install msgpack5 --save
```
我在这里加上 `--save` ，这样在下载到本地时候，在当前目录下的 `package.json` 会自动添加依赖，带版本号滴呦，很省心有木有~

在用到 `msgpack` 的js文件中引入即可。
```js
var msgpack = require('msgpack5')();
```
注意，这里有个小坑，就是引入的时候，必须当做函数运行一下，也就是后面加上那对括号。不然使用的时候，是代码不是调用。

普通的使用方式可以和 `JSON.stringify()` && `JSON.parse()` 一样。
像这样：
```js
var a = {
    a : 1,
    b : 2,
    c : c
}

var b = msgpack.encode(a); // 压缩
var c = msgpack.decode(b); // 解压缩

console.log( c === a);
```
注意，使用 `msgpack.encode` 之后返回的是一个 `Buffer` 对象或者是 `bl` 实例，这个 `bl` 全名 `BufferList` 是一个操作 `Buffer` 的利器。所以，其这一点和 `JSON.stringify()` 不同，需要注意。

node连接redis有一个官方的包，安装引入即可：
```
npm install redis --save
```

```js
var redis = require('redis');

var client = redis.createClient(); // 什么都不行默认是 6379端口，127.0.0.1
```
好了，到此为止，准备活动已经好，坑也已经挖好了，下面开始跳坑。代码伺候。

```js
var a = {
    a : 1,
    b : 2,
    c : c
}

var b = msgpack.encode(a); // 压缩
var c = msgpack.decode(b); // 解压缩

client.set('test',b);
var d = client.get('test');

console.log(b);
console.log(c);

console.log( b === d); // false
```
运行这段代码，发现用 `msgpack.encode()` 之后的 `a` ，存入Redis中之后，再取出来就不是它了！你可以运行这段代码试试。

然后我又测试了不存入Redis的情况，发现很正常，就是存入Redis中，就出错。

打印存入之前和取出的 `Buffer` 对象，发现对象前面后面都多了一些不同的东西，只有中间是一样的。

百思不得其解，没有办法，只好先换回JSON方式来处理数据。

直到第二天，无意中看这个连接Redis的包时，看到这么一句话：
> **return_buffers**: defaults to false. If set to true, then all replies will be sent to callbacks as node Buffer objects instead of JavaScript Strings.

和这么一句

> **detect_buffers**: default to false. If set to true, then replies will be sent to callbacks as node Buffer objects if any of the input arguments to the original command were Buffer objects. This option lets you switch between Buffers and Strings on a per-command basis, whereas return_buffers applies to every command on a client.

也就是说，在这个包中，有两个设置项，默认都是 `false` 。如果修改为 `true` 的话，第一个设置（`return_buffers`）所有作为 `callback` 的返回都会是一个 `Buffer` 对象，而不再是 `JavaScript String` 。

也就是说，只要我们在 `redis.createClient({return_buffers:true})` 加上里面个选项或者 `detect_buffers:true` ， 传入和取出的数据都会是 `Buffer` 类型了。

使用 `MsgPack` 压缩或者 类似的 `Buffer` 终于不再有问题了。

尼玛。。。瞬间舒爽了，终于发现问题了。

实际上，不光是所有的 `replise` 都会是一个 `Buffer`，再向Redis里面存储数据的时候，也会先转换成一个 `String` ， 这就相当于直接把用 `msgpack` 压缩后的 `Buffer` 强制转换回 `String` ，而不经过 `msgpack` 自身的算法来解压。

第二个设置比较灵活，它会根据是否是一个 `Buffer` 来选择是否返回一个 `Buffer` 对象。

至此，困扰我两天的问题终于解决了。

恩，很高兴，来来，一起同饮三百杯~~！
