tags: ReactJS
---

点进来这篇文章，我相信你一定是一个爱学习，爱劳动，爱技术的一等好骚年，React可以说是一门新技术，在React方面我也是菜鸟一个，那么就让我们一起走进React的世界，学习她，拥抱她，亲吻她吧，let's go!走起

<!--more-->

> 我保证这里面的例子都是最简单的，我精简了再精简，只为博君一笑：so easy!


## 关于React
``React``本来是``facebook``内部的一个JavaScript类库，在2013年开源了。``React``是用于创建web用户交互界面的库，很多人都把他当做``MVC``框架的V。想当初我们开发前端页面，数据改变了我们要去手动更新``DOM``，数据更改的多，我们DOM操纵的也多。就问你一句“烦不烦？”。直到遇见了``React``，那么憋屈的日子一去不复返了。

## 虚拟DOM
既然不用手动更改``DOM``，那么``React``是怎么操纵的呢？React有一个虚拟DOM，当数据变化React会更改虚拟DOM树，并计算与真实显示的DOM的不同，然后以最小的DOM更改来更新整个应用程序。

## 组件化
``React``核心就是组件（component）,把原来的页面看成很多组件，一个组件一个组件的实现，这些组件的组合就能组成一个界面了。就好比我们穿的一件一件的衣服一样，没有穿衣服就是光光的😬

骚年别怕，也许你刚开始不了解，不过等你写了几个小demo的时候，这么概念就会理解了。

## Hello World
老规矩，咱们先使用React实现一个Hello World！

```javascript
<body>
  <div id="test"></div>
  <script type="text/babel">
    ReactDOM.render(	
      <h1>你好，世界!</h1>,	//第一个参数传进入要显示的数据
      document.getElementById('test') //第二个参数是把上面要显示的数据添加到test节点上面
    );
  </script>
</body>
```

显示Hello World是不是很简单，看清楚了script的type为``text/babel``，不是我们平常的``text/script``。

