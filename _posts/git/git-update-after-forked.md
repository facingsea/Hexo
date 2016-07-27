---
title: Fork项目之后更新
date: 2016-05-28 23:29:36
description: 假如有一项目A，从A项目fork的项目B，当项目A有更新之后，项目B需要从项目A获取更新的内容
categories: 
- git
tags:
- git
---

假如有一项目A，从A项目fork的项目B，当项目A有更新之后，项目B需要从项目A获取更新的内容，可以按以下操作：
1、clone项目B到本地
```bash
git clone B_REPO_URL
```

2、cd到本地B的目录，把A作为一个remote加到本地的B中（一般命名为upstream）
```bash
git remote add upstream A_REPO_URL
```

3、将远程项目A的相应分支pull到本地，如果有冲突需要先处理冲突
```bash
git pull upstream master
```

4、将本地的更改push到远程B
```bash
git push origin master
```