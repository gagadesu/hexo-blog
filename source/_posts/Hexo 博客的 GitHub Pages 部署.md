---
title: Hexo 博客部署指南
date: 2018-08-14 17:09:42
tags: 
    - Hexo
    - GitHub Pages
    - Netlify
categories: BLOG
description: 在 GitHub Pages 和 Netlify 上部署 Hexo 博客的方法
---

# Hexo

## 安装 Hexo 框架

```
cd PATH/TO/PROJECT;
mkdir hexo
```

```
npm install -g hexo
```

## 初始化

```
hexo init
```

## 生成静态文件

```
hexo g
```
执行以上命令之后，Hexo 会在 public 文件夹生成相关 html 文件，这些文件将提交到 GitHub 仓库。

## 本地预览

```
hexo s --debug
```
打开浏览器访问`http://localhost:4000`即可预览博客内容，<kbd>⌃</kbd><kbd>C</kbd>停止本地预览。

## 新建文章

```
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

```
hexo new blog "postName" #缩写：hnb
```

## 其他命令

```
hexo generate #生成静态页面至 public/
hexo server #开启预览访问端口
hexo deploy #部署到 GitHub
hexo help  #查看帮助
hexo version  #查看 Hexo 的版本
```

```
hexo s -g #生成并本地预览
hexo d -g #生成并上传到 GitHub
```

## 主题设定

主题设定可参考 [NexT 官方使用指南](http://theme-next.iissnan.com/)。

### 安装 NexT

```
cd PATH/TO/HEXO
git clone https://github.com/iissnan/hexo-theme-next themes/next
```

### 启用并验证主题

编辑**站点配置文件**`_config.yml`，将`theme`字段更改为`next`；执行`hexo s --debug`在本地验证主题。

------

# 部署

{% tabs Deploy Hexo Blog %}
<!-- tab GitHub Pages -->
**创建 GitHub 仓库**

新建名为`USERNAME.github.io`的仓库，`https://USERNAME.github.io`为博客的访问地址。

<br />

**配置 Hexo**

编辑 <span id="inline-blue">站点配置文件</span> 的`Deploy`字段：

{% code %}
# Deployment
## Docs: https://hexo.io/docs/deployment.html
deploy:
    type: git
    repository: git@github.com:USERNAME/USERNAME.github.io.git
    branch: master
{% endcode %}

<br />

**提交部署**

{% code %}
hexo d -g
{% endcode %}
<!-- endtab -->
<!-- tab Netlify -->
**本地仓库**

将`/node_modules`和`/public`添加到`.gitignore`：

{% code %}
echo "/public" >> .gitignore
echo "/node_modules" >> .gitignore
{% endcode %}

初始化并提交本地仓库：

{% code %}
cd PATH/TO/HEXO
git init
git add .
git commit -m "Initial commit"
git remote add origin git@github.com:USERNAME/USERNAME.github.io.git
git push origin master
{% endcode %}

<br />

**Netlify 设置**

- 关联 GitHub 仓库

![](http://pa5j6ydpq.bkt.clouddn.com/20180825143749_4QNQ73_Screenshot.jpeg)

![](http://pa5j6ydpq.bkt.clouddn.com/20180825144205_C4URTL_Screenshot.jpeg)

- 自动部署

![](http://pa5j6ydpq.bkt.clouddn.com/20180825144325_IrSChH_Screenshot.jpeg)

稍等片刻即可部署成功：

![](http://pa5j6ydpq.bkt.clouddn.com/20180825144504_uSmVh5_Screenshot.jpeg)

- 开启 HTTPS：

![](http://pa5j6ydpq.bkt.clouddn.com/20180825144716_XjkA1b_Screenshot.jpeg)

未来只需在本地仓库完成编辑后，提交到远程仓库，Netlify 便会自动部署博客。
<!-- endtab -->
{% endtabs %}






