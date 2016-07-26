title: iOS端网页中js方法与OC方法如何交互(JavaScriptCore)
toc: false
date: 2016-04-22 20:53:19
tags: 
	- iOS
	- HybridApp
categories: iOS
---
![](iOS-javascriptcore-call-native/hybrid.jpg)

骚年，你好哦。咱们这篇文章主要讲解一下``JavaScriptCore``框架的使用，了解一下使用这个框架怎么让网页中的js代码来调用本地的代码。以及native端又怎么跟网页端交互的，其实也就是别人口中所说的``Hybrid App``的思想🙃，别急听我慢慢道来，我都是以最简单的例子来说明问题。。。

<!-- more -->

在了解本篇文章之前你需要先知道一下``JavaScriptCore``框架是什么？推荐大家看看下列文章
[JavaScriptCore初探](https://hjgitbook.gitbooks.io/ios/content/04-technical-research/04-javascriptcore-note.html)

很久以前，我们没有``JavaScriptCore``是怎么实现网页和native交互的呢，很麻烦，很苦逼，可以看一下这篇文章
[WebView 与 JS 的交互](http://vonglo.me/2015/10/19/WebView%E4%B8%8E%20JS%20%E7%9A%84%E4%BA%A4%E4%BA%92/)
但是从iOS7以后我们幸福了，JSCore框架的出生让我们有了很棒的工具，只需要写很少的代码就可以实现JS与Native端的交互（Native端就是说的OC/Swift代码）,目前我们只需要了解使用JavaScriptCore的这种办法就行了，因为这是未来！

# JavaScriptCore简述
啰嗦几个概念

1. JSValue: 代表一个JavaScript实体，一个JSValue可以表示很多JavaScript原始类型例如boolean, integers, doubles，甚至包括对象和函数。
2. JSContext: 代表JavaScript的运行环境，你需要用JSContext来执行JavaScript代码。所有的JSValue都是捆绑在一个JSContext上的。
3. JSExport: 这是一个协议，可以用这个协议来将原生对象导出给JavaScript，这样原生对象的属性或方法就成为了JavaScript的属性或方法，非常神奇。

下面我们通过一个最简单的例子，来说明JS与OC的交互，既然说到Hybrid App肯定是少不了网页。我们就先实现这个网页。
# 网页
网页内容如下：

```html
<!DOCTYPE html>
<html>
    <head>
	    <meta charset="utf-8">
        <title>test javascript</title>
    </head>
    <body>
        <div>
        		<!--为什么使用ttf.调用，下面会讲到-->
            <button onclick="ttf.nslog('JS与OC交互');">点击我然后看xcode的log</button> 
        </div>
    </body>
</html>
```

![](iOS-javascriptcore-call-native/button.png)

很简单的一个HTML网页，我们想要做的就是在这个网页显示的时候点击按钮会调用OC的NSLog方法，打印出“``JS与OC交互``”信息。这个网页是使用UIWebView显示的。
# 注入对象
我们要想调用OC方法，肯定是和OC对象扯不开关系的，所以我们自定义一个类，调用一个JS方法就会去调用这个类中的对象方法。
### 关于要注入的这个对象
JavaScriptCore支持将对象注入JSContext运行环境中，直接使用注入的名称调用JS方法就会自动调用这个对象对应的OC方法。我们注入的这个对象是有一定要求的，你可能想到了，就是要继承某个协议，这个协议就是JSExport。

因为OC语言和JS语言设计本身的差异，因此JSContext不能把OC的对象直接转成JS的对象，因此官方提供了一个JSExport的协议标识，来实现这个转换，通过直接继承JSExport定义自己的protocol，可以让自定义对象在JavaScript环境中使用，任何注册到JS环境中的对象都需要标记为JSExport

万句话不如一句代码，来看一下代码

```objc
//定义一个协议继承JSExport协议
@protocol PersonJSExport <JSExport>
- (void)nslog:(NSString *)str; //协议里面要声明调用的方法
@end

//我们的自定义类要遵循我们自己定义的这个协议
@interface MyJSObject() <PersonJSExport>
- (void)nslog:(NSString *)str;
@end

@implementation MyJSObject
- (void)nslog:(NSString *)str {
    NSLog(@"%@", str);
}
@end

```

我们自定的对象必须需要这样写，首先自定义一个协议遵循``JSExport``，然后我们自定义对象再遵循我们的自定义协议，并把我们需要与JS交互的方法定义与实现好。

我们的对象定义好了，接下来就是把对象注入到JS环境中。所谓注入到JS环境其实就是给JSContext对象赋值，其实可以把JSContext看成是一个字典，里面存储了很多JSValue值。任何被存进去的东西都会被包装成JSValue。

```objc
MyJSObject *jsObject = [MyJSObject new]; //创造一个对象
JSContext *context = [JSContext new];	//创建JS运行环境，这个只是创建一个JSContext环境的例子，并不是我们最终使用的那个。
context[@"ttf"] = person;	//将对象注入JS运行环境
```

那么问题又来了，我们怎么拿到这个JSContext环境呢？因为我们使用的是UIWebView显示的HTML网页，那么JS代码也是在这个webView中运行，所以我们要拿JSContext环境是需要向这个webView拿的。

# 向UIWebView对象拿JSContext环境对象
我们从UIWebView中拿JSContex环境t对象有两种方法，但是这两种方法官方文档中都没有讲述。可能苹果不想让我们使用hybrid方式。😪
### 使用KVC
我们可以使用KVC方式向webView拿这个JSContext对象

```objc
JSContext *context = [self.webView valueForKeyPath:@"documentView.webView.mainFrame.javaScriptContext"];

MyJSObject *jsObject = [MyJSObject new];
_context[@"ttf"] = jsObject;	//将对象注入这个context中

```
这样子一句代码就能拿到了这个webView的JS环境对象，是不是很简单。
很不幸，因为KVC的这个方式比较暴力，说不一定我们的应用在上架前夕就被Apple拒了，真心是太可惜了。但是我们还有另外一个不暴力但是很hack的方式。

### 给NSObject添加分类
在OS X中，WebFrameLoadDelegate负责WebKit与NSWebView的通信，由于NSWebView内部仍然使用WebKit渲染引擎，若要侦听渲染过程中的一系列事件，则必须使用WebFrameLoadDelegate对象：

但是在iOS中没有这个对象，但是有Nick Hodapp对象，webView在加载网页的时候如果网页有js代码(也就是<script></script>中的内容)，那么就会去判断是否实现了``webView:didCreateJavaScriptContext:forFrame:``这个方法，有就执行，没有就不执行，总之只有有JS代码运行就会去判断。**注意：如果没有js代码，那么就不会去判断是否实现这个方法。**这个方法第二个参数就是我们要的JSContext环境对象。所以实现这个方法来获得我们的JSContext环境对象，因为所有的类都是继承NSObject，所以我们给NSObject的category中重写这个方法。

```objc
@implementation NSObject (JSTest)
- (void)webView:(id)unuse didCreateJavaScriptContext:(JSContext *)ctx forFrame:(id)frame {
    [[NSNotificationCenter defaultCenter] postNotificationName:@"DidCreateContextNotification" object:ctx];
}
@end
```

调用这个方法的时候WebKit就已经获取到了JSContext对象，在这个方法中我们发出一个通知，这个通知会把获取到的JSContext环境对象传递出去。

我们通知添加是在ViewController的``viewDidLoad``方法中添加的

```objc
- (void)viewDidLoad {
    [super viewDidLoad];
    [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(didCreateJSContext:) name:@"DidCreateContextNotification" object:nil];
    
    NSURL *url = [[NSBundle mainBundle] URLForResource:@"test" withExtension:@"html"];
    [self.webView loadRequest:[NSURLRequest requestWithURL:url]];
}
```

但是有时候会有很多的UIWebView，一个屏幕打开很多页面，我们不知道我们获取的这个JSContext对象是属于哪一个webView的，所以我们让先让每一个webView对象都执行一段JS方法，把属于这个webView的标识注入这个JSContext环境中，我们通过判断获取到的这个JSContext环境中有没有这个标识来判断是否属于当前的webView对象。

我们在``- (void)didCreateJSContext:(NSNotification *)notification``方法中对这个JSContext对象进行判断:

```objc
- (void)didCreateJSContext:(NSNotification *)notification {
    NSString *indentifier = [NSString stringWithFormat:@"indentifier%lud", (unsigned long)self.webView.hash];
    NSString *indentifierJS = [NSString stringWithFormat:@"var %@ = '%@'", indentifier, indentifier];
    [self.webView stringByEvaluatingJavaScriptFromString:indentifierJS];

    JSContext *context = notification.object;
    //判断这个context是否属于当前这个webView
    if (![context[indentifier].toString isEqualToString:indentifier]) return;
    
    _context = context;		//如果属于这个webView
    MyJSObject *jsObject = [MyJSObject new];
    _context[@"ttf"] = jsObject;	//将对象注入这个context中
}
```

这样子我们就拿到JSContext环境对象，并且把对象注入到了这个环境中。也许你注意到了，我注入对象的时候键为``ttf``，而我们网页中的调用对象也是``ttf``，你是否猜到了什么？  

是的当我们点击按钮的时候JS端调用``ttf.nslog('JS与OC交互')``，因为我们把ttf这个OC对象注入到了JS环境中，那么JSCore会去``ttf``对应的这个对象（本质上就是MyJSObject对象，只是名字一个名字而已）中寻找``nslog``方法，找到我们原来自定义类``MyJSObject ``有实现这个方法，那么就去调用OC类中的``nslog``方法。可以看到打印结果。**可以看到JSCore会自动将参数传递过来的！**

![](iOS-javascriptcore-call-native/testresult.png)

这就是JS方法调用native端的OC方法，关键点就是获取这个JSContext环境。拿到了这个环境再把对象注入进去，调用JS方法就会去调用相应对象的OC方法了。

# JSExportAs
上面所述的JS方法必须和自定义方法一模一样，JS中的调用方法``ttf.nslog('JS与OC交互')``;自定义类中的方法``- (void)nslog:(NSString *)str``，他们都是nslog，如果我们JS调用的方法和OC方法都要一样的话，那我们OC那么多那么长的方法怎么办？放心苹果考虑到了，``JavaScriptCore``框架提供了``JSExportAs``，他可以将JS方法和我们的OC方法绑在一起，那么调用JS方法就会去调用我们绑定的那个OC方法。这个操作需要在自定义协议中实现。

```objc
@protocol PersonJSExport <JSExport>
//将JS的nslog和我们OCprintSome方法绑定在一起
JSExportAs(nslog,
           - (void)printSome:(NSString *)string
           );
@end

@interface MyJSObject() <PersonJSExport>
@end

@implementation MyJSObject
- (void)printSome:(NSString *)string {
    NSLog(@"%@", string);
}
@end

```

这时候再点击按钮就会去调用``printSome ``函数，很简单吧！

# OC类中调用JS方法
说好的JS与OC交互呢？怎么一直在说JS调用OC方法？下面咱们就说说JS与OC交互-OC方法调用JS方法。

我想实现的是点击网页中按钮，Xcode打印完消息过1秒钟再让网页中alert一个框。
看下效果图：

![](iOS-javascriptcore-call-native/OC-JS.gif)

想象一下怎么去实现呢？也许你猜到了，使用回调，在我们打印完消息以后，我们去执行一个JS的回调，注意这个回调是JS的回调不是OC的回调。

我们修改一下我们的HTML文件，增加一个回调方法：

```html
<!DOCTYPE html>
<html>
    <head>
	    <meta charset="utf-8">
        <title>test javascript</title>
        <script type="text/javascript">
            function callBack(a) {
                alert(a);
            }
        </script>
    </head>
    <body>
        <div>
        		<!--为什么使用ttf.调用，下面会讲到-->
            <button onclick="ttf.nslog('JS与OC交互', 'callBack');">点击我然后看xcode的log</button> 
        </div>
    </body>
</html>
```

我们还需要更改一下我们的自定义类，因为我们这个自定义类要在回调方法中要操控webView对象执行JS的回调方法，所以我们自定义类要包含一个webView对象

```objc
@protocol PersonJSExport <JSExport>
JSExportAs(nslog,
           - (void)printWithString:(NSString *)string callback:(NSString *)callback
           );
@end

@interface MyJSObject() <PersonJSExport>
@property(strong, nonatomic) UIWebView *webView;//我们需要拿到webView
@end

@implementation MyJSObject

- (instancetype)initWithWebView:(UIWebView *)webView {
    if (self = [super init]) {
        _webView = webView;
    }
    return self;
}

- (void)printWithString:(NSString *)string callback:(NSString *)callback {
    NSLog(@"%@", string);
    
    NSString *callbackJS = [NSString stringWithFormat:@"%@('我是callback')", callback];
    //1秒钟以后执行JS的回调方法
    [self.webView performSelector:@selector(stringByEvaluatingJavaScriptFromString:) withObject:callbackJS afterDelay:1];
}
```

注意UIWebView执行JS代码使用的是``stringByEvaluatingJavaScriptFromString ``方法，而JavaScriptCore执行JS代码使用的是``- (JSValue *)evaluateScript:(NSString *)script;``方法。

这样子我们就实现了JS和OC相互调用，没什么难度的，都说了以最简单的例子说明问题，我本身也很厌烦一个简单的问题非要搞很复杂的代码去讲解。🤓

通过这篇文章的介绍我想大家应该对``Hybrid App``有所了解了，能掌握``JavaScriptCore``的一些应用了，希望我这么多字没有白敲，建议大家参考Demo理解一下文章，下载demo请点击[这里](http://7xt9yk.com2.z0.glb.clouddn.com/javascriptcore%E6%B5%8B%E8%AF%95.zip?attname=&e=1461349910&token=r8Cu9SE4ohukkkaqqjPNiOgKcfgiep821jba2HrY:D8IvDZnnsuOFjt378pcTH0EIYTY)

demo中请使用``git reflog``查看git日志，然后``git reset``到不同的提交点是文章中不同知识点的代码。代码可能与文章中有所出入，不过大致一样。

这篇文章就到这里了，感谢大家耐心的看下去，能有这耐心我相信你一定是超人，还请各位超人多多关注一下我的博客哦😁