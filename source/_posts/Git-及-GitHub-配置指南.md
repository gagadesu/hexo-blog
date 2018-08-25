---
title: Git 及 GitHub 的使用
date: 2018-08-14 17:17:45
tags:
    - Git
    - GitHub
categories: TECH
description: Git 及 GitHub 的基本使用方法
---

# 常规操作

- SSH

```
ssh-keygen -t rsa -f ~/.ssh/ALIAS.pub -C "EMAIL"
```

将公钥复制到 GitHub 的 SSH and GPG Keys 中，然后测试是否连接成功：

```
ssh -T git@github.com
```

{% note success %}
Hi USERNAME You've successfully authenticated, but GitHub does not provide shell access.
{% endnote %}

- 添加用户信息

```
git config --global user.name "USERNAME";
git config --globla user.email "EMAIL"
```

- 本地仓库初始化

```
cd PATH/TO/PROJECT
git init
```

- 关联远程仓库

```
git remote add origin git@github.com:USERNAME/xxx.git
```

```
git pull git@github.com:USERNAME/xxx.git
```

通过`git clone`获得的本地仓库无需再关联远程仓库，只需在本机终端添加 SSH 和用户信息。

- 提交项目

```
git add .
```

```
git commit -m "COMMENT"
```

```
git push origin master
```

------

# 用 git subtree 管理子项目包

以 Hexo 博客优化中需要用到的 NexT 主题为例，通过`fork`+`git subtree`将项目下载到相应文件夹中便于管理和未来的更新。

```
cd PATH/TO/HEXO
```

- 关联子项目的远程仓库

```
git remote add -f next git@github.com:changzhiga/hexo-theme-next.git
```

- 拉取项目到本地主题文件夹

```
git subtree add --prefix=themes/next next master --squash
```

{% note warning %}
如果不加`--squash`参数，主项目会合并子项目本身所有的 commit 历史记录，加上`--squash`参数是把子项目的记录合成一次 commit 提交到主项目，这样主项目只是合并一次 commit 记录。
{% endnote %}

- 提交项目

主项目的提交按常规操作，子项目按以下方式提交：

```
git subtree push --prefix=themes/next next master --squash
```

- 更新子项目

```
git subtree pull --prefix=themes/next next master --squash
```

- 子项目切出起点

当主项目的 commit 提交多次过后，再推送子项目到远程库时，`subtree`每次都要遍历很多 commit 而浪费时间，此时可以用`subtree`将子项目当前版本切出为一个分支，作为后面的 push 时遍历的新起点，这样每次遍历都只从上次切出的分支的起点开始，不会再遍历以前的记录，从而节省时间。

假如已经提交了主项目和子项目，工作空间干净，这时子项目当前版本切出到新分支作为起点：

```
# git subtree split [--rejoin] --prefix=<本地子项目目录> --branch <主项目中作为放置子项目的分支名>
git subtree split [--rejoin] --prefix=themes/next --branch next
```

{% note warning %}
如果 push 时使用了`--squash`参数合并提交，那么 split 时不能使用`--rejoin`参数，反之必须使用。
{% endnote %}






