---
title: Git 及 GitHub 的使用
date: 2018-08-14 17:17:45
tags:
    - Git
    - GitHub
categories: TECH
permalink: git-and-github
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

------

# Git 分支

## 分支的新建与合并

- 分支的新建与切换

    ```
    git branch BRANCHNAME # 新建分支
    git checkout BRANCHNAME # 切换到新建的分支
    ```

    与以上命令等价的是：

    ```
    git checkout -b BRANCHNAME
    ```

- 分支的合并

    ```
    git checkout master # 切换到 master 分支
    git merge BRANCHNAME # 合并到 master 分支
    git branch -d BRANCHNAME # 删除已经合并的分支
    ```

- 遇到冲突时的分支合并

    如果在不同的分支中都修改了同一个文件的同一部分，Git 就无法干净地将两者合并到一起；Git 作了合并，但是没有提交，它会停下等待人工将冲突解决，此时可用`git status`查阅，用`git mergetool`（默认选择`opendiff`）等工具解决冲突。当冲突解决完毕后，可用`git commit`完成此次提交。另外，为了方便未来的查阅，可加以注解以提供更多改动的步骤和原因。

## 分支的管理

```
git branch # 列出当前所有的分支清单
git branch -v # 查看各个分支最后一次提交对象的信息

git branch --merged # 查看哪些分支已被并入当前分支（哪些分支是当前分支的直接上游）
git branch --no-merged # 查看尚未合并到当前分支的工作

git branch -D BRANCHNAME # 强制删除尚未合并到主分支的分支
```

## 远程分支

远程分支是对远程仓库中的分支的 **索引**，它们是一些无法移动的 **本地分支**，只有在 Git 进行网络交互时才会更新；远程分支就像是书签，标示着上次连接远程仓库时上面各分支的位置。远程分支的表示形式是`(远程仓库名)/(分支名)`，例如`origin/master`。

`git clone`一个远程仓库到本地之后，Git 会自动将此远程仓库命名为`origin`，并下载其中所有的数据，建立一个指向它的`master`分支的指针，在本地命名为`origin/master`，然而在本地无法更改其数据；接着 Git 会建立一个本地的`master`分支，始于`origin`上`master`分支相同的位置。

- 同步远程仓库

    ```
    git fetch origin # 同步远程仓库的数据到远程分支
    git fetch teamone # 同步另一个小组仓库的数据到远程分支

    git merge origin/master # 将远程分支中的内容合并到当前分支
    ```

- 推送本地分支

    ```
    # git push (远程仓库名) (分支名)
    git push origin master
    ```

    以上命令等价于：

    ```
    git push origin master:master # (本地分支名):(远程分支名)
    git push origin master:BRANCHNAME # 将本地 master 分支上传到远程仓库的 BRANCHNAME 分支
    ```

- 跟踪远程分支

    从远程分支`checkout`获得的本地分支称为 *跟踪分支（tracking branch）*。在克隆仓库时，Git 通常会自动创建一个名为`master`的分支来跟踪`origin/master`，这正是`git push`和`git pull`一开始就能正常工作的原因。

    ```
    git checkout -b BRANCHNAME origin/BRANCHNAME # 在本地新建分支来跟踪 origin/BRANCHNAME
    git checkout --track origin/BRANCHNAME # 以上命令的简化

    git checkout -b BRANCHNAME2 origin/BRANCHNAME # 为本地分支设定不同于远程分支的名字
    ```

- 删除远程分支

    ```
    git push origin :BRANCHNAME # 相当于提取空白然后将其变成(远程分支)
    ```

## 分支的变基

- 变基的概念与命令

    与`merge`类似，`rebase`也是将一个分支中的修改整合到另一个分支中的操作，然而两者有着明显的区别。变基的原理是回到两个分支最近的共同祖先，根据当前分支（要进行变基的分支）后续的历次提交对象，生成一系列文件补丁，然后以基底分支（即主干分支）最后一个提交对象为新的出发点，逐个应用之前准备好的补丁文件，最后生成一个新的合并提交对象，从而改写当前分支的提交历史，使其称为主干分支的直接下游：

    ![](http://pa5j6ydpq.bkt.clouddn.com/20180828232408_AufzGQ_18333fig0328-tn.jpeg)

    ```
    git checkout experiment # 切换到要进行变基的分支
    git rebase master # 将当前分支变基到主干分支
    ```

    <br />

    ![](http://pa5j6ydpq.bkt.clouddn.com/20180828232527_Vxyd4f_18333fig0329-tn.jpeg)

    然后回到`master`分支，进行一次快进合并：

    ```
    git checkout master
    git merge experiment
    ```

    <br />

    ![](http://pa5j6ydpq.bkt.clouddn.com/20180828232825_ORmIuh_18333fig0330-tn.jpeg)

- 多个分支的变基

    例如要将`client`分支合并到`master`：

    ![](http://pa5j6ydpq.bkt.clouddn.com/20180828232931_oSxHNg_18333fig0331-tn.jpeg)

    ```
    git rebase --onto master server client
    ```

    <br />

    结果为：

    ![](http://pa5j6ydpq.bkt.clouddn.com/20180828233024_8Mi34O_18333fig0332-tn.jpeg)

    `git checkout master && git merge client`后：

    ![](http://pa5j6ydpq.bkt.clouddn.com/20180828233111_04ZGR1_18333fig0333-tn.jpeg)

    接下来合并`server`分支：

    ```
    git rebase master server
    ```

    <br />

    ![](http://pa5j6ydpq.bkt.clouddn.com/20180828233240_6u8off_18333fig0334-tn.jpeg)

    <br />

    ```
    git checkout master
    git merge server

    git branch -d client
    git branch -d server
    ```

    <br />

    ![](http://pa5j6ydpq.bkt.clouddn.com/20180828233327_XY3Wdr_18333fig0335-tn.jpeg)

- 变基的风险

    **一旦分支中的提交对象发布到公共仓库，就千万不要对该分支进行变基操作。**






