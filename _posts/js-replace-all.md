---
title: 实现replaceAll
date: 2016-05-23 16:54:36
description: 通过replace实现js的replaceAll方法
categories: 
- js
tags:
- js
---


js中提供了replace方法在字符串中用一些字符替换另一些字符，或替换一个与正则表达式匹配的子串。

```js
stringObject.replace(regexp/substr,replacement)

```
> regexp/substr 为子字符串或正则表达式


通过使用正则表达式来达到replaceAll的效果：

```js

String.prototype.replaceAll = function(AFindText,ARepText){
   var raRegExp = new RegExp(AFindText.replace(/([\(\)\[\]\{\}\^\$\+\-\*\?\.\"\'\|\/\\])/g,"\\$1"),"ig");
   return this.replace(raRegExp,ARepText);
}

```
