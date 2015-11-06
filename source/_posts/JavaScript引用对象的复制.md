title: "JavaScript引用对象的复制"
date: 2015-03-28 09:06:04
tags: JavaScript 对象 复制
---

# JavaScript引用对象复制

JavaScript有6种数据类型，分别是：
* Boolean 布尔
* String 字符串
* Number 数字
* Null
* Undefined
* 对象

其中，是分为 `基本类型` 和 `引用类型`。
Boolean、String、Number、Null、Undefined都是基本类型。基本类型是按值访问的，意思是说，操作的是保存在变量中的实际值。
<!--more-->
其中，对象是引用类型，引用类型的值是保存在内存中的对象。因为JavaScript不允许直接操作对象的内存空间，所以，在操作对象的时候，实际上是操作对象的引用。

```js
var num = 10;
var another_num = num; // 都是10

another_num++; // anther_num的值是11
console.log(another_num); // 11
console.log(num); // 而num的值还是10
```
引用对象表现的是这样：
```js
var example = [1,2,3,4,5];
var another_example = example;

another_example.push(6); // 向another_example尾部压入一个数字
console.log(another_example); // [1, 2, 3, 4, 5, 6]
console.log(example); // [1, 2, 3, 4, 5, 6] 输出example发现，example也改变了。
```
对于对象的操作也是如此。

因此，新手常犯的一个错误就是，把一个数组或者对象 `复制` 给了另外一个。觉得这样就可以对 `复制` 出来的新对象随便操作了。
实际上，赋值语句传递的实际上是引用。

另外，在`JavaScript`中，函数参数的传递是值的传递。如果传入的是一个对象类型，那么这个参数得到的是这个传入参数的引用地址。举个例子：
```js
var a = {};

function b(c){
    c.d = 1;
    c = {};
    return c;
}

var d = b(a);
console.log(a.d); // 1
console.log(d.d); // undefined
```
在上面的例子中，先将a传递进去，参数c实际上和a指向同一个地址，所以对c操作也会在a上反应出来，但是，如果对c重新赋值，那么c就指向新的内存地址了。

那么复制一个数组通常可以有两个方法。
一个是用ECMAScript5规定给数组的 `concat()`来返回一个复制的数组。
`concat()` 本身是用来将里面的参数加入到数组的尾部。

```js
var a = [1,2,3];
b = a.concat(4,5,6);
console.log(b); // [1, 2, 3, 4, 5, 6]
```

在这里还有一点要说明，在JavaScript中，基本类型是不可以直接 `操作` 的，恩，没有看错。
看代码：
```js
var a;
a = 1;
a = a + 1;
```
上面的代码先是声明变量 `a` ，然后给变量 `a` 赋值为1，最后一个操作实际上是 先计算 `a + 1` 的值，然后生成新的值，再将新的值赋值给 `a`。

回到正题上来。
赋值数组还有一个方法直接的方法。使用 `slice()` 。
`slice` 方法据说是JavaScript中，比较神奇的方法，它可以接受两个参数。
* 第一个参数是从哪里开始。
* 返回从第一个参数到第二个参数之间的项，不包括第二个参数。注意，第二个参数是可选的，如果没有提供第二个参数，那么会返回从第一个参数到数组尾部的所有值。
举个栗子：
```js
var LiZi = [1,2,3,4,5,6];

console.log(LiZi.slice(0,4));   // [1, 2, 3, 4]
console.log(LiZi.slice(1,4));   // [2, 3, 4]
console.log(LiZi.slice(2,4));   // [3, 4]
console.log(LiZi.slice(2,-1));  // [3, 4, 5]
console.log(LiZi.slice(0));     // [1, 2, 3, 4, 5, 6]
```
上面的 `LiZi.slice(2,-1)` 意思是从第三个元素开始一直到结尾，等价于 `LiZi.slice(2)` 。
所以，复制整个数组可以直接 `LiZi.slice(0)` ，它将会返回从开始到结尾的全部值。
在这里需要明白一点，不论是 `concat` 还是 `slice` 都不会直接对数组进行操作。
它们都是返回操作之后的内容。

当然，对于数组的复制，还有别的方法，比如遍历整个数组。
不过，我觉得，用ECMAScript5提供的方法会更快，因为这是JavaScript的实现（浏览器或者Nodejs）来完成的，更直接，效率上更快。

下一篇会介绍JavaScript对象的复制。

欢迎拍砖。
