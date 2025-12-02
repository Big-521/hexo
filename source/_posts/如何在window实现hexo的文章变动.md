---
title: 如何在window实现hexo的文章变动
date: 2025-12-01 13:01:54
category: 博客
layout: post
cover: /images/如何在云服务上部署Hexo/image1.png
coverWidth: 1920
coverHeight: 925
---

## current situation

目前的状况是本次需要在 Windows 上 `hexo new "name"`，写好文章之后，重新执行：

```
hexo clean
hexo g
```


然后重新打包部署。
下面的方法用来探索在 Windows 变动文章之后，自动同步到服务器。

****

## 文章变动 test
