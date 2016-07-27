---
title: Git库迁移
date: 2016-02-12 12:11:36
description: Git库迁移
categories: 
- git
tags:
- git
---
有时我们需要将库迁移，如从bitbucket迁到github，可采用以下方式：

```bash
git clone --mirror https://bitbucket.org/exampleuser/repository-to-mirror.git

# Make a bare mirrored clone of the repository
cd repository-to-mirror.git
git remote set-url --push origin https://github.com/exampleuser/mirrored

# Set the push location to your mirror
git push --mirror
```