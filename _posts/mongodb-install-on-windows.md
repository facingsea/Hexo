---
title: Windows下安装Mongodb
date: 2016-06-25 14:11:20
description: Windows下安装Mongodb
categories: 
- mongodb
tags:
- mongodb
---

## 安装
### 下载
MongoDB提供了.msi和.zip两种社区版下载，参见`https://www.mongodb.org/dl/win32/x86_64-2008plus-ssl?_ga=1.76096855.312951906.1466825504`，这里我下载了.zip的格式。
### 安装
.zip形式直接解压到要安装的目录，如`D:\dev\mongodb`，目录结构为：
```
-- mongodb/
--     bin/
--         *.exe
--         *.pdb
--     GNU-AGPL-3.0
--     MPL-2
--     README
--     THIRD-PARTY-NOTICES

```
`bin/`下对应文件介绍：
| Component Set                     | Binaries                                                              | 
| --------------------------------- |:---------------------------------------------------------------------:|
| Server（服务）                    | mongod.exe                                                            |
| Router（路由）                    | mongos.exe                                                            |
| Client（客户端）                  | mongo.exe                                                             |
| MonitoringTools（监控工具）       | mongostat.exe, mongotop.exe                                           |
| ImportExportTools（导入导出工具） | mongodump.exe, mongorestore.exe, mongoexport.exe, mongoimport.exe     |
| MiscellaneousTools（辅助工具）    | bsondump.exe, mongofiles.exe, mongooplog.exe, mongoperf.exe           |

## 启动

### 环境构建
MongoDB需要一个目录去存放数据，默认的目录是`\data\db`，该目录需要手动创建，如目录全路径为`D:\dev\mongodb\data\db`，启动命令：
```bash
D:\dev\mongodb\bin\mongod.exe --dbpath D:\dev\mongodb\data\db
```
如果路径中包含空格，需要用双引号括起来：
```bash
D:\dev\mongodb\bin\mongod.exe --dbpath "D:\dev\mongodb\data db"
```
### 开启MongoDB服务
```bash
D:\dev\mongodb\bin\mongod.exe

```

### 客户端连接
要连接MongoDB服务，需要执行`mongo.exe`命令，另外打开一个cmd，输入：
```bash
D:\dev\mongodb\bin\mongo.exe
```
控制台输出：
```bash
MongoDB shell version: 3.2.7-44-ge9746ac
connecting to: test
Welcome to the MongoDB shell.
For interactive help, type "help".
For more comprehensive documentation, see
        http://docs.mongodb.org/
Questions? Try the support group
        http://groups.google.com/group/mongodb-user
>
```
表示连接成功。
如停止连接，可按`Control+C`。

## 配置windows服务
加入windows服务后，可以开机启动，很方便。
1、创建db和log两个目录，如：
```
D:\dev\mongodb\data\db
D:\dev\mongodb\data\log
```
2、创建配置文件
我们可以创建一个配置文件，该文件必须包含`systemLog.path`，其他可选配置参见：https://docs.mongodb.com/manual/reference/configuration-options/。如文件路径为：
```
D:\dev\mongodb\mongod.cfg
```
内容为：
```
systemLog:
    destination: file
    path: D:\dev\mongodb\data\log\mongod.log
storage:
    dbPath: D:\dev\mongodb\data\db
```
3、注册为服务
> 以下命令需要以管理员身份打开cmd！
命令：
```bash
D:\dev\mongodb\bin>mongod.exe --config D:\dev\mongodb\mongod.cfg --install
```

4、启动服务
现在启动MongoDB服务命令可以使用windows方式：
```bash
net start MongoDB
```
5、停止服务
```bash
net stop MongoDB
```
如果要移除该服务，可执行：
```bash
D:\dev\mongodb\bin>mongod.exe --remove
```

## 所遇问题
### 系统丢失dll文件
启动时，系统报缺失`MSVCP140.dll`和`VCRUNTIME140.dll`文件
方案一：下载对应dll文件
方案二：更换老一点的版本

## 参考
1、MongoDB: https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/