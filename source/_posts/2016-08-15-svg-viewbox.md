title: svg-viewbox
toc: false
date: 2016-08-15 11:31:31
tags:
 - svg
categories:
 - web前端
---

svg-viewbox 视野
<!-- more -->

# 概念
参考 [viewbox](http://www.zhangxinxu.com/wordpress/2014/08/svg-viewport-viewbox-preserveaspectratio/)
- viewport
表示SVG可见区域的大小，或者可以想象成舞台大小，画布大小
- viewbox
“视区盒子”， 更形象的解释就是：SVG就像是我们的显示器屏幕，viewBox就是截屏工具选中的那个框框，最终的呈现就是把框框中的截屏内容再次在显示器中全屏显示！
- preserveAspectRatio
实际应用viewBox不可能一直跟viewport穿同一条开裆裤。此时，就需要preserveAspectRatio出马了，此属性也是应用在<svg>元素上，且作用的对象都是viewBox

# preserveAspectRatio 具体计算
***无论是meet还是slice，你是不可能在一种状态下同时看到x, y方向上的位移的。因为总会有一个方向是充满viewport的。***
### meet
保持纵横比***缩放***viewBox适应viewport

```
<svg width="300" height="200" viewBox="0 0 600 500" style="border:1px solid #cd0000;">
    <rect x="10" y="10" width="150" height="150" fill="#cd0000"/>
</svg>
```
用viewbox宽高对应比例，比较大小， 比例大的证明需要缩放更多， 选择这个比计算另外一边：
600/300 <  500/200
所以以高比为准， 缩放宽为 600*2/5 = 240

### slice
保持纵横比同时比例小的方向放大填满viewport
用viewbox宽高对应比例，比较大小， 比例小的的证明更容易挤满， 选择这个比计算另外一边

600/300 < 500/200
所以以宽为准， 那么扩大高： 500* 1/2 = 250

### none
扭曲纵横比以充分适应viewport
