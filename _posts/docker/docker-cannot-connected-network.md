---
title: docker容器不能联网的解决方案
date: 2015-08-05 14:30:20
description: docker容器不能联网的解决方案
categories: 
- docker
tags:
- docker
---

在使用Ubuntu容器时，如果使用apt-get安装软件时，会提示类似如下信息：
```bash
E: Failed to fetch http://archive.ubuntu.com/ubuntu/pool/main/w/wheel/python-wheel_0.24.0-1~ubuntu1_all.deb  Could not resolve 'archive.ubuntu.com'
 
E: Unable to fetch some archives, maybe run apt-get update or try with --fix-missing?
```
	注：下面的方法是在容器内能ping通公网IP的解决方案，如果连公网IP都ping不通，那主机可能也上不了网（尝试`ping 8.8.8.8`）

### 使用--net:host选项
```bash
sudo docker run --net:host --name ubuntu_bash -i -t ubuntu:latest /bin/bash 
```
### 使用--dns选项
```bash
sudo docker run --dns 8.8.8.8 --dns 8.8.4.4 --name ubuntu_bash -i -t ubuntu:latest /bin/bash 
```
### 改dns server
```bash
vi /etc/default/docker 
```
去掉`docker_OPTS="--dns 8.8.8.8 --dns 8.8.4.4"`前的#号 （之后重启容器即可访问网络）

### 不用dnsmasq
```bash
vi /etc/NetworkManager/NetworkManager.conf 
```
在dns=dnsmasq前加个#号注释掉 
```bash
sudo restart network-manager 
sudo restart docker 
```
### 重建docker0网络（慎用）
```bash
pkill docker 
iptables -t nat -F 
ifconfig docker0 down 
brctl delbr docker0 
docker -d 
```
`iptables -t nat -F`可能会导致计算机中默认路由被删除，导致无法上网。

### 直接在docker内修改/etc/hosts
