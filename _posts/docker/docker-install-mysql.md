---
title: docker安装mysql
date: 2015-08-13 23:00:20
description: docker安装mysql
categories: 
- docker
tags:
- docker
- mysql
---

## 下载mysql
如下载版本5.2.26：
```bash
wget -c  http://cdn.mysql.com/Downloads/MySQL-5.6/mysql-5.6.26-linux-glibc2.5-x86_64.tar.gz
```
-c 表示下载中断可以续传

## 解压
一般情况下，将mysql安装到/usr/local/mysql下：
```bash
tar -zxf Mysql-*.tar.gz 
mv Mysql-* /usr/local/mysql 
```

## 安装
由于是在docker环境下，没有必要再为mysql创建用户及用户组，直接用root即可
执行命令：
```bash
/usr/local/mysql/scripts/mysql_install_db --user=root 
```
输出信息为：
```bash
Installing MySQL system tables...2015-08-13 14:47:13 0 [Warning] TIMESTAMP with implicit DEFAULT value is deprecated. Please use --explicit_defaults_for_timestamp server option (see documentation for more details).
2015-08-13 14:47:13 0 [Note] ./bin/mysqld (mysqld 5.6.26) starting as process 114 ...
2015-08-13 14:47:13 114 [Note] InnoDB: Using atomics to ref count buffer pool pages
2015-08-13 14:47:13 114 [Note] InnoDB: The InnoDB memory heap is disabled
2015-08-13 14:47:13 114 [Note] InnoDB: Mutexes and rw_locks use GCC atomic builtins
2015-08-13 14:47:13 114 [Note] InnoDB: Memory barrier is not used
2015-08-13 14:47:13 114 [Note] InnoDB: Compressed tables use zlib 1.2.3
2015-08-13 14:47:13 114 [Note] InnoDB: Using Linux native AIO
2015-08-13 14:47:13 114 [Note] InnoDB: Using CPU crc32 instructions
2015-08-13 14:47:13 114 [Note] InnoDB: Initializing buffer pool, size = 128.0M
2015-08-13 14:47:13 114 [Note] InnoDB: Completed initialization of buffer pool
2015-08-13 14:47:13 114 [Note] InnoDB: The first specified data file ./ibdata1 did not exist: a new database to be created!
2015-08-13 14:47:13 114 [Note] InnoDB: Setting file ./ibdata1 size to 12 MB
2015-08-13 14:47:13 114 [Note] InnoDB: Database physically writes the file full: wait...
2015-08-13 14:47:14 114 [Note] InnoDB: Setting log file ./ib_logfile101 size to 48 MB
2015-08-13 14:47:18 114 [Note] InnoDB: Setting log file ./ib_logfile1 size to 48 MB
2015-08-13 14:47:20 114 [Note] InnoDB: Renaming log file ./ib_logfile101 to ./ib_logfile0
2015-08-13 14:47:20 114 [Warning] InnoDB: New log files created, LSN=45781
2015-08-13 14:47:20 114 [Note] InnoDB: Doublewrite buffer not found: creating new
2015-08-13 14:47:21 114 [Note] InnoDB: Doublewrite buffer created
2015-08-13 14:47:21 114 [Note] InnoDB: 128 rollback segment(s) are active.
2015-08-13 14:47:21 114 [Warning] InnoDB: Creating foreign key constraint system tables.
2015-08-13 14:47:21 114 [Note] InnoDB: Foreign key constraint system tables created
2015-08-13 14:47:21 114 [Note] InnoDB: Creating tablespace and datafile system tables.
2015-08-13 14:47:21 114 [Note] InnoDB: Tablespace and datafile system tables created.
2015-08-13 14:47:21 114 [Note] InnoDB: Waiting for purge to start
2015-08-13 14:47:21 114 [Note] InnoDB: 5.6.26 started; log sequence number 0
2015-08-13 14:47:28 114 [Note] Binlog end
2015-08-13 14:47:28 114 [Note] InnoDB: FTS optimize thread exiting.
2015-08-13 14:47:28 114 [Note] InnoDB: Starting shutdown...
2015-08-13 14:47:29 114 [Note] InnoDB: Shutdown completed; log sequence number 1625977
OK

Filling help tables...2015-08-13 14:47:29 0 [Warning] TIMESTAMP with implicit DEFAULT value is deprecated. Please use --explicit_defaults_for_timestamp server option (see documentation for more details).
2015-08-13 14:47:29 0 [Note] ./bin/mysqld (mysqld 5.6.26) starting as process 137 ...
2015-08-13 14:47:29 137 [Note] InnoDB: Using atomics to ref count buffer pool pages
2015-08-13 14:47:29 137 [Note] InnoDB: The InnoDB memory heap is disabled
2015-08-13 14:47:29 137 [Note] InnoDB: Mutexes and rw_locks use GCC atomic builtins
2015-08-13 14:47:29 137 [Note] InnoDB: Memory barrier is not used
2015-08-13 14:47:29 137 [Note] InnoDB: Compressed tables use zlib 1.2.3
2015-08-13 14:47:29 137 [Note] InnoDB: Using Linux native AIO
2015-08-13 14:47:29 137 [Note] InnoDB: Using CPU crc32 instructions
2015-08-13 14:47:29 137 [Note] InnoDB: Initializing buffer pool, size = 128.0M
2015-08-13 14:47:29 137 [Note] InnoDB: Completed initialization of buffer pool
2015-08-13 14:47:29 137 [Note] InnoDB: Highest supported file format is Barracuda.
2015-08-13 14:47:29 137 [Note] InnoDB: 128 rollback segment(s) are active.
2015-08-13 14:47:29 137 [Note] InnoDB: Waiting for purge to start
2015-08-13 14:47:29 137 [Note] InnoDB: 5.6.26 started; log sequence number 1625977
2015-08-13 14:47:29 137 [Note] Binlog end
2015-08-13 14:47:29 137 [Note] InnoDB: FTS optimize thread exiting.
2015-08-13 14:47:29 137 [Note] InnoDB: Starting shutdown...
2015-08-13 14:47:31 137 [Note] InnoDB: Shutdown completed; log sequence number 1625987
OK

To start mysqld at boot time you have to copy
support-files/mysql.server to the right place for your system

PLEASE REMEMBER TO SET A PASSWORD FOR THE MySQL root USER !
To do so, start the server, then issue the following commands:

  ./bin/mysqladmin -u root password 'new-password'
  ./bin/mysqladmin -u root -h ubuntu14 password 'new-password'

Alternatively you can run:

  ./bin/mysql_secure_installation

which will also give you the option of removing the test
databases and anonymous user created by default.  This is
strongly recommended for production servers.

See the manual for more instructions.

You can start the MySQL daemon with:

  cd . ; ./bin/mysqld_safe &

You can test the MySQL daemon with mysql-test-run.pl

  cd mysql-test ; perl mysql-test-run.pl

Please report any problems at http://bugs.mysql.com/

The latest information about MySQL is available on the web at

  http://www.mysql.com

Support MySQL by buying support/licenses at http://shop.mysql.com

New default config file was created as ./my.cnf and
will be used by default by the server when you start it.
You may edit this file to change server settings

```

可能会出现找不到`libaio`依赖的错误，执行安装即可：
```bash
apt-get install libaio1
```

## 启动mysql服务
执行如下命令：
```bash
./bin/mysqld_safe & 
```

## 连接
```bash
./bin/mysql -u root 
```
刚装好时没有密码，所以可以直接登录上

## 设置新密码：
```bash
./bin/mysqladmin -u root  password '1234'  
```
1234为新密码

## 关闭mysql服务：
```bash
./bin/mysqladmin -uroot -p1234 shutdown 
```
