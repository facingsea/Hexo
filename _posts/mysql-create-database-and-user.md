---
title: mysql创建库及分配用户
date: 2015-11-24 13:24:33
description: mysql创建库及分配用户
categories: 
- mysql
tags:
- mysql
---

## 创建数据库
```sql
CREATE DATABASE /*!32312 IF NOT EXISTS*/`demo_db` /*!40100 DEFAULT CHARACTER SET utf8 */;
```

## 创建用户
```sql
CREATE USER 'user_name'@'localhost' IDENTIFIED BY 'password';
```

## 分配对应库的权限
```sql
grant all privileges on demo_db.* to 'user_name'@'%' identified by 'password';
```

此时，权限已经分配完成，查询`mysql.user`表来查看分配情况：
```sql
mysql> select User, Host, Password from mysql.user;
+----------------+-----------+--------------------+
| User           | Host      | Password           |
+----------------+-----------+--------------------+
| root           | localhost | *abcd              |
| root           | 127.0.0.1 | *abcd              |
|                | localhost |                    |
| user_name      | localhost | *abcd              |
| user_name      | %         | *abcd              |
+----------------+-----------+--------------------+
```
这里给用户分配了全部权限，具体权限可查询`mysql.user`表。

