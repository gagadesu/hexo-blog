---
title: VPS 的装配流
date: 2018-08-13 23:31:58
tags: 
    - VPS
    - Ubuntu
    - CentOS
categories: TECH
permalink: vps
description: 介绍一台新开机的 VPS 在个人应用场景下的装配方法。
---

# 准备

## 主机别名

约定格式为：

<span id="inline-yellow">HOSTALIAS</span>
= `主机商/平台名三位缩略字母 - 主机所在国家/城市名两位缩略字母 + 数字编号`

如 Google Cloud Platform 为`gcp`、GigsgigsCloud 为`ggs`、Vultr 为`vtr`、Bandwagon 为`bwg`，以此类推；地名如 Taiwan 为`tw`，Tokyo 为`tk`。

故有`gcp-tw`、`bwg-la`、`vtr-nj1`等，HOSTALIAS全部使用小写。

## 信息预存

可通过多种方法在本机存储主机的外部 IP：

- 使用 LaunchBar 或 Alfred 等效率启动工具的 Snippets 存储各个主机的外部 IP，以 <span id="inline-yellow">HOSTALIAS</span> 的`首字母（重复则使用前两位） + 地名（数字编码）`共三或四位字母作为 Snippets 的快捷短语：如`gtw`，`gghk`；

- 在系统输入法或鼠须管中添加文本替换，快捷短语同上。

------

# 配置

以下操作全部基于 Ubuntu 18.04 LTS。

## SSH

- 生成公钥和私钥，必要时手动复制到`~/.ssh/authorized_keys`：

    ```
    ssh-keygen -t rsa -f ~/.ssh/HOSTALIAS -C USERNAME
    ```

    {% note info %}
    <span id="inline-yellow">HOSTALIAS</span> 如上填写，此处 <span id="inline-green">USERNAME</span> 为 `teishikaa`
    {% endnote %}

    {% note warning %}
    某些主机可能需要通过`ssh-copy-id`命令添加公钥：

    {% code %}
    ssh-copy-id -i ~/.ssh/HOSTALIAS.pub USERNAME@IP
    {% endcode %}
    {% endnote %}

- SSH 登录 root，使用 Auto Config 添加主机到 HyperApp，测试`sudo`和`docker`命令，确认系统是否正常。

    ```
    ssh root@IP
    ```
        
- 确认系统正常后，在 root 账户下添加自定义用户并为其赋予 root 权限：

    ```
    adduser USERNAME;
    usermod -aG sudo USERNAME;
    su USERNAME
    ```

- 设置本机免密码登录

    往`~/.ssh/config`配置文件添加以下信息即可通过`ssh alias`登录：

    ```
    vim ~/.ssh/config
    ```

    ```
    Host    alias   # 自定义别名
    HostName    hostname    # ssh 服务器的 IP 或域名
    Port    22  # ssh 服务器的端口，默认为22
    User    root    # ssh 服务器USERNAME
    IdentityFile    ~/.ssh/id_rsa   # 第一步生成的公钥文件对应的私钥文件
    ```

    此时只需输入`ssh alias`即可登录；更进一步还可以在 zsh 新建别名，免输`ssh`而直接用`alias`登录，`alias`即 <span id="inline-yellow">HOSTALIAS</span>。

- 延长 SSH 会话超时时间

    - 服务器端配置
	
	```
	vim /etc/ssh/sshd_config
	```

    ```
	ClientAliveInterval 30
	ClientAliveCountMax 3
    ```

    {% note info %}
    **ClientAliveInterval**：SSH Server 与 Client 的心跳超时时间。当客户端没有指令过来，Server 间隔 ClientAliveInterval 的时间（单位秒）会发一个空包到 Client 来维持心跳，保证 Session 有效。   
   
    **ClientAliveCountMax**：当心跳包发送失败时重试的次数，比如设置成了3，如果 Server 向 Client 连续发三次心跳包都失败了，就会断开这个 Session 连接。  
    {% endnote %}

	重启 ssh 以使之生效：
	
	```
	service ssh restart
	```

    - 客户端配置
    
    ```
    ~/.ssh/config
    ```
    修改以下内容：
    
    ```
    Host myhostshortcut
    HostName myhost.com
    User root
    ServerAliveInterval 30
    ServerAliveCountMax 3
    ```

