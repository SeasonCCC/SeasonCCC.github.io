---
title: Pixijs 动画制作教程
date: 2019-01-02 11:45:00
tags: Canvas
---

Pixijs 是一个 HTML5 的 2D 渲染引擎，支持 Canvas 和 Webgl 两种方式渲染。使用 Pixijs 可以制作几乎所有的 2D 动画效果，甚至一些简单的小游戏都可以实现。目前 Github 上，已经有超过 2W 星，是最流行的 2D 渲染引擎之一。

Pixijs 采用代码开发的形式，虽然开发过程较 Egret 和 Coco2d 复杂，但是该开发过程十分灵活，并且能够与其他业务系统进行无缝对接，是很多前端动画开发者的最爱。本场 Chat 就让我们来研究一下，Pixijs 是如何制作动画的。

本场 Chat 包括：

Pixijs 安装和使用简介；
Pixijs 动画元素渲染；
Pixijs 事件处理；
Pixijs 必备的插件。

***

<!-- more -->
本场Chat，我们来研究一下Pixijs这一强大的2D渲染引擎。在进入正题之前，先简单介绍一下Pixijs。单从名字上我们就知道，这是一个js库，跟Jquery、Vue和React等等的这些库一样。而Pixijs最主要做的是：渲染即把图片、SVG、模型等等的东西，生成到页面中。

- [Pixijs安装和使用简介](#Pixijs安装和使用简介)
- [Pixijs动画元素渲染](#Pixijs动画元素渲染)
- [Pixijs事件处理](#Pixijs事件处理)
- [Pixijs必备的插件](#Pixijs必备的插件)

***

## Pixijs安装和使用简介
<span id="Pixijs安装和使用简介"></span>
### Pixijs 安装
由于Pixijs是一个js库，所以安装方法跟一般的js库一样
```
<script src="pixi.min.js"></script>
```
或者通过npm\yarn安装
```
npm install pixi.js
yarn add pixi.js
```
安装之后，在控制台的console中，会出现Pixijs特有的log提示，看到了这个，就表示项目中使用到了Pixijs
```
PixiJS 4.4.5 - * canvas * http://www.pixijs.com/  ♥♥♥
```
### Pixijs 使用
Pixijs使用方法非常简单，最基本的代码，就是
```
let app = new PIXI.Application({width: 256, height: 256});
...
document.body.appendChild(app.view);
```
这里调用PIXI.Application方法，创建一个宽度和高度都为256的场景。PIXI.Application方法调用过后，会返回一个canvas对象，把该对象加入到dom当中，一个简单的PIXI场景就出现了。

当然，PIXI.Application方法还有很多配置，这个可以到文档中查看详细的介绍：[PIXI文档](http://pixijs.download/release/docs/index.html)

***

## Pixijs动画元素渲染
<span id="Pixijs动画元素渲染"></span>
Pixijs既然是一个渲染引擎，那么它在渲染方面肯定是有独特之处，那究竟Pixijs是如何做渲染的呢

首先，Pixijs支持Canvas和Webgl两种方式渲染
* Canvas方式相信我们都不陌生，是Html5的一个新增的一个画布绘图的功能。
* Webgl其实是Opengl在网页浏览器中的运用，专门针对三维视图所出现最新Api。

在Pixijs中，无论是使用Canvas还是Webgl方式，所写的代码都是一样的，只是需要通过PIXI.Application中的forceCanvas来设置是否强制使用Canvas方式即可。


