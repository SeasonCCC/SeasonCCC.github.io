---
title: Pixijs 动画制作教程
date: 2019-01-02 11:45:00
tags: Canvas
categories:
- Pixijs
---

本次要介绍的Pixijs 是一个 HTML5 的 2D 渲染引擎，支持 Canvas 和 Webgl 两种方式渲染。使用 Pixijs 可以制作几乎所有的 2D 动画效果，甚至一些简单的小游戏都可以实现。目前在 Github 上，已经有超过 2W star，是最流行的 2D 渲染引擎之一。

Pixijs 采用纯代码开发的形式，虽然开发过程不像 Egret 和 Coco2d 有图形化工具方便，但是该开发过程十分灵活，并且能够与其他业务系统进行无缝对接，是很多前端动画开发者的最爱。本场 Chat 就让我们来研究一下，Pixijs 是如何制作动画的。

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
由于Pixijs是一个js库，所以安装方法跟一般的js库一样：
```
<script src="pixi.min.js"></script>
```
或者通过npm\yarn安装
```
npm install pixi.js
yarn add pixi.js
```
安装之后，在控制台的console中，会出现Pixijs特有的log提示，看到了这个，就表示项目中使用到了Pixijs。
```
PixiJS 4.4.5 - * canvas * http://www.pixijs.com/  ♥♥♥
```
### Pixijs 使用
Pixijs使用方法非常简单，最基本的代码，就是：
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
Pixijs既然是一个渲染引擎，那么它在渲染方面肯定是有独特之处，那究竟Pixijs是如何做渲染的呢。

### **首先，Pixijs支持Canvas和Webgl两种方式渲染**

> * Canvas方式相信我们都不陌生，是Html5的一个新增的一个画布绘图的功能。
> * Webgl其实是Opengl在网页浏览器中的应用，专门针对三维视图所出现最新Api。

在Pixijs中，无论是使用Canvas还是Webgl方式，所写的代码都是一样的，只是需要通过PIXI.Application中的forceCanvas来设置是否强制使用Canvas方式即可。

### **搭建舞台**
一个Pixijs项目，最开始都是根据需求搭建舞台：
```
this.app = new PIXI.Application(this.options.width, this.options.height, {
  view: this.options.stage,
  backgroundColor: 0xffffff
  // forceCanvas: true
})
```
舞台会有很多属性可以设置，比较重要的是宽、高以及舞台依附的dom元素（view）。然后就是一些可选的设置，包括背景色、是否强制使用canvas、不透明度等等，这些可以根据具体的项目需求进行设置。

### **预加载元素**
因为动画和游戏的资源都很多，所以任何一个动画或者游戏框架，都必须有预加载的功能。Pixijs有一个十分强大的加载器：PIXI.loaders。通过loader可以把图片资源预加载到项目中，然后通过onProgress和onComplete方法，就能监听到图片加载情况。
```
this.loader = new PIXI.loaders.Loader()
this.loader.add('stage1', stage1)
this.loader.load()
this.loader.onProgress.add(progressHandel)
this.loader.onComplete.add(handleComplete)
```
利用loader能够制作项目加载进度的动画

### **Pixijs通过Sprite、Graphics、TilingSprite等的api，生成场景中的元素**
日常开发中，最经常用到的是Sprite类，这个用来创建一个精灵：
```
const car = new PIXI.Sprite(textures['car1.png'])
```
创建了之后，可以用对这个精灵进行各种的操作，比较常见的有以下几个：
```
car.setTransform(104, 94) // 设置位置
car.anchor.set(0.5)  // 设置锚点
car.visible = false  // 设置隐藏
car.alpha = 0.5  // 设置不透明度
```
设置好各种属性之后，就可以把精灵加入到场景中（一般会给各个场景新建一个Container，把精灵添加到Container中，再把Container添加到舞台中）：
```
this.stage2 = new PIXI.Container()
this.stage2.addChild(car)
```

Pixijs精灵的属性有很多，需要根据具体的需求来设置精灵的属性，更多时候是需要翻查官网[PIXI文档](http://pixijs.download/release/docs/index.html)。

## Pixijs事件处理
<span id="Pixijs事件处理"></span>
经过渲染之后，舞台中已经渲染了有很多精灵元素，那么下面就来看看，如何制作交互效果。

### **Sprite精灵事件**
精灵中除了有各种属性之外，还提供了大量的事件，基本上涵盖了市面上所有交互需求:
```
this.againBtn.on('pointerdown', e => {
  console.log(e)
})
```
通过on方法，绑定Pixijs中的click、mousedown、mouseup等方法，通过回调的参数e，就可以获取事件中回调的各种参数属性，通过这些参数即可做出各种的效果。这里有几点需要注意：
1. 这些事件中，有一些在手机上才会有效果，例如touch类的方法
2. 需要开启精灵的interactive属性才可以调用成功
3. 事件存在冒泡问题

## Pixijs必备的插件
<span id="Pixijs必备的插件"></span>
Pixijs作为一个渲染引擎，能做的主要的渲染方面的东西，但是要完成一个h5或者游戏，还需要一些辅助的工具才行。这里就介绍一下，用Pixijs开发的时候，必备的几个插件：
+ GSAP动画库
> 动画是h5和游戏中十分重要的一环，Pixijs中对于动画的支持极其有限，而GSAP恰好能够解决这一问题。GSAP是国外流行的动画库，并且有专门为Pixijs量身定制的插件，是Pixijs的好兄弟。

+ Pixi-sound
> Pixijs官网推荐的音频插件，是一个简单的音频播放插件。虽然不及很多大型的音频库功能那么强大，但是一般的h5和小游戏只会播放简单的背景音乐，基本上已经够用了。

+ Pixi-layers
> Pixijs官网推荐的层级插件，作用类似用css中的zindex。由于Pixijs基于Canvas，所以如果元素一多的话，叠层的情况就会经常出现；这个时候，如果还是通过代码的添加顺序来区分层级的话，就会十分地混乱。有了Pixi-layers后，可以通过设置z-index来区分层级，十分方便。