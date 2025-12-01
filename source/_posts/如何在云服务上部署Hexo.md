---
title: 如何在云服务上部署Hexo
date: 2025-12-01 09:49:00
category: 博客
layout: post
cover: \images\如何在云服务上部署Hexo\image1.png
coverWidth: 1920
coverHeight: 925
---

## 生成 `public` 文件

1. 执行 `hexo clean` 清理 Hexo 的缓存和生成的文件
2. 执行 `hexo g` 生成静态页面

---

## 将 `public` 文件上传至云服务器

使用 FileZilla 将 `public` 文件夹上传至云服务器。

---

## 登入云服务器控制台
    CloudShell
---

## 进入 `public` 目录

```bash
cd /portfolio/public
```

---

## 使用 PM2 启动服务

1. 安装 PM2：

```bash
npm install -g pm2
```

2. 启动 http-server 并命名进程：

```bash
pm2 start "http-server -p 4000 -a 0.0.0.0" --name hexo-portfolio
```

3. 设置开机自启：

```bash
pm2 save
pm2 startup
```

4. 查看服务状态：

```bash
pm2 list
```

5. 浏览器访问：

```
http://IP地址:4000
```

---

## 设置安全组策略

确保云服务器的 **TCP 4000 端口**允许入站访问。
