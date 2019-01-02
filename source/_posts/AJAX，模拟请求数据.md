---
title: Mockjs + Vue 拦截 AJAX，模拟请求数据
date: 2018-11-30 17:29:22
tags: Vue
categories:
- Vue
---

日常工作中，前端通过ajax调用后端的接口是非常常见的事情。然而很多情况下，调用接口也会出现很多不方便或者麻烦的情况。

例如：现在有一个抽奖的项目，分为1，2，3等。后台同事写好接口之后，我们前端通过调用抽奖接口，根据不同的奖品等级出现不同的奖品细节。在这种情况下，我们调用接口会出现一个情况，就是调用10次，可能连一次1等奖都没有，因为1等奖中奖率很低。这个时候，为了能测试1等奖，我们就要想个办法来模拟数据了。
<!-- more -->
Mock.js是一个能够生成随机数据，拦截Ajax请求，实现前后端分离 让前端攻城师独立于后端进行开发的库。本场chat我就来介绍一下，这个库的一些用法。
<!--more-->
- Mockjs 应用场景介绍
- Mockjs 简单使用和配置
- Mockjs 搭配 Vue 使用
- Mockjs 注意事项

### 应用场景介绍
1.项目流程比较复杂，而后台同事已经完全写好程序之后，可以使用mockjs来模拟数据进行开发测试

2.需要制作案例库或者作品展示之类的项目，需要把原有项目进行处理，去除所有的http请求

3.项目已经上线，但是临时要修改，不想影响线上页面和数据的时候，可以使用mockjs协助

4.后台同事接口还没写完，而前端想调用接口，可以mockjs模拟数据

5.进行单元测试的时候，可以用mockjs随机数据，模拟各种场景

### 简单使用和配置
Mockjs的使用方法并不难，首先安装mockjs（[支持各种方式安装](https://github.com/nuysoft/Mock/wiki/Getting-Started)）

```
npm install mockjs
bower install --save mockjs
<script type="text/javascript" src="./bower_components/mockjs/dist/mock.js"></script>
```

最简单的用法，随机一组数据
```
import Mock from 'mockjs'

var data = Mock.mock({
    // 属性 list 的值是一个数组，其中含有 1 到 10 个元素
    'list|1-10': [{
        // 属性 id 是一个自增数，起始值为 1，每次增 1
        'id|+1': 1
    }]
})
console.log(JSON.stringify(data, null, 4))
```
MockJs通过在对象属性后面，添加 '|XX'的方式，来进行数据的随机生成。也可以使用mock.random方法进行生成数据。在日常开发中，我们常用的数据大概有如下几类：integer、string、date、time、image、color、text、url、name。Mockjs都有对应的方法来生成这些类型的模拟数据。



一般常用的随机方法有下面几个

- 随机多个重复数据

```
Mock.mock({
  "string|1-10": "★"
})

★
```
- 随机数字

```
Mock.mock({
  "number|1-100": 100
})

89
```
- 随机bool

```
Mock.mock({
  "boolean|1-2": true
})

false
```

- 随机数组

```
Mock.mock({
  "array|1-10": [
    "Mock.js"
  ]
})

{
  "array": [
    "Mock.js",
    "Mock.js",
    "Mock.js"
  ]
}
```

- 随机正则

```
Mock.mock({
  'regexp': /[a-z][A-Z][0-9]/
})

{
  "regexp": "rJ7"
}
```
- 随机日期

```
Mock.mock('@date')

"1980-01-05"
```
- 随机字符串

```
Mock.mock('@word')

"fbzgdlg"
```
Mockjs还有很多随机的方法可以选择，基本上测试上已经够用的了，我在这里就列出了一些我常用的方法，如果还需要更加高级的可以查看mockjs的[文档](http://mockjs.com/examples.html)。

#### **拦截http请求**
Mockjs一个很好用的功能，就是可以拦截ajax，正因为有了这个方法，使得mockjs能够用来为一些中小型项目提供简单的测试环境。先举一个简单的例子：
```
Mock.mock('http://test.com',{
    'name|3':'fei',
    'age|20-30':25,
})

$.ajax({
    url:'http://estcom',
    dataType:'json',
    success:function(e){
       console.log(e)
    }
})
```
执行之后，会发现netword中没有请求，而ajax会返回我们设定的数据。

Mock方法还有几个参数：Mock.mock( rurl?, rtype?, template|function( options ) )。

- rurl表示需要拦截的 URL，可以是 URL 字符串或 URL 正则。
- rtype表示需要拦截的 Ajax 请求类型。例如 GET、POST、PUT、DELETE 等。
- template表示数据模板，可以是对象或字符串。例如 { 'data|1-10':[{}] }、'@EMAIL'。
- function(options)表示用于生成响应数据的函数。

一般使用rurl和template就已经足够模拟接口了，用于测试的请求不需要非常完整，只需要模拟一到两种返回数据类型就可以了。

### 搭配vue使用
现在很多国内项目都是使用Vue进行构建，Mockjs和Vue是可以完美结合的。（建议写一个utils.js文件，专门处理Mockjs的逻辑）

main.js

```
import Vue from 'Vue'
import Router from 'VueRouter'
import utils from '../utils/utils'

const router = new VueRouter({routes})

const getQueryString = (name) => {
  var reg = new RegExp('(^|&)' + name + '=([^&]*)(&|$)');
  var r = window.location.search.substr(1).match(reg);
  if (r != null)
    return unescape(r[2]);
  return null;
}

if (getQueryString('static') === 'true') {
    ......
}

Vue.use(Router)

new Vue({
  el: '#app',
  router,
  template: '<App/>',
  components: {
    App
  }
})

```
utils.js

```
import Mock from 'Mock'

const utils = {
  cj(options) {
    Mock.mock(options.cj, {
      'statusCode': '200',
      'data': {
        'id|1-100000': 1,
        'name': '1元红包',
        'type': 'hb:100',
        'level': '五等奖',
        'sno': 'pd4mjq'
      },
      'msg': '恭喜你，中奖了！'
    })
  }
}

export default utils

```
有一点需要注意的是，Mockjs需要在new vue()之前执行，不然vue已经初始化了。配置好了之后，在项目中可以对接口不做任何调整，我的方法是用链接带参数static=true的方式，生成两个链接，一个正式链接，一个测试链接；然后只需要多写mock的配置，就可以实现正式链接请求后台数据，而测试链接不发送任何请求但项目依然可以完美运行。

```
let postData = this.$qs.stringify({
  answer_id: this.$route.params.answerId,
  vdata: vdata
})

this.$http.request({
  method: 'post',
  url: this.$projectOptions.cj,
  data: postData
})
```
正式链接：http://www.test.com

测试链接：http://www.test.com&static=true


### Mockjs 注意事项
1. 最好通过链接带参数，或者一些标记的方式来调用mockjs，这样在不需要做太多调整，就能弄出测试和正式环境。
2. 由于Mockjs修改了XHR对象，对于一些通过XHR获取资源（例如createjs用preloadjs加载图片）的库需要注意设置成不使用XHR方式加载。
3. Mockjs虽然很好用，但是在大项目中，其实并不合适，最正规的测试应该是搭建测试服务器进行测试。只是在一些中小型公司，没有这样的资源，可以使用这一个折中的办法。
4. Mockjs的模拟数据虽然已经很全面，但是随着需求的变更，肯定会有无法模拟的时候，这个时候可以使用其他随机数据的库，配合mock使用
5. Mockjs已经有一段时间没有更新了，不过很多测试框架像Jest都提供mock方法，如果在时间充裕并需要正规地进行项目测试，建议使用专业的测试框架进行单元测试