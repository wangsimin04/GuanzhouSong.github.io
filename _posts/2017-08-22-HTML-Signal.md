---
layout: post
title:  HTML中常见的符号
date:   2017-08-23 00:00:00 +0800
categories: 不周山
tag: HTML
---


* content
{:toc}

### 上标
上标的HTML标签的是`<sup>`，所以如果要打上标的话就用以下格式：

`<sup>xxx</sup>`

其中xxx表示上标的内容，看个例子：

我现在想写一个公式：n的平方等于n+1，写法如下：

`n<sup>2</sup>=n+1`

显示效果就是：

n<sup>2</sup>=n+1

### 下标

下标的标签是<sub>，同理我们来实现一个例子：a=log<sub>2</sub>b
写法如下：

`a=log<sub>2</sub>b`

### 注册商标的符号：

立白®

`立白&reg;`

### function符号:

ƒ可以轻松得打出函数式：ƒ(x)=x+1

`&fnof;(x)=x+1`

### 根号

不过这个根号不完美，少了上面一横，更像对勾：√5

`&radic;5`

###角度符号：

30°

`30&deg;`

### 空格
最后说一下每段前面的两个空格怎么打，因为在markdown里直接打空格的话是不行的，直接按键盘的空格，三个以内都没有效果，从第四个开始就变成了代码片段了，那么要怎么愉快得打空格呢？
答案是用HTML的

`&nbsp;`

### 常见符号

下图是HTML代码中一些常见的符号

![](/images/HTML_Signal.png)

转载自作者：这是朕的江山
