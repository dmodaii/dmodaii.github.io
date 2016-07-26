title: browserJudge
toc: false
date: 2016-06-14 14:57:43
tags:
  移动开发
categories:
  前端开发
---

js判断是手机访问还是电脑访问， 以及手机浏览器的类型

<!--more-->

#电脑和手机判断

```js
var system = {  
win : false,  
mac : false,  
xll : false  
};  
//检测平台  
var p = navigator.platform;  
alert(p);  
system.win = p.indexOf("Win") == 0;  
system.mac = p.indexOf("Mac") == 0;  
system.x11 = (p == "X11") || (p.indexOf("Linux") == 0);  
//跳转语句  
if (system.win||system.mac||system.xll) { //转向电脑端
window.location.href="www.111cn.net";  
}else{  
window.location.href="m.111cn.net";  //转向手机端
}
```

#手机浏览器判断

```js
/*
 * 智能机浏览器版本信息:
 *
 */
  var browser={
    versions:function(){
           var u = navigator.userAgent, app = navigator.appVersion;
           return {//移动终端浏览器版本信息
                trident: u.indexOf('Trident') > -1, //IE内核
                presto: u.indexOf('Presto') > -1, //opera内核
                webKit: u.indexOf('AppleWebKit') > -1, //苹果、谷歌内核
                gecko: u.indexOf('Gecko') > -1 && u.indexOf('KHTML') == -1, //火狐内核
                mobile: !!u.match(/AppleWebKit.*Mobile.*/)||!!u.match(/AppleWebKit/), //是否为移动终端
                ios: !!u.match(/\(i[^;]+;( U;)? CPU.+Mac OS X/), //ios终端
                android: u.indexOf('Android') > -1 || u.indexOf('Linux') > -1, //android终端或者uc浏览器
                iPhone: u.indexOf('iPhone') > -1 || u.indexOf('Mac') > -1, //是否为iPhone或者QQHD浏览器
                iPad: u.indexOf('iPad') > -1, //是否iPad
                webApp: u.indexOf('Safari') == -1 //是否web应该程序，没有头部与底部
            };
         }(),
         language:(navigator.browserLanguage || navigator.language).toLowerCase()
};

window.onload=function(){
  var ua = navigator.userAgent;
  var iphone = ua.indexOf("iPhone") > -1;
  var ipod = ua.indexOf("iPod") > -1;
  var ipad = ua.indexOf("iPad") > -1;
  var android = ua.indexOf("Android") > -1;

  if (android){
          document.getElementById('android').style.display="block";
          document.getElementById('android2').style.display="block";
      }else if (iphone || ipod || ipad){
          (function func() {
              var button = document.getElementById('button');
              var guide = document.getElementById('guide');
              if(navigator.userAgent.indexOf('MicroMessenger') != -1) {
                  guide.style.display = 'block';
              }
              button.onclick = function() {
                  guide.style.display = 'none';
              };
          })();

          //dosomething
      }
};
```
