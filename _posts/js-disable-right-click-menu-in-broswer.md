---
title: 禁用浏览器鼠标右键
date: 2013-10-14 11:11:45
description: 禁用浏览器鼠标右键
categories: 
- js
tags:
- js
---

代码：
```js
window.onload = function(){
	document.oncontentmenu = function(e){
		if(e.preventDefault){
			e.preventDefault();
		}else{
			e.returnValue = false;
		}
	};
};
```
preventDefault是火狐
returnVal=false是IE浏览器的
