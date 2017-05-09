title: rem-flexible
toc: false
date: 2016-06-21 15:17:52
tags:
  - 移动开发
  - flexible
categories:
  - 前端开发
---

移动开发横屏竖屏以及rem
<!--more-->

# 禁止用户缩放

```html
<meta name="viewport" content="width=device-width,initial-scale=1.0, minimum-scale=1.0, maximum-scale=1.0, user-scalable=no"/>
```
针对上述viewport标签有如下说明
1)、content中的width指的是虚拟窗口的宽度。

2)、user-scalable=no就一定可以保证页面不可以缩放吗？NO，有些浏览器不吃这一套，还有一招就是minimum-scale=1.0, maximum-scale=1.0 最大与最小缩放比例都设为1.0就可以了。

3)、initial-scale=1.0 初始缩放比例受user-scalable控制吗？不一定，有些浏览器会将user-scalable理解为用户手动缩放，如果user-scalable=no，initial-scale将无法生效。

4)、手机页面可以触摸移动，但是如果有需要禁止此操作，就是页面宽度等于屏幕宽度是页面正好适应屏幕才可以保证页面不能移动。

5)、如果页面是经过缩小适应屏幕宽度的，会出现一个问题，当文本框被激活（获取焦点）时，页面会放大至原来尺寸。

### CSS判断横屏竖屏

```css
@media screen and (orientation: portrait) {
  /*竖屏 css*/
}
@media screen and (orientation: landscape) {
  /*横屏 css*/
}

<link rel="stylesheet" media="all and (orientation:portrait)" href="portrait.css">
<link rel="stylesheet" media="all and (orientation:landscape)" href="landscape.css">
```
### js判断横屏竖屏

```js
//判断手机横竖屏状态：
window.addEventListener("onorientationchange" in window ? "orientationchange" : "resize", function() {
        if (window.orientation === 180 || window.orientation === 0) {
            alert('竖屏状态！');
        }
        if (window.orientation === 90 || window.orientation === -90 ){
            alert('横屏状态！');
        }  
    }, false);
//移动端的浏览器一般都支持window.orientation这个参数，通过这个参数可以判断出手机是处在横屏还是竖屏状态。
```

# rem

首先假设我上面的页面设计稿给我时候是按照640的标准尺寸给我的前提下，（当然这个尺寸肯定不一定是640，可以是320，或者480，又或是375）来看一组表格。
![](rem-flexible/rem.jpeg)

## 使用css适应主流屏幕

```css
@media screen and (min-width: 320px) {
    html {font-size: 14px;}
}
 
@media screen and (min-width: 360px) {
    html {font-size: 16px;}
}
 
@media screen and (min-width: 400px) {
    html {font-size: 18px;}
}
 
@media screen and (min-width: 440px) {
    html {font-size: 20px;}
}
 
@media screen and (min-width: 480px) {
    html {font-size: 22px;}
}
 
@media screen and (min-width: 640px) {
    html {font-size: 28px;}
}
```
## 使用js适应所有屏幕

```js
    (function (doc, win) {
      var docEl = doc.documentElement,
        resizeEvt = 'orientationchange' in window ? 'orientationchange' : 'resize',
        recalc = function () {
          var clientWidth = docEl.clientWidth;
          if (!clientWidth) return;
          /** 
          if (clientWidth > 640) clientWidth = 640;
          if (clientWidth < 320) clientWidth = 320;
          */
          docEl.style.fontSize =  (clientWidth / 7.5) + 'px';
        };

      if (!doc.addEventListener) return;
      win.addEventListener(resizeEvt, recalc, false);
      doc.addEventListener('DOMContentLoaded', recalc, false);
    })(document, window);
```

###  通过设计图计算rem方法
上面的750的设计稿， 只需要被除的7.5 和 换算的 100 乘积是当前设计稿即可
```
如果设计稿基于iphone6，横向分辨率为750，body的width为750 / 100 = 7.5rem
如果设计稿基于iphone4/5，横向分辨率为640，body的width为640 / 100 = 6.4rem
```

## 利用js+sass适配
如果是7.5， 则$browser-default-font-size = 100
```javascript
sass
@function px2rem($px){
    //$px为需要转换的字号 
    @return $px / $browser-default-font-size * 1rem; 
    }

SCSS
html { font-size: $browser-default-font-size; } 
.header { font-size: pxTorem(12px); } 

CSS html { font-size: 16px; } 
.header { font-size: 0.75rem; }
```


# 参考资料

- http://isux.tencent.com/web-app-rem.html

