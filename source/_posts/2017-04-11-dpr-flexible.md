title: dpr-flexible
toc: false
date: 2016-06-21 15:17:52
tags:
  - 移动开发
  - flexible
categories:
  - 前端开发
---

移动开发利用dpr弹性布局
<!--more-->

# 概念
### 设备像素(device pixels)
设备屏幕的物理像素，任何设备的物理像素的数量都是固定的（与密度无关的像素)。设备像素也被称为物理像素,他是显示设备中一个最微小的物理部件。一种基于屏幕密度的抽象单位。在每英寸160点的显示器上，1dp = 1px。

### CSS像素(CSS pixels): 
又称为逻辑像素，是为web开发者创造的，在CSS和javascript中使用的一个抽象的层

### 设备独立像素(device independent pixels)
meta里面设置width=device-width，这个device-width就是设备独立像素 
![](dpr-flexible/chrome1.png)
在chrome里  看到的375*667  320*480等等都是设备独立像素，它们在数值上与css数值是相等的

### 设备像素比DPR(devicePixelRatio)
DPR = 设备像素 / dpi(视口大小) 

<!--是默认缩放为100%的情况下，设备像素和CSS像素的比值
DPR = 设备像素 / CSS像素 (某一方向上)-->
>iphon4 iPhone6的设备宽度和高度为320 * 480 dp,可以理解为设备的独立像素；而其dpr为2，根据上面公式，物理像素为640 * 960 px

### ppi  像素密度（pixel density）
要计算显示器的每英寸像素值，首先要确定屏幕的尺寸和分辨率
![](dpr-flexible/ppi1.png)

>在桌面端，css的1个像素往往都是对应着电脑屏幕的1个物理像素
而在手机端，由于屏幕尺寸的限制，缩放是经常性的操作。
设备像素(深蓝色背景)、CSS像素(半透明背景)
(上图)当用户进行缩小操作时，一个设备像素覆盖了多个CSS像素
(下图)当用户进行放大操作时，一个CSS像素覆盖了多个设备像素
![](dpr-flexible/dpr.jpg)
![](dpr-flexible/dpr1.gif)
![](dpr-flexible/dpr2.gif)
不论我们进行缩小或放大操作，*** 元素设置的CSS像素(如width:300px)是始终不变的，而一个CSS像素对应多少个设备像素是根据当前的缩放比例来决定的 ***
（iphone4的分辨率提高了一倍，但屏幕尺寸却没有变化，这意味着同样大小的屏幕上，像素多了一倍，于是DPR = 2）

ppi(pixels per inch)：图像的采样率（在图像中，每英寸所包含的像素数目）
dpi(dots per inch)：打印分辨率（每英寸所能打印的点数，即打印精度）等。

### Visual viewport, Layout viewport
- Visual viewport
The visual viewport is the part of the page that’s currently shown on-screen. The user may scroll to change the part of the page he sees, or zoom to change the size of the visual viewport.
Layout viewport
- Layout viewport
However, the CSS layout, especially percentual widths, are calculated relative to the layout viewport, which is considerably wider than the visual viewport.
Thus the element takes the width of the layout viewport initially, and your CSS is interpreted as if the screen were significantly wider than the phone screen. This makes sure that your site’s layout behaves as it does on a desktop browser.
How wide is the layout viewport? That differs per browser. Safari iPhone uses 980px, Opera 850px, Android WebKit 800px, and IE 974px.


# 实战应用
### 动态更改viewport的content

> 安卓的dpr1， ios看情况，然后在头部加上对应的 <html data-dpr="2" style="font-size: 75px;" dir="ltr">  <meta name="viewport" content="initial-scale=0.5, maximum-scale=0.5, minimum-scale=0.5, user-scalable=no"> 缩放比例为 1/dpr (以dpr1为标准的理想尺寸，当我们在dpr上显示时候，css宽度已经为原来的2倍，整个Layout viewport都扩大为原来的2倍，所以缩放到1/2)