## 安装 zsh 和 oh my zsh

- zsh

    ```
    sudo apt-get install zsh
    ```

    ```
    zsh --version
    ```

- oh my zsh

    ```
    sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
    ```

## 安装 Docker CE

{% tabs Install Docker CE %}
<!-- tab Ubuntu@linux -->
**设置仓库**

- 更新`apt package index`：

    {% code %}
    sudo apt-get update 
    {% endcode %}


- 安装依赖包以允许 apt 通过 HTTPS 使用仓库：

    {% code %}
    sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    software-properties-common
    {% endcode %}


- 添加 Docker 官方 GPG key：

    {% code %}
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    {% endcode %}


- 验证 GPG key 已安装：

    {% code %}
    sudo apt-key fingerprint 0EBFCD88
    {% endcode %}


- 显示以下内容说明安装成功：

    {% note success %}
    pub   4096R/0EBFCD88 2017-02-22
    Key fingerprint = 9DC8 5822 9FC7 DD38 854A  E2D8 8D81 803C 0EBF CD88
    uid                  Docker Release (CE deb) <docker@docker.com>
    sub   4096R/F273FCD8 2017-02-22
    {% endnote %}

<br />

**安装 Docker**

- 配置稳定版的仓库：

    {% code %}
    sudo add-apt-repository \
    "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
    $(lsb_release -cs) \
    stable"
    {% endcode %}


- 安装 Docker CE：

    {% code %}
    sudo apt-get update
    {% endcode %}

    {% code %}
    sudo apt-get install docker-ce
    {% endcode %}

    {% code %}
    sudo docker run hello-world
    {% endcode %}
<!-- endtab -->
<!-- tab CentOS@linux -->
**设置仓库**

- 安装`yum-utils`：

    {% code %}
    sudo yum install -y yum-utils
    {% endcode %}


- 建立稳定的仓库：

    {% code %}
    sudo yum-config-manager \
    --add-repo \
    https://docs.docker.com/v1.13/engine/installation/linux/repo_files/centos/docker.repo
    {% endcode %}

<br />

**安装 Docker**

{% code %}
sudo yum makecache fast
{% endcode %}

{% code %}
sudo yum -y install docker-engine
{% endcode %}

{% code %}
sudo systemctl start docker
{% endcode %}

{% code %}
sudo docker run hello-world
{% endcode %}
<!-- endtab -->
{% endtabs %}

## 启用 BBR

- 修改系统变量

    {% code %}
    $ root#
    echo "net.core.default_qdisc=fq" >> /etc/sysctl.conf;
    echo "net.ipv4.tcp_congestion_control=bbr" >> /etc/sysctl.conf;
    sysctl -p
    {% endcode %}


- 检查内核是否已开启 BBR

    {% code %}
    sysctl net.ipv4.tcp_available_congestion_control
    {% endcode %}

    输出以下内容即开启成功：

    {% note success %}
    $ sysctl net.ipv4.tcp_available_congestion_control
    net.ipv4.tcp_available_congestion_control = bbr cubic reno
    {% endnote %}


- 查看 BBR 是否启动

    {% code %}
    lsmod | grep bbr
    {% endcode %}

    输出以下内容即启动成功：

    {% note success %}
    $ lsmod | grep bbr
    tcp_bbr                20480  14
    {% endnote %}

------

# 应用

## Shadowsocks

- 使用 HyperApp 安装 Love Bundle，端口为 443；

- 将节点信息配置到 Surge，节点命名规则为 **`国旗 + 大写HOSTALIAS`**，如 `🇼🇸 GCP-TW`、`🇭🇰 GGS-HK`。
