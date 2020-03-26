---
title: "MVC、MVVM相关"
date: 2019-04-12
categories: JavaScript
description: 提到MVVM，很多前端开发者都会想到Vue的双向绑定，然而它们并不能划等号，MVVM是一种软件架构模式，而Vue只是一种在前端层面上的实现，其实不单在Vue里，在很多Web 框架应用里都有相关的实现
tags: 
- JavaScript
- 优化
---

#### 什么是MVC？

##### model

数据模型，可以把它当成数据库里查出来后的一条数据，或者是将查询出来的元数据经过处理后的一个特定数据结构。

<!--more-->

##### view

视图，是将内容呈现给用户的界面。View曾最终会将数据模型下的信息，渲染显示为用户易于接受的部分。

##### Controller

Controller是数据模型与View之间的桥梁层，实际界面层的改变都要经过它来控制，而且用户从界面提交的数据也会经过controller的组装生成模型数据，然后改变数据库里的内容。



前后端分离后，就没有v这个概念了。从前端视角去看，Restful接口返回的Json数据当成一个数据模型，作为MVC的Model层，而前端Javascript自身对数据的处理是Contrller层，真正的页面渲染结果是View层。

##### viewmodel

在Model和View之间多了View-model的一层，将模型和数据做了绑定关系。在理想情况下，数据模型返回什么，视图就显示什么。

### vue中的mvvm实现

vue2.0对View-Model的实现本质利用的ES5的Object.defineProperty方法，当Object.defineProperty方法在给数据Model对象定义属性的时候先挂载一些方法，在这些方法里实现与界面的值绑定响应关系，当应用的属性被读取或者写入的时候便会触发这些方法，从而达到数据模型里的值发生变化时同步响应到页面上。

当new Vue在实例化的时候，首先将data方法里返回的对象属性都挂载上setter方法，而setter方法里将页面上的属性进行绑定，当页面加载时，浏览器提供的DOMContentloaded事件触发后，调用mounted挂载函数，开始获取接口数据，获取完成后给data里属性赋值，赋值的时候触发前面挂载好的setter方法，从而引起页面的联动，达到响应式效果。

##### vue3.0的优化

Proxy是ES6里的新构造函数，它的作用就是代理，简单理解为有一个对象，不想完全对外暴露出去，想做一层在原对象操作前的拦截、检查、代理，这时候你就要考虑Proxy了。

Reflect是ES6里的新的对象，非构造函数，不能用new操作符。可以把它跟Math类比，Math是处理JS中数学问题的方法函数集合，Reflect是JS中对象操作方法函数集合，它暴露出来的方法与Object构造函数所带的静态方法大部分重合，实际功能也类似，Reflect的出现一部分原因是想让开发者不直接使用Object这一类语言层面上的方法，还有一部分原因也是为了完善一些功能。Reflect提供的方法还有一个特点，完全与Proxy构造函数里Hander参数对象中的钩子属性一一对应。

