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
是默认缩放为100%的情况下，设备像素和CSS像素的比值
DPR = 设备像素 / CSS像素 (某一方向上)
>iphon4 iPhone6的设备宽度和高度为375pt * 667pt,可以理解为设备的独立像素；而其dpr为2，根据上面公式，物理像素为750pt * 1334pt

### ppi  像素密度（pixel density）
要计算显示器的每英寸像素值，首先要确定屏幕的尺寸和分辨率
![](dpr-flexible/ppi1.png)

>在桌面端，css的1个像素往往都是对应着电脑屏幕的1个物理像素
而在手机端，由于屏幕尺寸的限制，缩放是经常性的操作。
设备像素(深蓝色背景)、CSS像素(半透明背景)
(上图)当用户进行缩小操作时，一个设备像素覆盖了多个CSS像素
(下图)当用户进行放大操作时，一个CSS像素覆盖了多个设备像素

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

``````


### 使用postcss-flexible处理css
[require('postcss-flexible')({remUnit: 75} 75和dpr2为参照点


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
    - flexible工具
  https://github.com/crossjs/postcss-flexible
  https://github.com/amfe/lib-flexible
    - flexible.js下载
  http://www.w3cplus.com/sites/default/files/blogs/2016/1601/flexible.js

  - 设备参数
  https://material.io/devices/
  https://www.paintcodeapp.com/news/ultimate-guide-to-iphone-resolutions
  ![](dpr-flexible/Android-Design-Cheat-Sheet-highres.png)