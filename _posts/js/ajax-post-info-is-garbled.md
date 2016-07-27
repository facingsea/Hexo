---
title: ajax的post请求出现乱码问题
date: 2013-04-30 12:36:20
description: ajax的post请求出现乱码问题
categories: 
- ajax
tags:
- ajax
---

如ajax请求如下：
```js
$.post("${pageContext.request.contextPath}/DeleteDocServlet?docid=${map.docid}&docType=${map.docType}", data, callback);
```
在服务器端，通过
```java
request.getParamerter("docType");
```
将获取乱码，考虑如果将获得的值进行`new String()`的方式进行解码：
```java 
new String(request.getParamerter("docType").getBytes("ISO8859-1"),"UTF-8")
```
但是这种形式取得的值仍然是乱码。

解决方法：

在Ajax中通过对中文参数进行两次URI编码，然后在服务器端通过getParameter方式获得，并对获得的值进行URI解码，这样便可以解决乱码问题，代码如下：
```js
//对中文进行编码
var name = encodeURI(encodeURI("${docType}" ));
$.post( "${pageContext.request.contextPath}/DeleteDocServlet?docid=${map.docid}&docType=" +name+"");
```
服务器端：
```java
String docType = request.getParameter ("docType" );
docType = URLDecoder.decode(docType, "UTF-8");
```
这样在服务器端即可获取中文数据。