```JavaScript
    // postcss-flexible is required
    // components/c-image.vue is for flexible images
    !function (a, b) {
      function c() {
        var b = f.getBoundingClientRect().width;
        b / i > 540 && (b = 540 * i);
        var c = b / 10;
        f.style.fontSize = c + "px", k.rem = a.rem = c
      }

      var d, e = a.document, f = e.documentElement, g = e.querySelector('meta[name="viewport"]'),
        h = e.querySelector('meta[name="flexible"]'), i = 0, j = 0, k = b.flexible || (b.flexible = {});
      if (g) {
        console.warn("将根据已有的meta标签来设置缩放比例");
        var l = g.getAttribute("content").match(/initial\-scale=([\d\.]+)/);
        l && (j = parseFloat(l[1]), i = parseInt(1 / j))
      } else if (h) {
        var m = h.getAttribute("content");
        if (m) {
          var n = m.match(/initial\-dpr=([\d\.]+)/), o = m.match(/maximum\-dpr=([\d\.]+)/);
          n && (i = parseFloat(n[1]), j = parseFloat((1 / i).toFixed(2))), o && (i = parseFloat(o[1]), j = parseFloat((1 / i).toFixed(2)))
        }
      }
      if (!i && !j) {
        var p = a.navigator.userAgent, q = (!!p.match(/android/gi), !!p.match(/iphone/gi)),
          r = q && !!p.match(/OS 9_3/), s = a.devicePixelRatio;
        i = q && !r ? s >= 3 && (!i || i >= 3) ? 3 : s >= 2 && (!i || i >= 2) ? 2 : 1 : 1, j = 1 / i
      }
      if (f.setAttribute("data-dpr", i), !g)if (g = e.createElement("meta"), g.setAttribute("name", "viewport"), g.setAttribute("content", "initial-scale=" + j + ", maximum-scale=" + j + ", minimum-scale=" + j + ", user-scalable=no"), f.firstElementChild) f.firstElementChild.appendChild(g); else {
        var t = e.createElement("div");
        t.appendChild(g), e.write(t.innerHTML)
      }
      a.addEventListener("resize", function () {
        clearTimeout(d), d = setTimeout(c, 300)
      }, !1), a.addEventListener("pageshow", function (a) {
        a.persisted && (clearTimeout(d), d = setTimeout(c, 300))
      }, !1), "complete" === e.readyState ? e.body.style.fontSize = 12 * i + "px" : e.addEventListener("DOMContentLoaded", function () {
        e.body.style.fontSize = 12 * i + "px"
      }, !1), c(), k.dpr = a.dpr = i, k.refreshRem = c, k.rem2px = function (a) {
        var b = parseFloat(a) * this.rem;
        return "string" == typeof a && a.match(/rem$/) && (b += "px"), b
      }, k.px2rem = function (a) {
        var b = parseFloat(a) / this.rem;
        return "string" == typeof a && a.match(/px$/) && (b += "rem"), b
      }
    }(window, window.lib || (window.lib = {}));

```

```
(function(designWidth) {
        'use strict';
        var docEl = document.documentElement;
        var dpr = Math.min(Math.floor(window.devicePixelRatio), 3);
        var scale = 1 / dpr;
        var $viewport = document.querySelector('meta[name="viewport"]');
        var setDpr = function() {
          debugger
            // viewport
            var content = 'initial-scale=' + scale + ',maximum-scale=' + scale + ',minimum-scale=' + scale + ',user-scalable=no,width=device-width';
            if ($viewport) {
                $viewport.setAttribute('content', content);
            } else {
                var metaViewport = '<meta name="viewport" content="' + content + '"/>';
                document.write(metaViewport);
            }
            // font
            docEl.setAttribute("data-dpr",dpr);
            var width = docEl.clientWidth;
            if (width / dpr > 450) width = dpr * 450;
            var fontSize = width / designWidth * 100;
            docEl.style.fontSize = fontSize + 'px';
        }

        setDpr();
        window.addEventListener('resize', setDpr);
    })(640);
```


### 使用postcss-flexible处理css

- dpr
dpr1到dpr2： px * 2 算出大小那么 屏幕dpi比如320x568，px*2 = 设计图大小，就相当把设计图原稿放在手机上了（设计图的一个点是由2个物理像素组成的，所以就是高清了），
然后将viewport缩小为1/2 使适应屏幕实际宽度， 屏幕宽度没变，像素变成原来2倍， 所以是dpr2了