代码点击这里[demo](https://github.com/CoderTian/LearnReact/blob/master/HelloWorld.html)

## JSX
学习React你一定要知道JSX，JSX就是``JavaScript XML``一种在React组件内部构建标签类的XML语法,也就是看起来像是HTML标签那种，但是我们也可以不用JSX，但是人家官方推荐我们用，所以我们也最好使用JSX语法。组件显示的时候会把JSX格式的组件转换为常规的JavaScript格式，然后显示。所以使用与不使用JSX，最后都会转为常规的JavaScript

**使用JSX**  
``<a href="https://facebook.github.io/react/">Hello!</a>``

**不使用JSX**
``React.createElement('a', {href: 'https://facebook.github.io/react/'}, 'Hello!')``

看到这样子的对比我还是默默的使用``jsx``吧，所以本骚年我也建议大家使用JSX语法。

## 组件
都说了React的核心就是组件，我们使用各种组件来创建一个页面，下面就是一个创建组件的例子

```javascript
<body>
  <div id="test"></div>
  <script type="text/babel">
  	//创建组件
    var TestComponent = React.createClass({
      render: function() {	//渲染函数，就是返回这个组件包含的内容
        return ( <h1>还是你好，世界!!!</h1> );
      }
    });

  ReactDOM.render(
    <TestComponent />,
    document.getElementById('test')
  );
  </script>
</body>
```
``TestComponent ``就是我们创建的一个组件，一定要记住这个是大写字母开头的，一定要记住，我们使用``React.createClass``函数创建组件，组件中可以包含想要返回的html内容。
代码点击这里[demo](https://github.com/CoderTian/LearnReact/blob/master/TestComponent.html)

## state
组件就相当于一个整体，这个整体的内部肯定要有自己的数据吧，``state``是组件内部的存储数据的地方，这个state只存在组件内部，数据也只是供内部使用的。千言万语不如代码一句话。看代码🤒
初始化组件的state要在函数``getInitialState``中，初始化

```javascript
var TestComponent = React.createClass({
	//初始化state
	getInitialState: function() {
	  return {
	    showElem: true,		//这是两个属性
	    myHobby: '编程'
	  };
	},
		
	//渲染函数
	render: function() {
	  var hobby;
	  if(this.state.showElem) {
	    hobby = this.state.myHobby;
	  }
		
		return (
		  <p>{hobby}</p>
		);
	}
});
```

我们首先创建一个``TestComponent``组件，然后在他的``getInitialState``方法中初始化我们的``state``，然后在render函数中，判断是否显示``myHobby``

代码点击这里[demo](https://github.com/CoderTian/LearnReact/blob/master/State.html)

## props
props与state正好相反，props是一个组件的事件源，也可以看成组件的``外交官``，父组件可以把子组件需要显示的数据传递给子组件的props，子组件就可以根据他的props来显示东西。props是只读的，一定要把它当成只读的，props在组件内部是不能改变的。  
state可以在内部调用``this.setState``来改变state的值，但是千万不要在内部调用``this.setProps``去改变props的值，我再说一遍，props是只读的。

```javascript
var TestComponent = React.createClass({
  //设置一下属性的默认值，如果父组件不传递属性，就显示默认的值
  getDefaultProps: function() {
    return {
      hobby: '玩游戏'
    };
  },

  render: function() {
    return ( <h1>{this.props.hobby}</h1> ) //显示属性
  }
});

ReactDOM.render(
  <TestComponent hobby="编程"/>,  //传递属性
  document.getElementById('test')
);
```
可以看到外面传递一些数据给hobby，组件会根据传给hobby的数据显示东西，那么这个hobby就是我们设定的``TestComponent``的props。

## PropTypes
``PropTypes``是为了表明要给这个属性传什么格式的数据，这个东西并不是强制性的，如果传给属性的数据和设置的属性格式不一样会出现警告，我们在原来html文件中添加下面代码

```javascript
propTypes: {
	hobby: React.PropTypes.string
},
```
关于格式都有哪些可以参考官网http://facebook.github.io/react/docs/reusable-components.html

> 总结：  
> state存储视图的状态，修改state使用this.setState，state内在使用  
> props是只读属性，不要通过任何方式修改这个props，props是外面传递数据的入口   

骚年想看代码点击这里[demo](https://github.com/CoderTian/LearnReact/blob/master/Props.html)

## 事件处理
``React``也支持事件，像点击事件``onClick``,鼠标事件``onMouseOver``,``onMouseOut``等等事件。这里面的第二个单词是大写字母开头，骚年们千千万万不要搞错了。其他事件到时候可以参考一下官网。

下面咱们就实现一个动态的UI，看下效果

![](learn-react-part-one/dynamicUI.gif)

看一下这个代码怎么实现的

```
	getInitialState: function() {
	  return {
	    showElem: false //我们通过改变这个值来控制子视图的显示与不显示
	  };
	},
		
	//处理点击事件
	handleClick: function() {
	    this.setState( {showElem: !this.state.showElem} );
	},
		
	render: function () {
	  var elems;
	  if(this.state.showElem) {
		.............
	  }
		
	  return (
	    <div>
	      <button onClick={this.handleClick}>点击我</button>   //绑定onClick事件
	      <div> {elems} </div>
	    </div>
	  );
	}
});

```

通过点击事件，我们改变了state的值，前面说过，我们可以改变state的值，但是不能更改props的值，改变了state的值，那么虚拟DOM就会改变，就会重新更改真实显示的DOM。

骚年点击[我](https://github.com/CoderTian/LearnReact/blob/master/EventTest.html)看代码

## 组件复合
看到这里，是不是感觉一个组件玩来玩去很不爽，那么咱们再多来几个组件，看看组件之间是怎么组合使用的。

```javascript
//创建第三个组件，为第一个组件和第二个组件的复合
var ComponentThree = React.createClass({
  render: function() {
    return (
      <div>
        <ComponentOne />
        <ComponentTwo />
      </div>
    )
  }
});
```

我们创建了``ComponentOne ``和``ComponentTwo ``组件，在``ComponentThree ``组件中将他们复合到一起
是不是是不是简单？现在``ComponentThree ``就是前面两个组件的父组件。

代码参考这里[demo](https://github.com/CoderTian/LearnReact/blob/master/ComponentMultiple.html)

到了这里你可能会认为react很简单嘛，其实我这只是带大家简单入个门，等大家对这些基础概念都了解了以后，对于一些深层的研究不那么陌生了。

好了骚年们，为了文章篇幅不是太长，这篇文章就到此为止了。放心我还会继续更新的。请大家持续关注哦！！！