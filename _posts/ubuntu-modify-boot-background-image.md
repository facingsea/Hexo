---
title: 更改Ubuntu引导界面背景
date: 2015-12-23 14:30:20
description: 更改Ubuntu引导界面背景
categories: 
- ubuntu
tags:
- ubuntu
---

1. 把你喜欢的背景图片放入/boot/grub/目录下 例如桌面
```bash
$sudo cp  grub_bg.jpg   /boot/grub/
```

2. 更新grub配置
```bash
$sudo update-grub
```

3. 重启