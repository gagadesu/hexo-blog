---
title: ttRSS 的搭建
date: 2018-08-14 02:01:52
tags: TTRSS
categories: Tech
description: ttRSS 搭建指南
---

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

---

## 安装镜像并运行容器

### PostgreSQL[^1]

```
sudo docker pull nornagon/postgres;
sudo docker run -d --name ttrssdb nornagon/postgres
```

### ttRSS
```
sudo docker pull fischerman/docker-ttrss
```

```
sudo docker run -d --link ttrssdb:db -p 8080:80 -e SELF_URL_PATH=http://[[IP_ADDRESS]]:8080 fischerman/docker-ttrss
```

{% note info %}
`-p 8080:80`：该参数表明，将该容器内应用的`80`端口映射到主机的`8080` 端口；

`-e SELF_URL_PATH=http://[[IP_ADDRESS]]:8080`：该参数表明，该 ttRSS 应用将可从`http://[[IP_ADDRESS]]:8080` 访问。
{% endnote %}

---

## 配置优化

### 安装 Feedly 主题

下载主题文件并解压到主目录：

<a id="download" href="https://www.dropbox.com/s/88olyj3dsmmr23t/tt-rss-feedly-theme-1.3.0-for-ttrss-17.4.0.zip"><i class="fa fa-download"></i><span> Feedly 主题</span> </a>

若未安装解压工具，可先安装`unzip`：
```
sudo apt-get install unzip
```

```
unzip master.zip
```


查看当前 Docker 容器列表，找到`IMAGE`一列为 `[[fischerman/docker-ttrss]]` 的记录，复制其 `[[CONTAINER ID]]`：
```
sudo docker ps
```

![](http://pa5j6ydpq.bkt.clouddn.com/20180818165044_w53LBz_15091878055188.jpeg)

将主题配置文件复制到容器相应的路径中：
```
sudo docker cp tt-rss-feedly-theme-master/feedly.css [[CONTAINER ID]]:/var/www/themes
```

```
sudo docker cp tt-rss-feedly-theme-master/feedly [[CONTAINER ID]]:/var/www/themes
```

<br />

### 配置全文输出

```git
git clone https://github.com/WangQiru/mercury_fulltext.git
```

```
sudo docker cp mercury_fulltext [[CONTAINER ID]]:/var/www/plugins
```

注册 Mercury 并复制 [Mercury Web Parser API Key](https://mercury.postlight.com/web-parser/)，在 RSS 设置中配置 mercury_fulltext 插件。

<br />

### 模拟 Fever

```git
git clone https://github.com/rannen/tinytinyrss-fever-plugin.git
```

```
sudo docker cp ~/tinytinyrss-fever-plugin/fever [[CONTAINER ID]]:/var/www/plugins
```

在设置页面启用名为`fever`的插件；保存后，再次进入设置，选项卡下会多出一个名为`Fever Emulation`的板块；在该板块的文本框中，设置一个专用于该插件的密码，保存；文本框下方为服务器地址`http://rss.example.org/plugins/fever/`。

在 Reeder 中，在添加 RSS 账号时选择 Fever，在服务器地址栏填入上述地址，用户名填写`admin`，密码为以上设定的密码。
[^1]: 此镜像已预先配置完成以直接配合 RSS 镜像使用的数据库环境。