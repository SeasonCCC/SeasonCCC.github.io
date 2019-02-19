---
title: 使用 Zeal 打造属于自己的文档
date: 2019-02-15 10:09:14
tags: Tools
categories:
- Tools
---

作为一名程序开发者，翻查文档成为了每天都必须去做的事情。然而，由于使用到的知识和工具不相同，所以几乎每次都需要重新打开官网的文档进行搜索，而且不同知识和工具的文档还需要切换浏览器标签来浏览，十分不方便。再进一步，我们工作中，所用到的一些账号、密码以及日常的一些小知识点，如果归纳总结之后，可能也只是放在 word 或者 txt 里面，查阅也是比较不方便的。

那么有没有一个工具，即可以让我们方便查阅文档，又可以把自己日常用到的资料存起来的工具呢，本场 Chat 就来介绍一下 Zeal 这一离线文档工具。

***

## 引言
***
作为一名程序开发者，翻查文档成为了每天都必须去做的事情。然而，由于使用到的知识和工具不相同，所以几乎每次都需要重新打开官网的文档进行搜索，而且不同知识和工具的文档还需要切换浏览器标签来浏览，十分不方便。再进一步，我们工作中，所用到的一些账号、密码以及日常的一些小知识点，如果归纳总结之后，可能也只是放在 word 或者 txt 里面，查阅也是比较不方便的。

那么有没有一个工具，即可以让我们方便查阅文档，又可以把自己日常用到的资料存起来的工具呢，下面就来介绍一下 Zeal 这一离线文档工具。


