---
title: "Css的一些心得"
date: 2017-09-13
categories: CSS
description: 除了天天见的flex，还有些小零碎的知识点，记录一下。
tags: 
- CSS
---

## css笔记  

最近翻版angular2，由于所有东西都要重写，以前的很多样式都是别人写的，所以没有太多注意，现在自己动手写的时候，发现有点痛苦，写点笔记记录一下。持续更新。。。。。  

#### CSS优先级算法  

- 优先级就近原则，同权重情况下样式定义最近者为准;

- 载入样式以最后载入的定位为准;  

同权重: 内联样式表（标签内部）> 嵌入样式表（当前文件中）> 外部样式表（外部文件中）。  
!important > id > class > tag

#### 实现一个垂直居中的样式  

设置层的外边距  

```
div {
   position: relative;     /* 相对定位或绝对定位均可*/
   width:500px;
   height:300px;
   top: 50%;
   left: 50%;
   margin: -150px 0 0 -250px;      /*外边距为自身宽高的一半 */
   background-color: pink;     /* 方便看效果 */
 }
```


未知容器的宽高，利用 `transform` 属性. 

```
div {
   position: absolute;     /* 相对定位或绝对定位均可*/
   width:500px;
   height:300px;
   top: 50%;
   left: 50%;
   transform: translate(-50%, -50%);
   background-color: pink;     /* 方便看效果 */
}
```

利用*flex*布局  

```
.container {
   display: flex;
   align-items: center;        /* 垂直居中 */
   justify-content: center;    /* 水平居中 */
}
```

```
.container div {
   width: 100px;
   height: 100px;
   background-color: pink;     /* 方便看效果 */
} 
```

#### 弹性盒布局  
一个用于页面布局的全新CSS3功能，Flexbox可以把列表放在同一个方向（从上到下排列，从左到右），并让列表能延伸到占用可用的空间。

较为复杂的布局还可以通过嵌套一个伸缩容器（flex container）来实现。

采用Flex布局的元素，称为Flex容器（flexcontainer），简称"容器"。

它的所有子元素自动成为容器成员，称为Flex项目（flex item），简称"项目"。

常规布局是基于块和内联流方向，而Flex布局是基于flex-flow流可以很方便的用来做居中，能对不同屏幕大小自适应。

在布局上有了比以前更加灵活的空间。  

#### 头大的兼容性问题  

- 浏览器默认的margin和padding不同。解决方案是加一个全局的*{margin:0;padding:0;}来统一。  
-  IE6双边距bug:块属性标签float后，又有横行的margin情况下，在ie6显示margin比设置的大。解决方案是在float的标签样式控制中加入 ——_display:inline;  
-  Chrome 中文界面下默认会将小于 12px 的文本强制按照 12px 显示。可通过加入 CSS 属性-webkit-text-size-adjust: none; 解决。  

#### 清除浮动  
清除浮动是为了清除使用浮动元素产生的影响。浮动的元素，高度会塌陷，而高度的塌陷使我们页面后面的布局不能正常显示。  

1. 父级div定义height；
2. 父级div 也一起浮动；
3. SASS编译的时候，浮动元素的父级div定义伪类:after；

```
&:after,&:before{
       content: " ";
       visibility: hidden;
       display: block;
       height: 0;
       clear: both;
    }
```

相关原理：  

-  display:block 使生成的元素以块级元素显示,占满剩余空间;  
-  height:0 避免生成内容破坏原有布局的高度。    
-  visibility:hidden 使生成的内容不可见，并允许可能被生成内容盖住的内容可以进行点击和交互;  
-  通过content:"."生成内容作为最后一个元素，至于content里面是点还是其他都是可以的，例如oocss里面就有经典的 content:".",有些版本可能content 里面内容为空,一丝冰凉是不推荐这样做的,firefox直到7.0content:”" 仍然会产生额外的空隙；  
-  zoom：1 触发IEhasLayout。  

通过分析发现，除了clear：both用来闭合浮动的，其他代码无非都是为了隐藏掉content生成的内容，这也就是其他版本的闭合浮动为什么会有font-size：0，line-height：0。