---
title: 路由器刷 Padavan 固件
date: 2019-01-16 02:07:40
tags: 
  - Padavan
  - 路由器
  - 科学上网
categories: TECH
permalink: padavan
description: 路由器刷 Padavan 固件实现科学上网，以小米路由3为例。
---

### 前期准备

- [低版本小米路由3固件](http://bit.ly/2DbZ7z3)
- [VMware Fusion（macOS）或 VMware Workstation（Windows）](http://bit.ly/2Df0mO7)
- [Linux 操控台 PROMETHEUS-64](http://bit.ly/2DdoD7i)
- [适配小米路由3的 Padavan 固件](http://bit.ly/2Ddv0aC)（官网：http://bit.ly/2Da4RcE ）

### 操作步骤

#### 1. 路由器降级

将路由器刷成低版本的开发版，以便获取 ROOT 权限和开启 SSH；先对原先的固件作备份，然后将系统『手动升级』成准备好的 2.11.20 版。

#### 2. 安装 VMware 并开启 Linux 操控台

Windows 使用 VMware Workstation，macOS 使用 VMware Fusion；注意 macOS 不能在 Parallel Desktop 环境里使用 VMware Workstation，因为 PD 不支持 Intel VT-x。

#### 3. 获取 ROOT 权限

进入 Linux 操控台，在主界面用数字键选择『0：SSH-hack of stock firmware』：

- 输入路由器 IP：默认『192.168.31.1』
- 输入密码：若降级后配置了路由器，则输入配置的登录密码；反之输入 [ROOT 密码](http://bit.ly/2Da6ze4)

#### 4. 刷入华硕固件

返回主界面，选择『4：Flash a firmware』；提示是否备份，选择备份，按提示直至刷机完成。

#### 5. 配置路由

搜索名为『ASUS』的 Wi-Fi 连入，密码为『1234567890』；浏览器输入『192.168.1.1』进入路由器后台，帐号密码皆为『admin』。

点击右上角版本号进入升级界面，选择准备好的适配小米路由3的 Padavan 固件，上传。

刷机完成后点击左侧导航栏的『高级设置-系统管理-恢复/导出/上传设置』，先点击下方的『路由器内部存储-恢复出厂模式-重置』，再点击上方的『路由器参数(NVRAM)-恢复出厂模式-重置』；重置完成后路由器的 IP 变为『192.168.123.1』。

