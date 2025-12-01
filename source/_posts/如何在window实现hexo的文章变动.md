---
title: 如何在window实现hexo的文章变动
date: 2025-12-01 13:01:54
category: 博客
layout: post
cover: \images\如何在云服务上部署Hexo\image1.png
coverWidth: 1920
coverHeight: 925
---
## current situation
    目前的状况是本次需要在windows上`hexo new "name"`,写好文章之后，重新
    ```
    hexo clean
    hexo g
    ```
    然后重新打包部署
    下面的方法用来探索在windows变动文章之后,自动同步到服务器
---

## 方案：使用 CI/CD
🧩 第 1 步：把 Hexo 项目放到 GitHub
在本地：
```
git init
git add .
git commit -m "init hexo"
git remote add origin https://github.com/YOUR_NAME/your-hexo.git
git push -u origin main
```
