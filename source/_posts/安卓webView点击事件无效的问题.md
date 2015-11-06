title: 安卓webView点击事件无效的问题
date: 2015-09-02 15:35:10
tags: 安卓 webView 点击无效
---

前阵子应公司这边需求，开发嵌入在`webView`里面的页面，结果测试同学发现页面第一次载入的时候，是无法点击的，只有当页面滑动之后才可以点击。

通过查看`Eclipse`里面的`log`发现，在点击的时候，`webView`报错，页面没有错误，页面的事件都没有触发。

通过在页面载入之后的一瞬间执行
```js
document.body.scrollHeight = 1;
document.body.scrollHeight = 0;
```
就可以了。

小米部分机型存在即使这样做，也有部分点击会报错的问题。