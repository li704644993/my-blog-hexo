---
title: "vue优化方法总结"
date: 2019-04-09
categories: JavaScript
description: 记录一下vue中使用过的优化方法，可能不是太全面，后续持续添加
tags: 
- JavaScript
- vue
- 优化
---

### 优化一：路由懒加载

懒加载：也叫延迟加载，记载需要的时候再进行加载。

使用原因：解决进入首页摆平时间过长。

### 优化二：webpack压缩图片

一般vue项目打包过程中，会使用url-loader中设置limit对图片处理，对小于limit的图片转换为base64。对于较大的图片资源，加载比较慢，可以使用image-webpack-loader压缩图片。

<!--more-->

### 优化三：去掉不必要的插件

1. 打包时，讲一些不必要的插件去除，防止打包到无用资源。
2. 引入第三方插件时，按需引入。
3. 使用uglifyJsPlugin压缩混淆js代码。

### 优化四：gzip压缩

在对应nginx下添加gzip配置，包括压缩的最小文件，压缩级别等。

### 优化五：服务器缓存

使用nginx缓存静态资源，为每种格式添加expires过期时间，js等文件尾部会有一个hash值，所以缓存管理比较好做。