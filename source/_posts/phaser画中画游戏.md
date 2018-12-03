---
title: Phaser画中画游戏
date: 2018-12-03 15:27:43
tags: Canvas
---

这篇文章主要记录，Phaser画中画游戏的制作流程以及制作难点，主要涉及以下几点：

- [Phaser横屏适配](#Phaser横屏适配)
- [搭建图片](#搭建图片)
- [制作长按效果](#制作长按效果)
- [制作结束页面](#制作结束页面)

<!-- more -->
## Phaser横屏适配
<span id="Phaser横屏适配"></span>
Phaser横屏适配沿用[Phaser小站的横屏适配方案](https://www.phaser-china.com/tutorial-detail-16.html)
**注意：一般在preload的时候，就设置横屏了**

项目开头，先加入这一段代码；作用是在横屏的时候，让游戏旋转90度
```
Phaser.World.prototype.displayObjectUpdateTransform = function () {
  if (!this.game.scale.correct) {
    this.x = this.game.camera.y + this.game.width
    this.y = -this.game.camera.x
    this.rotation = Phaser.Math.degToRad(Phaser.Math.wrapAngle(90))
  } else {
    this.x = -this.game.camera.x
    this.y = -this.game.camera.y
    this.rotation = 0
  }
  PIXI.DisplayObject.prototype.updateTransform.call(this)
}
```
定义一个judgeOrient()方法，在游戏开始的时候和手机方向旋转的时候调用
```
judgeOrient () {
  if (this.game.scale.isLandscape) {
    this.game.scale.correct = true
    this.game.scale.setGameSize(
      this.game.optionsWidth,
      this.game.optionsHeight
    )
  } else {
    this.game.scale.correct = false
    this.game.scale.setGameSize(
      this.game.optionsHeight,
      this.game.optionsWidth
    )
  }
}
```

## 搭建图片
<span id="搭建图片"></span>

搭建图片之前，需要用ps测量几个尺寸：
- 每一张图片与下一张图片衔接区域的大小
- 衔接区域的坐标

测量好之后，就可以开始计算几个值：
- rate 图片缩放的大小
- anchorX和anchorY 缩放中心的坐标
- areaX和areaY 缩放区域的坐标

```
rate: 1920 / 178 / 2, 
anchorX: 724 + (178 * 724) / (724 + 1018),
anchorY: 494 + (102 * 494) / (494 + 484),
areaX: 724,
areaY: 494
```

**这里的游戏逻辑是，把图片从最后一张开始添加（因为后面添加的会叠在上面）。每次添加都需要把图片放大指定的尺寸，使得图片一开始可以跟前面一张图片重合。