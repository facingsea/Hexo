---
title: docker编译安装python2.7
date: 2015-07-25 23:28:20
description: docker编译安装python2.7
categories: 
- docker
tags:
- docker
- python
---

## 以--net="host"方式启动容器
这种方式能够使容器连接外网，便于在编译时下载所需的依赖
```bash
sudo docker run --net="host" -t -i REPOSITORY:TAG /bin/bash
```

## 下载python源码包
可以通过wget 命令来下载：
```bash
wget -c https://www.python.org/ftp/python/2.7.8/Python-2.7.8.tgz 
```
如果出现错误提示：无法建立ssl连接时，可以添加 `--no-check-certificate`
```bash
wget -c --no-check-certificate https://www.python.org/ftp/python/2.7.8/Python-2.7.8.tgz 
```

但是由于网络原因可能会中途没下载完就死了，建议通过p2p工具下载，如迅雷，然后通过数据卷的方式共享文件到宿主机和容器：
数据卷需要在启动容器的时候指定：
```bash
sudo docker run --net="host" -t -i -v /opt/share:/data/share REPOSITORY:TAG /bin/bash
```
	-v：表示挂载宿主机的/opt/share目录到指定容器的/data/share目录下，两个目录之间用分号隔开

## 执行./configure
在对python进行编译之前，必须对它进行配置。在unix/linux平台上的安装过程中，配置和编译过程全部已经自动化了，我们所需要做的就是输入几条命令然后回车即可。
通过执行脚本`./configure`开始编译， 这一过程可以决定系统的配置并创建相应的Makefile文件。你也可以传递一些参数选项来配置这个脚本。当这一步完成后，就可以运行make了.
```bash
./configure --prefix=/usr/local/python2.7
```
`--prefix`用于指定python的安装路径于`/usr/local/python2.7`,配置完成后，我们就可以执行make操作了   
由于python的编译需要gcc支持，所以此时如果系统中没有gcc的话，需要先安装gcc
```bash
sudo apt-get install gcc
```

## 执行make
执行：
```bash
root@ubuntu$ make
```
make编译的时间可能会比较长，大概5-6分钟，完成之后我们就可以进行安排操作了，输入指令make install(注意权限)， 如下:
```bash
sudo make install
```
之前在./configure的时候增加/usr/local/python的安装目录，所以我们用ls命令查看此目录的信息。其中bin目录下用于存放python的相关执行程序。
但是，由于这是我们自己手动将python安装于/usr/local/python2.7目录下，此时$PATH环境变量无法找到此目录下的python解释器，为此可以增加一个软链接，代码如下：
```bash
sudo ln -s /usr/local/python2.7/bin/python  /bin/python2.7
```
通过此命令可以在/bin路径下创建一软链接文件python2.7，当访问此链接文件，就可以间接地访问/usr/local /python2.7下的python程序了。这种方式与windows下的快捷方式类似。
下面可以通过运行python命令交互模式以查看刚刚安装的python版本情况：
执行
```bash
root@ubuntu$ python2.7
```
通过直接执行python2.7可以访问`/usr/local/python2.7`下的python解释器。
通过`python2.7 -V`来查看版本。