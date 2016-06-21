---
title: 鼠标移动到表格行变色
date: 2013-05-12 13:24:33
description: 鼠标移动到表格行变色
categories: 
- js
tags:
- js
---

首先需要定义改变前后的颜色：
```css
.over { background-color : #EFEFEF;color:#FFFFFF;}
.out { background-color: #ffffff; color: #000000;}
```

在表格中的<tr>标签中添加`onMouseOver`和`onMouseOut`，如：
```html
<tr onMouseOver="this.className='over'" onMouseOut="this.className='out'" >...</tr>
```
