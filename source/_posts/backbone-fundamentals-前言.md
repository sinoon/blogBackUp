title: backbone-fundamentals 前言
date: 2015-08-25 22:29:57
tags: backbone-fundamentals
---

## 前言

很久以前，“富数据web应用”(data-rich web)是一个矛盾的说法。但现在，这种web应用随处可见，并且你需要知道如何构建它们。

传统意义上来讲，web应用把繁重的数据放置在服务器端，服务器再把载入完成的HTML页面推向浏览器。因此，客户端那边的JavaScript被限制住无法提升用户体验。到了现在，这种情况被转变了——客户端应用可以在需要的地方，从服务器上拉去原始数据并在浏览器里面渲染这些数据。

想一下Ajax技术的购物车向里面添加物品的时候而不需要刷新整个页面。起初，jQuery成为处理这种需求的范例。它的原理就是发起Ajax请求然后更新页面里的文本以及其他东西。然而，这种jQuery所依赖的模式使得我们要把数据模型隐式的放在客户端。随着服务器不再是唯一知道我们物品数量的地方，这就暗示这种演变自然而然的会有一些波折。（不太会翻译，原文是：it was a hint that there was a natural tension and pull of this evolution.）

然而，随着与服务器交互的无规则的代码的增长，代码变得臃肿并且越来越复杂。好的架构在客户端上变得不可或缺 —— 你无法仅凭一些厉害的jQuery代码保持你的应用的健壮性。更有可能的是，你最终会在业务逻辑中陷入噩梦般的UI callbacks，最终也逃避不了被下一个继承你代码的人丢弃的命运。

谢天谢地的是，有很多并且还将有更多Javascript库可以帮助你提升代码的架构和可维护性，使得人们可以非常容易的构建健壮的界面而不需要付出太多的精力。[Backbone.js](http://documentclud.github.com/backbone/) 迅速成为解决这类问题的最流行开源框架之一，这本书将会带领你来进行一个深入的实践。

在刚开始学习基本知识的时候，通过一些练习，去学习如何在构建应用的时候，同时保持其组织性以及可维护性。如果你是希望自己写的代码拥有更好的可读性、架构、可扩展性，这份指南可以帮上你。

提升开发者的水平对我来说很重要，这也是为什么我给这本书使用 Creative Commons Atribution-NonCommercial-ShareAlike 3.0 Unported [license](http://creativecommons.org/licenses/by-nc-sa/3.0/).欢迎大家来修正存在的问题，并且我很希望我们可以保持社区一直都有最新的源码。

我还要感谢[Jeremy Ashkenas](https://github.com/jashkenas) 和 [DocumentCloud](http://www.documentcloud.org) 创建了 Backbone.js ，以及[社区成员](https://github.com/addyosmani/backbone-fundamentals/contributors)的贡献使得这个项目的发展完全超出了我的想象。
<!--more-->
## 目标读者 

这本书的目标读者是那些希望学习使他们前端代码拥有更好架构的初级到中级开发者。在这本教程里，JavaScript基础是需要的，不过，我会尽可能的提供一些关于这些概念的基本注释。

## 致谢

我非常感谢那些技术审核同仁们帮助校验和修正改进这本书。他们的知识，精力和热情使得这本书迅速成为一个很好的学习资料，并且，他们将继续致力于此。感谢他们：

* [Marc Friedman](https://github.com/dcmaf)
* [Derick Bailey](https://github.com/derickbailey)
* [Jeremy Ashkenas](https://github.com/jashkenas)
* [Samuel Clay](https://github.com/samuelclay)
* [Mat Scales](http://github.com/wibblymat)
* [Alex Graul](https://github.com/alexgraul)
* [Dusan Gledovic](https://github.com/g6scheme)
* [Sindre Sorhus](https://github.com/sindresorhus)

同时，我也还要感谢我挚爱的家人，感谢他们在我写作此书期间的耐心陪伴与支持。同样的，也要感谢我杰出的编辑 Mary Treseler。

## 相关人员

没有他们这些其他开发人员和作者在社区贡献的时间和心血，这本书将无法完成。我要向他们表示感谢：

* Derick and Marc (once again)
* [Ryan Eastridge](https://github.com/eastridge)
* [Jack Franklin](https://github.com/jackfranklin)
* [David Amend](https://github.com/raDiesle)
* [Mike Ball](https://github.com/mdb)
* Will Sulzer
* [Uģis Ozols](https://github.com/ugisozols)
* [Björn Ekengren](https://github.com/Ekengren)

同时还有他们这些写杰出的[贡献者](https://github.com/addyosmani/backbone-fundamentals/graphs/contributors)使得这个项目变得可能。

## 目标版本

本书开发 Backbone.js 应用对应版本是 Backbone.js 1.1.x (Underscore 1.6.x)，并且将会主动的去更新到最新版本包括最近的一些改动。如果可能的话，当你发现书中的例子在使用更新的一个版本时候出现问题，请查阅官方[升级指南](http://backbonejs.org/#upgrading)，它会指导如何在版本变化的情况下工作。当然，StackOverflow 同样包含很多优秀的例子，那些例子是其他用户在处理版本升级时候的代码。

## 阅读

我会假设你对于JavaScript有基本的掌握，同时，一些基本的概念将会被跳过，比如对象字面量。如果你需要对于这门语言需要更多的学习，我很乐意为你推荐：

* [Eloquent JavaScript](http://eloquentjavascript.net/)
* [JavaScript: The Definitive Guide](http://shop.oreilly.com/product/9780596805531.do) by David Flanagan (O’Reilly)
* [Effective JavaScript](http://www.informit.com/store/effective-javascript-68-specific-ways-to-harness-the-9780321812186) by David Herman (Pearson)
* [JavaScript: The Good Parts](http://shop.oreilly.com/product/9780596517748.do) by Douglas Crockford (O’Reilly)
* [Object-Oriented JavaScript](http://www.amazon.com/Object-Oriented-Javascript-Stoyan-Stefanov/dp/1847194141) by Stoyan Stefanov (Packt Publishing)