![](https://user-gold-cdn.xitu.io/2019/2/19/16903cf224d3390d?w=886&h=515&f=png&s=70130)

## Zeal 简介
***
Zeal 是一款免费的离线文档软件，能够让开发者更加便捷地查阅api文档。目前该软件有 windows、mac 以及 linux 版本，内部有超过 200 个文档，涵盖了几乎所有程序开发用到的库、框架以及语言，是一款十分实用的软件。

[Zeal官网](https://zealdocs.org)

[Zeal下载](https://zealdocs.org/download.html)

下载安装完成之后，界面上是看不到任何的文档的，因为需要去下载文档（Zeal 中文档的格式称为 Docsets）。在首页上面按下 Docsets 或者 Tools -> Docsets，选择你想要的文档进行下载，下载完成后就可以看到你所需要的文档了。
![](https://user-gold-cdn.xitu.io/2019/2/19/16903d841d25ad85?w=1559&h=791&f=png&s=77403)

> 除了在 Zeal 的官网上面下载 Docsets 之外，还能通过 Add Feed 来添加。国外有热心的开发者收集了一个 [Docsets 集合](https://zealusercontributions.now.sh/)，我们只需要点开自己需要的 Docsets，复制 xml 地址到 Add Feed 里面，就可以对应下载 Docsets 了。

下载好自己需要的 Docsets 之后，日常就可以在工作中使用离线文档进行工作了。日常使用过程中，有几点需要注意：

*  Zeal 原理是打开 html 的页面，所以它相当于一个浏览器，尽量养成定时关闭不用文档的习惯
*  Zeal 能够设置打开的快捷键，设置了之后就能快速打开 Zeal 查阅文档
*  Zeal 左上角具备搜索功能，在前面输入“文档名称:内容”就可以对指定的文档进行搜索

![](https://user-gold-cdn.xitu.io/2019/2/19/16903e04b6c2711c?w=1901&h=995&f=png&s=110733)

## Zeal 创建自己专属的文档项目
***
经过上面的步骤后，这个文档系统已经可以很好地为我们日常开发服务了，但是除了下载常用的 api 文档之外，能不能编写我们自己的文档呢？

Zeal 官方写了一个教我们如何去编写自己的 Docsets 的[文档](https://kapeli.com/docsets)，但是该文档写得比较简单，并且没有详细地操作指引，操作起来比较复杂。

> 经过实验之后，Zeal 的 Docsets 其实是 html 的集合，那么我们可以先用文档工具，生成一些静态的 html 文档。然后通过 Docsets 官方提供的 Docsets 生成器来把 html 生成 Docsets，这样就可以生成出属于我们自己的 Docsets 了。

### 合适的文档生成器
目前各种开发语言都有文档生成器，我比较熟悉的 Node.js 就有数十个像 Gitbook、Docsify、Vuepress 等等。但是并不是每一个都适合用来制作 Docsets，举个例子：

> Docsify 是一个很棒的生成器，但是用于 Docsets 的话就会有问题。原因是因为 Vuepress 是通过 js 读取 Markdown 来实现的，而 Zeal 内部是一个浏览器，并没有静态服务器，所以制作出来的 Docsets 会出现跨域的问题。

最终我选择了使用 Gitbook 来制作 Docsets，它能生成静态的 Html 文件，并且能够通过本地双击打开，能够跟 Zeal 完美融合。

### 编写文档
确定了使用 Gitbook 之后，先安装 Gitbook：
```npm install gitbook-cli -g```

然后，使用 Gitbook 创建项目：
```gitbook init```

创建完之后，会看到如下目录结构：
```
├── README.md
├── SUMMARY.md
├── chapter1
│   ├── README.md
│   ├── section1.1.md
│   └── section1.2.md
└── chapter2
    └── README.md
```

创建完成之后，通过命令 ```gitbook serve``` 即可看到文档的样式：
![](https://user-gold-cdn.xitu.io/2019/2/19/16904873fd60143f?w=1423&h=444&f=png&s=41925)

接下来就是每个开发者根据自己的需要，编写自己的 markdown 文件和修改 SUMMARY.md。由于文档是离线的，所以不用担心会被其他人盗取，可以把日常用到的账号密码、代码片段、常用的资料都分类写进去。

写完之后，通过命令 ``` gitbook build ``` 生成 html 文件。生成后会见到一个 ```_book``` 的目录，所有的 html 文件都在里面，这样文档就算编写完成了。

## 把 html 生成 Docsets
***
有了文档对应的 html 之后，需要把 html 生成 Docsets。我使用 Node.js 生成，在 npm 上面找了一个叫 [docset-generator](https://www.npmjs.com/package/docset-generator) 的插件。通过以下代码，即可生成对应的 Docsets：
```
let DocSetGenerator = require("docset-generator").DocSetGenerator;
let docSetGenerator = new DocSetGenerator({
  destination: "./output/",
  name: "Season",
  documentation: "./personal-doc/_book",
  icon: './icon.png',
  entries: [ // entries 可以设置 Docsets 的分类，一般一个分类对应一个 html
    {
      name: "个人资料",
      type: "Guide",
      path: "./index.html"
    },
    ...
  ]
});
docSetGenerator.create();
```

这里我把 Gitbook 和 docset-generator 整合到一个项目中，通过 [npm-run-all](https://www.npmjs.com/package/npm-run-all) 插件，先调用 Gitbook 生成 html，然后再生成 Docsets：
```
...
"scripts": {
  "start": "gitbook build ./personal-doc && node index.js"
},
...
```
生成完之后，将会得到一个 XXX.docsets 的文件夹，接下来就是最后的一步，把这个文件夹放进 Zeal 里面。

在 Zeal 里面有一个叫 docsets 的文件夹，进去之后会看到下载的 docsets 都在里面，把刚刚生成的 docsets 放进去，重启一下 Zeal 就可以看到自己的文档了。
![](https://user-gold-cdn.xitu.io/2019/2/19/16904a0175272bd8?w=1917&h=1042&f=png&s=140316)

## 结语
***
以上是我在工作过程中，根据自己的开发习惯所做出来的一个小文档，如果大家有其他方便的工具，可以一起讨论一下，有任何问题欢迎骚扰~