---
title: 针对SPFK主题修改记录
date: 2016-05-08 22:01:21
description: 主题修改记录
categories: 
- theme
tags:
- theme
- spfk
---

## 左侧社交信息展示
默认情况下，最后一个图标有一个margin-right的设置，设置在： ..source/css/_partial/main.styl中
```less
a:last-of-type {
    margin-right:0
}
```
将该设置去掉即可。

## 标签
标签中的部分字体显示会向下偏一些，设置在：source/css/_partial/tagcloud.styl中
```less
.tagcloud{
    a{
        display:inline-block;  
        text-decoration:none;  
        font-weight: normal;
        font-size:10px;  
        color:#fff;   
        height:18px;  
        line-height:18px;  
        float: left;
        padding:0 5px 0px 10px;  
        position:relative;
		...
	}
}  
```
将line-height属性值设置为16px，效果稍好一些。

## TODO
> 鼠标移动到左侧的icon上显示tip-box时，很大情况下会出现鼠标还没移动到tip-box上就消失的问题。

