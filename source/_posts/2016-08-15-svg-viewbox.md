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
世界是无穷大的， 视野是观察世界的一个矩形区域
- preserveAspectRatio
实际应用viewBox不可能一直跟viewport穿同一条开裆裤。此时，就需要preserveAspectRatio出马了，此属性也是应用在svg元素上，且作用的对象都是viewBox

# preserveAspectRatio 具体计算
***无论是meet还是slice，你是不可能在一种状态下同时看到x, y方向上的位移的。因为总会有一个方向是充满viewport的。***
### meet
保持纵横比**缩放**viewBox适应viewport

```
<svg width="300" height="200" viewBox="0 0 600 500" style="border:1px solid #cd0000;">
    <rect x="10" y="10" width="150" height="150" fill="#cd0000"/>
</svg>
```
用viewbox宽高对应比例，比较大小， 比例大的证明需要缩放更多， 选择这个比计算另外一边：
600/300 <  500/200
所以以高比为准， 缩放宽为 600*2/5 = 240

### slice
保持纵横比同时比例小的方向**放大填满**viewport
用viewbox宽高对应比例，比较大小， 比例小的的证明更容易挤满， 选择这个比计算另外一边

600/300 < 500/200
所以以宽为准， 那么扩大高： 500* 1/2 = 250

### none
扭曲纵横比以充分适应viewport

测试计算
```html
<!DOCTYPE html>
<html>
    <head>
        <title>ViewBox 使用演示</title>
        <style>
            body {
                background: #eee;
            }
            svg {
                position: absolute;
                border: 1px solid green;
                width: 300px;
                height: 200px;
                left: 50%;
                top: 50%;
                margin-top: -100px;
                margin-left: -150px;
                background: white;
            }
            input[type=number] {
                width: 50px;
            }
        </style>
    </head>
    <body>
        <h1>ViewBox 演示</h1>
        <form id="form">
            <fieldset>
                <legend>viewBox</legend>
                <label>x: <input id="vx" type="number" value="0"></label>
                <label>y: <input id="vy" type="number" value="0"></label>
                <label>width: <input id="vw" type="number" value="300"></label>
                <label>height: <input id="vh" type="number" value="200"></label>
            </fieldset>
            <fieldset>
                <legend>preserveAspectRatio</legend>
                <label>align: <select id="align">
                    <option value="none">none</option>
                    <option value="xMinYMin">xMinYMin</option>
                    <option value="xMidYMin">xMidYMin</option>
                    <option value="xMaxYMin">xMaxYMin</option>
                    <option value="xMinYMid">xMinYMid</option>
                    <option value="xMidYMid" selected>xMidYMid</option>
                    <option value="xMaxYMid">xMaxYMid</option>
                    <option value="xMinYMax">xMinYMax</option>
                    <option value="xMidYMax">xMidYMax</option>
                    <option value="xMaxYMax">xMaxYMax</option>
                </select></label>
                <label>meetOrSlice: <select id="meetOrSlice">
                    <option value="meet">meet</option>
                    <option value="slice">slice</option>
                </select></label>
            </fieldset>
        </form>
        <p>
            <svg id="svg" xmlns="http://www.w3.org/2000/svg">
                <!--Face-->
                <circle cx="100" cy="100" r="90" fill="#39F" />
                <!--Eyes-->
                <circle cx="70" cy="80" r="20" fill="white" />
                <circle cx="130" cy="80" r="20" fill="white" />
                <circle cx="65" cy="75" r="10" fill="black" />
                <circle cx="125" cy="75" r="10" fill="black"/>
                <!--Smile-->
                <path d="M 50 140 A 60 60 0 0 0 150 140"
                    stroke="white" stroke-width="3" fill="none" />
                <rect id="viewBoxIndicator" stroke="red" stroke-width="3.5" fill="none" />
            </svg>
        </p>
        <script>
            function update() {
                var viewBox =  [vx.value, vy.value, vw.value, vh.value].join(' ');
                var preserveAspectRatio = [align.value, meetOrSlice.value].join(' ');
                svg.setAttribute('viewBox', viewBox);
                svg.setAttribute('preserveAspectRatio', preserveAspectRatio);

                var rect = viewBoxIndicator;
                rect.setAttribute('x', vx.value);
                rect.setAttribute('y', vy.value);
                rect.setAttribute('width', vw.value);
                rect.setAttribute('height', vh.value);
            }
            form.addEventListener('input', update);
            update();
        </script>
    </body>
</html>
```
