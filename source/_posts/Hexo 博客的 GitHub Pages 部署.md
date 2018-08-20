---
title: Hexo 博客的 GitHub Pages 部署
date: 2018-08-14 17:09:42
tags: 
    - Hexo
    - GitHub Pages
categories: Blog
description: 在 GitHub Pages 上部署 Hexo 博客的方法
---

# GitHub Pages


## 创建 GitHub 仓库

新建名为`[[username]].github.io`的仓库，`https://[[username]].github.io`为博客的访问地址。

## 配置 Git

参考《Git 及 GitHub 配置指南》

------

# Hexo

## 安装 Hexo 框架

```shell
cd ~/Dropbox/Projects;
mkdir hexo
```

```shell
npm install -g hexo
```

## 初始化

```shell
hexo init
```

## 生成静态文件

```shell
hexo g
```
执行以上命令之后，Hexo 会在 public 文件夹生成相关 html 文件，这些文件将提交到 GitHub 仓库。

## 本地预览

```shell
hexo s --debug
```
打开浏览器访问`http://localhost:4000`即可预览博客内容，`⌃ + C`停止本地预览。

## 新建文章

```shell
hexo new "postName"
```
可在`~/hexo/scaffolds/`中自定义模板，模板将声明文章的标题、时间、标签等信息：

```
---
title: {{ title }}
date: {{ date }}
tags:
categories:
description:
---
```
以`blog.md`为例，新建文章用：

```shell
hexo new blog "postName" #缩写：hnb
```

## 其他命令

```shell
hexo generate #生成静态页面至 public/
hexo server #开启预览访问端口
hexo deploy #部署到 GitHub
hexo help  #查看帮助
hexo version  #查看 Hexo 的版本
```

```shell
hexo s -g #生成并本地预览
hexo d -g #生成并上传到 GitHub
```

# 主题设定

主题设定可参考 [NexT 官方使用指南](http://theme-next.iissnan.com/)。

## 安装 NexT

```shell
cd ~/Dropbox/Projects/hexo;
git clone https://github.com/iissnan/hexo-theme-next themes/next
```

## 启用并验证主题

编辑**站点配置文件**`_config.yml`，将`theme`字段更改为`next`；执行`hexo s --debug`在本地验证主题。