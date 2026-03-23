---
title: Hexo 博客部署指南
date: 2026-03-23 14:00:00
---

# Hexo 博客部署指南

本文记录如何使用 GitHub Actions 自动部署 Hexo 博客到 GitHub Pages。

## 1. 准备工作

### 1.1 创建 GitHub 仓库

在 GitHub 上创建一个新仓库，命名为 `username.github.io`（将 `username` 替换为你的 GitHub 用户名）。

### 1.2 安装 Node.js

确保本地安装了 Node.js（建议 v18+）和 npm。

```bash
node -v
npm -v
```

### 1.3 安装 Hexo CLI

```bash
npm install -g hexo-cli
```

## 2. 初始化项目

### 2.1 创建博客项目

```bash
hexo init myblog
cd myblog
npm install
```

### 2.2 选择主题

本博客使用 [one-paper](https://github.com/liruiying728/hexo-theme-one-paper) 主题，简洁美观，专注于写作体验。

```bash
git clone https://github.com/liruiying728/hexo-theme-one-paper.git themes/one-paper
```

在 `_config.yml` 中设置：

```yaml
theme: one-paper
```

## 3. 配置 GitHub Actions

在 `.github/workflows/pages.yml` 中配置自动部署：

```yaml
name: Deploy Hexo to GitHub Pages

on:
  push:
    branches:
      - master
  workflow_dispatch:

permissions:
  contents: write
  pages: write
  id-token: write

concurrency:
  group: pages
  cancel-in-progress: false

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-node@v4
        with:
          node-version: "20"
          cache: npm

      - name: Install dependencies
        run: npm ci

      - name: Generate site
        run: npm run build

      - name: Deploy to GitHub Pages
        uses: peaceiris/actions-gh-pages@v4
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./public
          publish_branch: gh-pages
          cname: www.yourdomain.cn
```

## 4. 部署到 GitHub

### 4.1 初始化 Git 仓库

```bash
git init
git add .
git commit -m "Initial commit"
```

### 4.2 添加远程仓库

```bash
git remote add origin https://github.com/username/username.github.io.git
git branch -M master
git push -u origin master
```

### 4.3 配置 GitHub Pages

1. 进入仓库 **Settings** → **Pages**
2. **Source** 选择 **GitHub Actions**
3. **Custom domain** 输入你的域名（如 `www.yourdomain.cn`）
4. 勾选 **Enforce HTTPS**

## 5. 域名解析

在域名服务商处添加 DNS 记录：

| 记录类型 | 名称 | 值 |
|---------|------|-----|
| CNAME | www | username.github.io |
| A | @ | 185.199.108.153 |

> GitHub Pages 官方 IP：185.199.108.153 ~ 185.199.111.153

## 6. 写文章

```bash
hexo new "文章标题"
```

文章创建在 `source/_posts/` 目录下，使用 Markdown 编写。

## 7. 自动化流程

以后只需：

1. 写完文章后 `git add . && git commit -m "xxx" && git push`
2. GitHub Actions 自动构建并部署
3. 访问 `https://username.github.io` 或你的自定义域名

## 相关链接

- [Hexo 官方文档](https://hexo.io/docs/)
- [one-paper 主题](https://github.com/liruiying728/hexo-theme-one-paper)
- [GitHub Pages 文档](https://docs.github.com/en/pages)
