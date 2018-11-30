---
title: CreateJS 制作 H5 长图动画
date: 2018-11-30 17:27:43
tags: Canvas
---

现在移动端高端大气的动画h5非常多，去各大素材网站，或者关注一些大型的公众号，不时就会推出一些新颖的形式。这些移动端的h5除了设计非常地漂亮以外，动画效果也特别棒。我们作为前端程序猿是很有必要去学习怎么样去制作这类型的动画效果。

首先要声明一点的是，前端制作稍微复杂的动画和游戏，一般不会使用dom来写的。主要原因有两个

- dom制作动画性能不高
- dom写复杂动画比较吃力

一般这种动画和游戏，我们会使用canvas来制作，这里介绍一下用createjs这一个动画库来制作一个简单的长图动画，完成的效果：
https://www.foshannews.com/zt/2018/wx/jdtp/zc1/#/

- [搭建加载场景](#搭建加载场景)
- [搭建长图场景](#搭建长图场景)
- [制作长按效果](#制作长按效果)
- [添加元素以及元素动画](#添加元素以及元素动画)


## Createjs搭建加载场景
<span id="搭建加载场景"></span>
在搭建场景之前，很重要的一步就是要加载资源，createjs有一个专门用于加载资源的库preloadjs。使用这个库可以在场景渲染之前，先加载需要的资源，实现预加载；再配合上一些加载的动画，用户体验会非常好。


首先把图片引用import进来，然后新建一个createjs.LoadQueue，调用on和loadManifest两个方法。在complete里面写上加载完成后需要执行的逻辑即可。

```
import XXX from '../assets/loading-pointer.png'

let queue = new createjs.LoadQueue(false)
queue.on('complete', () => {
    ...
}, this)

queue.loadManifest([
  {
    id: 'loadingPointer',
    src: loadingPointer
  }, {
    id: 'loadingProgress',
    src: loadingProgress
  }
])
```

**这里要注意的是，loadManifest这个方法需要在complete后；原因跟canvas的img.onload和img.src一样，为了在浏览器有缓存的情况下，不会出现img.src方法已经调用完成，而onload方法还没有绑定。**

---

#### 制作加载场景

有了加载资源这个方法之后，我们制作场景的思路是：
出现加载场景->加载资源->完成一个资源的加载->加载场景进度条往前移动


先创建一个container，相当于一个div，来包裹里面的元素，方便后面操作里面的元素

```
let stage = new createjs.Stage(options.stage)
let container = new createjs.Container()
```

接下来，通过Shape的graphics方法，绘制一个背景

```
let bg = new createjs.Shape()
bg.graphics.beginFill('#b7d45d').rect(0, 0, this.options.width, this.options.height)
container.addChild(bg)
```

有了背景之后，可以开始往上加各种的元素了，先把文字内容加上，定位的话，文字放在在页面中间，可以定义一个中点的mx和my，后面很多地方会用到
```
let progressWord = new createjs.Text('政策说明书打开中……', '30px Microsoft Yahei', '#637d12')
progressWord.textAlign = 'center'
progressWord.textBaseline = 'bottom'
progressWord.x = mx
progressWord.y = my * 1.03
container.addChild(progressWord)
```

加了加载页面的文字之后，就到添加页面的进度条了；由于进度条是图片，所以在添加图片要先用一个预加载，预加载过后，把图片添加到container上面
```
let queue = new createjs.LoadQueue(false)
queue.on('complete', () => {
  const loadingProgressImg = queue.getResult('loadingProgress')
  const loadingProgressBitmap = new createjs.Bitmap(loadingProgressImg)
  const bounds = loadingProgressBitmap.getBounds()
  loadingProgressBitmap.setTransform(mx - bounds.width * 0.4, my * 0.8, 0.8, 0.8)
  container.addChild(loadingProgressBitmap)

  // loading pointer
  const loadingPointerImg = queue.getResult('loadingPointer')
  const loadingPointerBitmap = new createjs.Bitmap(loadingPointerImg)
  loadingPointerBitmap.setTransform(mx - bounds.width * 0.4, my * 0.8, 0.8, 0.8)
  container.addChild(loadingPointerBitmap)
  callback.call(this, {
    loadingPointer: loadingPointerBitmap,
    progressWidth: bounds.width * 0.8,
    container: container
  })
}, this)

queue.loadManifest([
  {
    id: 'loadingPointer',
    src: loadingPointer
  }, {
    id: 'loadingProgress',
    src: loadingProgress
  }
])
```
最后把container添加到stage中

```
stage.addChild(container)
```
加载场景的效果：

![enter image description here](https://images.gitbook.cn/4ec42530-d1ea-11e8-b949-eb29383f10ad)

---

#### 加载场景添加动画
为加载场景添加动画，需要用到两个preloadjs的方法，progress能够获取加载的进度，complete能够在加载完成之后给与回调方法

```
this.queue = new createjs.LoadQueue()
this.queue.on('progress', progressHandel, this)
this.queue.on('complete', handleComplete, this)
```

首先调用progress方法，每加载完成一个资源，通过progress的event参数，调整一遍pointer图片的x坐标，这样就完成了进度条的移动效果

```
this.queue.on('progress', (event) => {
    obj.loadingPointer.x = obj.progressWidth * event.progress + beginX
}, this)

```
完成之后，调用complete方法，切换场景；这里使用了tweenjs的方法，将当前的container不透明度在0.5秒里面变成0，看起来就是一个渐变效果

```
this.queue.on('complete', (event) => {
    createjs.Tween.get(obj.container).to({
      alpha: 0
    }, 500).call(() => {
      this.stage.removeChild(obj.container)
      render.renderStage.call(this)
      render.renderElement.call(this)
    })
}, this)
```

## Createjs搭建长图场景
<span id="搭建长图场景"></span>

有了搭建加载场景的经验之后，搭建长图的场景就变得非常容易了。同样先新建一个container，然后加载背景图片，调整大小和位置（这里的背景图的y坐标，需要根据实际情况来修改）

```
this.container = new createjs.Container()
// render background
const background = this.queue.getResult('bg')
const backgroundImg = new createjs.Bitmap(background)

this.rate = this.options.width / 750
const stageY = -10726 + this.options.height / this.rate
backgroundImg.setTransform(0, stageY)
this.container.addChild(backgroundImg)
```

**注意：无论用什么游戏或者动画框架，只要是用canvas作为底层技术的，必须从底层往上层添加元素，不然上层的元素会被下层覆盖**


## Createjs制作长按效果
<span id="制作长按效果"></span>

有了背景图之后，先不着急往上面添加元素，因为长图很长，需要先把滚动或者长按效果做出来，方便后面开发

我这里是长按效果，所以需要一个按钮

```
const press = this.queue.getResult('press')
const pressImg = new createjs.Bitmap(press)
pressImg.setTransform(this.options.width * 0.72, this.options.height * 0.5)
this.stage.addChild(pressImg)
events.pressEvent.call(this, pressImg, this.container)
```
按钮里面绑定长按事件，这里使用createjs图片元素的mousedown和pressup方法，设置beginMove为true和false

```
obj.on('mousedown', (e) => {
  if (this.stage.getChildAt(2)) {
    this.stage.removeChild(this.stage.getChildAt(2))
  }
  this.beginMove = true
})

obj.on('pressup', (e) => {
  this.beginMove = false
})
```
设置完之后，其实只是改变一个状态，页面还没有可以动起来，createjs有一个很重要的东西，叫做ticker。可以理解为我们玩游戏时候的fps，每隔一定时间，重新绘制一偏页面


通过调用createjs.Ticker.on('tick', () => {})， 当beginMove为true的时候，每次ticker重新绘制页面，将container的y坐标就+5，这样就实现了页面长按滚动
```
createjs.Ticker.on('tick', () => {
  if (this.beginMove && this.container) {
    // move up
    let heightLimit = this.rate * 10726 - this.options.height
    if (this.container.y < heightLimit && this.container.y + 5 < heightLimit) {
      this.container.y += 5
    } else {
      this.container.y = heightLimit
    }
  }
})
```

## Createjs添加元素以及元素动画
<span id="添加元素以及元素动画"></span>

有了长按效果之后，页面就可以动了，这个时候就可以把其他元素加入到场景中

这里介绍一个重要的概念：精灵图。
![enter image description here](https://images.gitbook.cn/e33e11a0-d2a6-11e8-b28b-41c1c8a6fe34)

如上图，这种把一张张图片整合在按照一定规律整合在一起的图片，就是精灵图。使用这种图片的好处是加载资源只需要请求一次，其余操作都是用css或者js来对图片进行分割，减少请求次数

而作为游戏和动画领域，这种图片会被经常用到，createjs通过SpriteSheet来处理精灵图；通过定义blubData对象，定义精灵图的宽高以及帧数，就可以把一张精灵图变成动画了

```
// render blub
const blub = this.queue.getResult('bulb')
const blubData = {
  images: [blub],
  frames: {width: 212 / 2, height: 124, count: 2},
  animations: {
    light: {
      frames: [0, 1],
      speed: 0.05
    }
  }
}
const blubSpriteSheet = new createjs.SpriteSheet(blubData)
```
新建好了之后，在需要播放的地方使用gotoAndPlay('light')方法播放精灵图动画

```
this.blub1Sprite.gotoAndPlay('light')
```

除此之外，对于不是精灵图的图片，可以使用上面用到的加载图片方法

```
const policy = this.queue.getResult('policy')
this.policyImg = new createjs.Bitmap(policy)
this.policyImg.setTransform(700, -1338 + stageY)
this.container.addChild(this.policyImg)
```
然后使用tweenjs来使图片动起来

```
createjs.Tween.get(this.policyImg).to({
  x: 750 - this.policyImg.getBounds().width
}, 1000)
```
到这里，基本的技术点都已经介绍完了，剩下的就是去加入各种的图片和精灵图这种重复工作

****注意：可能有的小伙伴看到这里会问，为什么我上面写的代码，都有一些-1338 + stageY或者*this.rate这种代码。这个其实跟我的需求有关，也是做游戏和动画必须要做的一些兼容性调整。***

最后，给几个createjs的性能优化建议
- 图片尽量使用精灵图，并进行压缩
- 尽量不要用滤镜和叠加效果，如果要用，直接做进图片里面
- 减少容器之间的嵌套
- 不用的侦听就取消掉，特别是tick
- 所有基于canvas的引擎都一样，尽量少用旋转，缩放，alpha的操控
- 尽量重用一些元素