> 关于px为1的问题， 设计稿如果有1px对应在dpr1中设计时候 为0.5px ，但是有些android设备不支持小数会出现问题

320 * 480 dp  |  dpr 2 | 640 * 960 px


- rem
[require('postcss-flexible')({remUnit: 75} 75和dpr2为参照点
>首先，目前视觉稿大小分为640，750以及，1125这三种。
当前方案会把这3类视觉稿分成100份来看待（为了以后兼容vh，vw单位）。每一份被称为一个单位a。同时，1rem单位认定为10a。
拿750的视觉稿举例：
1a = 7.5px
1rem = 75px
因此，对于视觉稿上的元素的尺寸换算，只需要原始px值除以rem基准px值即可。例如240px * 120px的元素，最后转换为3.2rem * 1.6rem。


```css

.selector {
  font-size: dpr(32px);
  width: rem(75px);
  line-height: 3;
  /* replace all @[1-3]x in urls: `/a/@2x/x.png`, `/a@2x/x.png` or `/a/x@2x.png` */
  background-image: url(/images/qr@2x.png);
}
After processing:

.selector {
  width: 1rem;
  line-height: 3;
}

[data-dpr="1"] .selector {
  font-size: 16px;
  background-image: url(/images/qr@1x.png);
}

[data-dpr="2"] .selector {
  font-size: 32px;
  background-image: url(/images/qr@2x.png);
}

[data-dpr="3"] .selector {
  font-size: 48px;
  background-image: url(/images/qr@3x.png);
}
```
### 使用postcss的precss
- 字体和1px
```
@define-mixin dpr-font $font-size{
      [data-dpr="1"] & {
          font-size: calc($font-size / 2);
      }
      [data-dpr="2"] & {
        font-size: $font-size;
      }
      [data-dpr="3"] & {
        font-size: calc($font-size * 3 / 2);
      }
}
@define-mixin dpr-line-height $line-height{
      [data-dpr="1"] & {
        line-height: calc($line-height / 2);
      }
      [data-dpr="2"] & {
        line-height: $line-height;
      }
      [data-dpr="3"] & {
        line-height: calc($line-height * 3 / 2);
      }
}
@define-mixin dpr-border-width $top:0, $right:0, $bottom:0, $left:0 {  
      [data-dpr="1"] & {
        border-width: calc($top / 2) calc($right / 2) calc($bottom / 2) calc($left / 2) ;
      }
      [data-dpr="2"] & {
        border-width: $top $right $bottom $left;
      }
      [data-dpr="3"] & {
        border-width: calc($top * 3 / 2) calc($right * 3 / 2) calc($bottom * 3 / 2) calc($left * 3 / 2) ;
      }
}
```
- 图片
```css

@define-mixin dpr-img $image, $size: 100% 100%{
    background-size: $size;
    [data-dpr="1"] & {
      background-image: url(../../images/mobile/$(image)@2x.png);
    }
    [data-dpr="2"] & {
      background-image: url(../../images/mobile/$(image)@2x.png);
    }
    [data-dpr="3"] & {
      background-image: url(../../images/mobile/$(image)@3x.png);
    }
}
```


# 参考资料

- 概念
  - dpr
   http://www.cnblogs.com/xiaohuochai/p/5494624.html
   http://yunkus.com/physical-pixel-device-independent-pixels/
  - Visual viewport, Layout viewport
   http://yunkus.com/meta-viewport-usage/
   http://www.quirksmode.org/mobile/viewports2.html

  - flexible
    - 统筹知识
  http://www.w3cplus.com/mobile/lib-flexible-for-html5-layout.html
  https://segmentfault.com/a/1190000003690140
    - flexible工具
  https://github.com/crossjs/postcss-flexible
  https://github.com/amfe/lib-flexible
    - flexible.js下载
  http://www.w3cplus.com/sites/default/files/blogs/2016/1601/flexible.js
 
    - px为1处理
  http://www.jianshu.com/p/d62d22b44ce4
  http://www.ghugo.com/css-retina-hairline/
  - 设备参数
  https://material.io/devices/
  https://www.paintcodeapp.com/news/ultimate-guide-to-iphone-resolutions
  ![](dpr-flexible/Android-Design-Cheat-Sheet-highres.png)