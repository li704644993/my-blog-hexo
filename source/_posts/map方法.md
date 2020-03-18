---
title: "JS中的map"
date: 2016-11-13
categories: JavaScript
description: 最近在刷题的时候，看到一道JS的题目“使用map将字符串转变为整数”里面的结果很难理解，记录下来。
tags: 
- JavaScript
---

 经典js题目  
## 使用map()将字符串变为整数时。
```
var arr = ['1','2','3'];  
var r;  
r = arr.map(parseInt);
```

### 理论结果应该是1,2,3,实际结果却为1, NaN, NaN  
<!--more-->
#### 由于map()接收的回调函数可以有3个参数：callback(currentValue, index, array)，通常我们仅需要第一个参数，而忽略了传入的后面两个参数。不幸的是，parseInt(string, radix)没有忽略第二个参数，导致实际执行的函数分别是: (此处有引用/超链接) 
- parseInt('0', 0); // 0, 按十进制转换. 
- parseInt('1', 1); // NaN, 没有一进制.  
- parseInt('2', 2); // NaN, 按二进制转换不允许出现2。 

#### 可以改为`r = arr.map(Number);`，因为Number(value)函数仅接收一个参数。