title: muti-text.md
toc: false
date: 2016-09-29 15:38:28
tags:
  - muti-text
categories:
  - css
---

多行文本处理
<!-- more -->

# 超出隐藏

## 超出换行
```css
display:block;/*内联对象需加*/
width:31em;
word-break:keep-all;/* 不换行 */
white-space:nowrap;/* 不换行 */
overflow:hidden;/* 内容超出宽度时隐藏超出部分的内容 */
text-overflow:ellipsis;/* 当对象内文本溢出时显示省略标记(...) ；需与overflow:hidden;一起使用。*/
```

## 限制N行
```css
max-height: 115px;
display: -webkit-box;
-webkit-line-clamp: 3;//行数
-webkit-box-orient: vertical;
text-overflow: -o-ellipsis-lastline;
overflow: hidden;
text-overflow: ellipsis;
```
