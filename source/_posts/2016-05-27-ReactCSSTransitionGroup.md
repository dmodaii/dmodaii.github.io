title: ReactCSSTransitionGroup
toc: false
date: 2016-05-27 19:28:43
tags:
  - react
categories: 前端开发
-
---

React provides a ReactTransitionGroup add-on component as a low-level API for animation, and a ReactCSSTransitionGroup for easily implementing basic CSS animations and transitions.

<!--more-->
![](ReactCSSTransitionGroup/286966.jpg)

本文相关链接
https://facebook.github.io/react/docs/animation.html

# ReactCSSTransitionGroup

```javascript
<ReactCSSTransitionGroup transitionName="example" transitionEnterTimeout={500} transitionLeaveTimeout={300}>
  {items}
</ReactCSSTransitionGroup>

<style type="text/css">
.example-enter {
  opacity: 0.01;
}

.example-enter.example-enter-active {
  opacity: 1;
  transition: opacity 500ms ease-in;
}

.example-leave {
  opacity: 1;
}

.example-leave.example-leave-active {
  opacity: 0.01;
  transition: opacity 300ms ease-in;
}
</style>
```
如果使用模块化的css则不能使用指定类, 采用transitionName={style}, style为import引入的对应sass/less，其中必须有以下class

```css
.appear {
  opacity: .01;
}

.appear.appearActive {
  opacity: 1;
  transition: all 3000ms ease;
}

.enter {
  opacity: .01;
  left: 0;
}

.enter.enterActive {
  opacity: 1;
  left: 3rem;
  transition: all 300ms ease;
}

.leave {
  opacity: 1;
}

.leave.leaveActive {
  opacity: .01;
  transition: opacity 300ms ease-in;
}
```
### appear
ReactCSSTransitionGroup provides the optional prop transitionAppear, to add an extra transition phase at the initial mount of the component
在挂载该ReactCSSTransitionGroup时候触发， 子元素添加和删除分贝对应enter 和leave

# Custom Classes

```javascript
<ReactCSSTransitionGroup
  transitionName={ {
    enter: 'enter',
    enterActive: 'enterActive',
    leave: 'leave',
    leaveActive: 'leaveActive',
    appear: 'appear',
    appearActive: 'appearActive'
  } }>
  {item}
</ReactCSSTransitionGroup>

<ReactCSSTransitionGroup
  transitionName={ {
    enter: 'enter',
    leave: 'leave',
    appear: 'appear'
  } }>
  {item2}
</ReactCSSTransitionGroup>
```
