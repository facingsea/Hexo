---
title: MongoDB 3.x 创建用户
date: 2016-06-26 17:20:20
description: MongoDB 3.x 创建用户
categories: 
- mongodb
tags:
- mongodb
---

> 请先安装好MongoDB 3

## 启动服务
不用`--auth`参数启动MongoDB服务：
```bash
D:\dev\mongodb\bin\mongod.exe --dbpath D:\dev\mongodb\data\db
```
## 客户端连接
使用客户端连接：
```bash
D:\dev\mongodb\bin\mongo.exe
```
执行`show dbs`后，会发现只有一个`local`库

## 创建管理员用户
添加一个具有`userAdminAnyDatabase`角色的用户，把他作为数据库的管理员。如：
```bash
use admin

db.createUser({
    user: "root", 
    pwd: "1234", 
    roles: [{role: "userAdminAnyDatabase", db: "admin"}]
})
```
以上创建了用户root，密码为1234，操作的数据库为admin。

> `roles`中的`db`属性用于指定该用户所要操作的数据库，必须有，不然会报错。

此时可以通过`show users`（必须在admin数据库下）或`db.system.users.find()`来查看新建的用户：
```bash
> db.system.users.find()
{ "_id" : "admin.root", "user" : "root", "db" : "admin", "credentials" : { "SCRAM-SHA-1" : { "iterationCount" : 10000, "salt" : "veRFyPTSy7YeTjgJ0II5cw==", "storedKey" : "L8w3oCwXAf/0fBtYDzFd90w0h7o=", "serverKey" : "w+D2G7InpC+feDCKCFfHRFRgw+g=" } }, "roles" : [ { "role" : "userAdminAnyDatabase", "db" : "admin" } ] }
> show users
{
    "_id" : "admin.root",
    "user" : "root",
    "db" : "admin",
    "roles" : [
        {
            "role" : "userAdminAnyDatabase",
            "db" : "admin"
        }
    ]
}
```
其他角色参见：[Built-In Roles](https://docs.mongodb.com/manual/core/security-built-in-roles/)

## 以认证方式重启
添加`--auth`参数启动`mongod`服务：
```bash
D:\dev\mongodb\bin\mongod.exe --auth --dbpath D:\dev\mongodb\data\db
```

## 管理员登录
客户端有两种登录方式：
1、按之前的方式直接登录
```bash
D:\dev\mongodb\bin>mongo.exe
MongoDB shell version: 3.2.7-44-ge9746ac
connecting to: test
> show dbs
2016-06-26T17:08:23.299+0800 E QUERY    [thread1] Error: listDatabases failed:{
        "ok" : 0,
        "errmsg" : "not authorized on admin to execute command { listDatabases: 1.0 }",
        "code" : 13
} :
_getErrorWithCode@src/mongo/shell/utils.js:25:13
Mongo.prototype.getDBs@src/mongo/shell/mongo.js:62:1
shellHelper.show@src/mongo/shell/utils.js:761:19
shellHelper@src/mongo/shell/utils.js:651:15
@(shellhelp2):1:1
```
可以发现，现在能够进入数据库，但是无法执行其他操作，这时就需要使用`db.auth(name, pwd)`来认证：
```bash
> db.auth("root", "1234")
1
```
返回`1`表示成功。

2、通过添加`-u <username>`、`-p <password>`、`--authenticationDatabase <database>`启动参数直接认证：
```bash
mongo.exe --port 27017 -u "root" -p "1234" --authenticationDatabase "admin"
```

当通过以上两种方式登录后，发现操作还是报错，这是正常的，因为`userAdminAnyDatabase`角色只是用来管理用户的，并没有操作数据库的权限。

## 创建其他用户
创建用户需要以下权限：

    * 对于目标数据库，需要有`createUser`的权限
    * 需要有`grantRole`权限，以便给用户分配角色

MongoDB中自带的`userAdmin`和`userAdminAnyDatabase`两个角色拥有`createUser`和`grantRole`权限，所以之前添加的root用户可以创建用户。
如，给数据库**script**创建用户**script_user**，并且该用户对数据库**report**有read权限：
```bash
use script
db.createUser({
    user: "script_user", 
    pwd: "1234", 
    roles: [
        {role: "readWrite", db: "script"}, 
        {role: "read", db: "report"}
    ]
})
```
查看用户：
```json
> show users
{
    "_id" : "script.script_user",
    "user" : "script_user",
    "db" : "script",
    "roles" : [
        {
            "role" : "readWrite",
            "db" : "script"
        }
    ]
}
```
切换为`script`数据库及使用`script_user`用户：
```bash
> db.auth("script_user", "1234")
> use script
switched to db script
```
此时，用户`script_user`就可以操作`script`数据库了。


## 参考
1、Add Users：https://docs.mongodb.com/manual/tutorial/create-users/
1、Enable Client Access Control：https://docs.mongodb.com/manual/tutorial/enable-authentication/


