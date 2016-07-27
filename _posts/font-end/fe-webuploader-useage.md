---
title: 百度webuploader使用
date: 2015-12-04 18:24:33
description: 百度webuploader使用
categories: 
- webuploader
tags:
- webuploader
---

	使用版本：0.1.6

## 初始化
在初始化时可通过`WebUploader.create({...})`的形式创建，如：
```js
var uploader = null, 
uploader = WebUploader.create({
    // 文件接收服务端。
    server: "https://****.do"
    // 选择文件的按钮。可选。
    // 内部根据当前运行是创建，可能是input元素，也可能是flash.
    //, pick: '#fileSelectBtn'
    , pick: {
        id: "#fileSelectBtn"  // 指定选择文件的按钮容器，不指定则不创建按钮
        //, innerHTML: "按钮1"
        , button: '<div style="width: 112px; height: 30px;"></div>'
        , style: '' 
        , multiple: false // 是否开起同时选择多个文件能力
    }
    , threads: 2  // [可选] [默认值：3] 上传并发数。允许同时最大上传进程数。
    , fileNumLimit: 2  // [可选] [默认值：undefined] 验证文件总数量, 超出则不允许加入队列。
    , duplicate: false  // {Boolean} true：可以重复，false：不允许重复 [可选] [默认值：undefined] 去重， 根据文件名字、文件大小和最后修改时间来生成hash Key.
    , auto: true
});

```

如果需要添加多个按钮，可通过`uploader.addButton({...});`，如：
```js
uploader.addButton({
    id: '#fileSelectBtn02'
    , button: '<div style="width: 112px; height: 30px;"></div>'
    , style: '' 
    , multiple: false    
});

```

## 事件

### 文件被加入队列之前
当文件被加入队列之前会触发`beforeFileQueued`事件：
```js
uploader.on("beforeFileQueued", function(file){
    console.log(file);
    return true;
});

```
回调中的参数file为上传的文件对象，可以通过这个file对象检查该文件是否合法。如果该回调返回值为false，则此文件不会被添加进入队列。

### 一批文件添加进队列以后
当一批文件添加进队列以后会触发`filesQueued`事件：
```js
uploader.on("filesQueued", function(file){
    //console.log(file.length);
});

```

### 当有文件添加到队列时
当有文件添加进来的时候会触发`fileQueued`事件：
```js
uploader.on( "fileQueued", function( file ) {
    // 获取文件信息，构建界面
});

```
可以在该回调中根据file的信息构建界面。


### 文件被移除队列
当文件被移除队列后触发`fileDequeued`事件：
```js
uploader.on("fileDequeued", function(file){
    console.log("file "+file.name+" is removed.");
});

```

### 文件上传过程中进度
文件上传过程中创建进度事件：
```js
uploader.on( "uploadProgress", function( file, percentage ) {
    var item = $("#" + file.id),
        msg = item.find(".uploader-msg"),
        percent = item.find(".uploader-progress-bar");
    var val = parseInt(percentage * 100);
    var per = val + "%";
    msg.text(" - " + per);
    percent.css("background", "#1daeeb");
    percent.css( "width", per );
});

```
	file: File对象
	percentage: 上传进度，数值

### 文件上传成功
当文件上传成功时会触发`uploadSuccess`事件：
```js
uploader.on( "uploadSuccess", function( file, response ) {
    console.log(file);
});

```
	response: 服务端返回的数据，Object对象

### 处理后端返回错误信息
当后台校验文件后，需要将特定的信息传到前台并显示，需要结合`uploadAccept`和`uploadError`两个事件一起用：
```js
uploader.on("uploadAccept", function(obj, response, fn){
    if(response.error != undefined ){
        if(response.error){ //只有失败的时候才将信息往下传
            fn(response);
        }
    }
});

uploader.on( "uploadError", function( file, reason  ) {
    if(reason.error != undefined ){
        if(reason.error){
            // 上传失败
            // 处理逻辑
        }
    }
});

```
当某个文件上传到服务端响应后，会派送`uploadAccept`事件来询问服务端响应是否有效。`response`为服务端的返回数据，json格式，如果服务端不是json格式，从ret._raw中取数据，自行解析。`fn`为回调，定义见webuploader.js中4038，在4043行触发`uploadAccept`事件时，将`fn`传到了自己定义的`uploader.on("uploadAccept", function(..., fn){})`。


### error事件
`error`事件只是前台校验失败的显示信息。
```js
uploader.on("error", function(type){
    console.log("error");
    console.log(type);
    if(type == 'Q_EXCEED_NUM_LIMIT'){
        top.showMsg(3, '文件上传数超出限制！');
    }else if (type == 'Q_EXCEED_SIZE_LIMIT'){
        top.showMsg(3, '文件大小超出限制！');
    }else if (type == 'Q_TYPE_DENIED'){
        top.showMsg(3, '非法文件类型！');
    }else if (type == 'F_DUPLICATE'){
        top.showMsg(3, '文件已存在！');
    }
});

```

## 参考
WebUploader：http://fex.baidu.com/webuploader/doc/index.html#WebUploader_Uploader
