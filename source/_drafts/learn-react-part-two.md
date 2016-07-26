title: 走进react第二部分
toc: true
date: 2016-04-08 18:48:44
tags: ReactJS
categories: Web前端
---

# 组件的生命周期
既然咱们知道了组件，那么咱们就得知道这个组件是怎么渲染的，他是怎么活的，怎么死，方便我们在他获得时间做某些事，在他死之前做某些事。

组件从创建到渲染出来经历的函数

1. getDefaultProps		//获取默认属性的方法 
2. getInitialState		//获取默认的状态
3. componentWillMount	//组件将要被渲染，我们可以做一些事
4. render					//组件渲染
5. componentDidMount	//组件渲染完成

组件存在期，随着应用状态的改变

1. componentWillReceiveProps	//组件将要接收属性
2. shouldComponentUpdate		//组件需要更新
3. componentWillUpdate			//组件将要更新
4. render							//渲染
5. componentDidUpdate			//组件更新完毕

组件销毁调用方法

1. componentWillUnmount			//组件被取消

代码点击这里[demo](https://github.com/CoderTian/LearnReact/blob/master/ComponentLifecycle.html)

骚年们可以运行一下，打开chrome的开发者工具，点击``console``菜单，就可以看到函数的调用顺序了，别光让我动手，你也动手试一下。

对于组件来说``getDefaultProps``这个方法只会被调用一次，无论这个组件使用多少次，只会被调用一次。

``getInitialState``这个方法每次使用这个组件的时候都会被调用一次，而且只会被调用一次。
