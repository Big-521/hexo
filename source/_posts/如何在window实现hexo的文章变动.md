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
确保仓库结构是：
```
hexo/
 ├── source/
 ├── themes/
 ├── public/   ← 不需要上传
 ├── _config.yml
 ├── package.json
```
注意：public 不要上传到 GitHub（会被 Hexo 自动生成）
---

## 🧩 第 2 步：服务器准备
进入服务器目录
```
cd /blog
```

clone 仓库
```
git clone https://github.com/Big-521/hexo.git
cd hexo
```

安装依赖
```
npm install
npm install -g hexo-cli http-server pm2
```
---

## 🧩 第 3 步：写自动部署脚本
创建部署脚本：
```
nano /portfolio/deploy.sh
```

写入：
```
#!/bin/bash
cd /portfolio/your-hexo

echo "Pulling latest code..."
git pull

echo "Building..."
hexo clean
hexo g

echo "Restarting service..."
pm2 restart hexo-app || pm2 start "http-server public -p 4000 -a 0.0.0.0" --name hexo-app

echo "Done!"
```
保存退出
```
Ctrl+O
Enter #确认文件名
Ctrl+X
```
然后：
chmod +x /blog/deploy.sh
---

## 🧩 第 4 步：服务器创建 SSH 部署专用 key（CI 用）
在服务器创建一个 SSH Key：
```
ssh-keygen -t rsa -b 4096 -C "hexo-deploy" -f ~/.ssh/hexo_deploy -N ""
```
你会得到：
```
~/.ssh/hexo_deploy        ← 私钥（给 GitHub）
~/.ssh/hexo_deploy.pub    ← 公钥（添加到 authorized_keys）
```
将公钥加入授权：
```
cat ~/.ssh/hexo_deploy.pub >> ~/.ssh/authorized_keys
```
---

## 🧩 第 5 步：把私钥加到 GitHub Secrets
1. 打开 GitHub 仓库
2. Settings → Secrets and variables → Actions → New repository secret
---

## 🧩 第 6 步：创建 GitHub Actions 工作流
在本地：
```
mkdir -p .github/workflows
nano .github/workflows/deploy.yml
```
写入：
```
name: Deploy Hexo

on:
  push:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout Repo
      uses: actions/checkout@v3

    - name: Setup SSH
      run: |
        mkdir -p ~/.ssh
        echo "${{ secrets.DEPLOY_KEY }}" > ~/.ssh/id_rsa
        chmod 600 ~/.ssh/id_rsa
        ssh-keyscan -p ${{ secrets.SERVER_PORT }} ${{ secrets.SERVER_HOST }} >> ~/.ssh/known_hosts

    - name: Deploy to Server
      run: |
        ssh -p ${{ secrets.SERVER_PORT }} ${{ secrets.SERVER_USER }}@${{ secrets.SERVER_HOST }} "bash /blog/deploy.sh"
```

## 🧩 最终效果（自动部署）
本地写文章：
```
hexo new post "新文章"
```

写完：
```
git add .
git commit -m "update"
git push
```
