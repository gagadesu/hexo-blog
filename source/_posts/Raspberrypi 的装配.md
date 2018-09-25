---
title: Raspberrypi 的装配
date: 2018-08-13 02:07:40
tags: Raspberrypi
categories: TECH
permalink: raspberrypi
description: Raspberrypi Configuration
---

# 镜像烧制

- 从[树莓派官网](https://www.raspberrypi.org/downloads/raspbian/)下载最新的 Raspbian 系统镜像，通过 Etcher 烧录进 TF 卡中；如果不使用最新的 Debian 系统，可以烧录 [Raspbian Jessie](https://www.dropbox.com/s/y5vsffe57aa680e/2017-07-05-raspbian-jessie.zip?dl=0)。

- 烧制完成后，进入系统根目录`boot`，新建无后缀的空脚本文件`ssh`。

- 完成以上步骤之后，即可拔出 TF 卡，将其插入树莓派中，接通电源，等待其自动连接上局域网。

# 基础配置

## SSH 连接

在终端输入`ssh pi@192.168.X.X`，初始密码为`raspberry`。亦可参照 VPS 配置 SSH 的做法生成公钥和私钥来创建快捷短语。
    
## 修改管理员密码

```
passwd pi
```
    
## Samba

修改系统软件源：

```
sudo nano /etc/apt/sources.list
```

将其修改为[清华大学](https://mirror.tuna.tsinghua.edu.cn/help/raspbian/)等国内源：

```
deb http://mirrors.tuna.tsinghua.edu.cn/raspbian/raspbian/ stretch main non-free contrib
deb-src http://mirrors.tuna.tsinghua.edu.cn/raspbian/raspbian/ stretch main non-free contrib
```
       
* 刷新软件列表：

```
sudo apt-get update
```
        
* 安装 Samba 及其依赖：

```
sudo apt-get install samba samba-common-bin
```
        
* 修改 Samba 的配置文件：

```
sudo nano /etc/samba/smb.conf
```

在配置文件的最后加上：

```
[pi]

    path = /home/pi/

    valid users = pi

    browseable = Yes

    writeable = Yes

    writelist = pi

    create mask = 0777

    directory mask = 0777
```

保存，重新运行 Samba 服务：
        
```
sudo /etc/init.d/samba restart
```

* 添加 pi 用户为 Samba 用户：

```
sudo smbpasswd -a pi
```

------

# 应用配置

## Homebridge

项目地址：

* [Homebridge](https://github.com/nfarina/homebridge)

* [Homebridge-Mi-Aqara](https://github.com/YinHangCode/homebridge-mi-aqara)

* [Homebridge-Yeelight](https://github.com/vvpossible/homebridge_yeelight)

* [Homebridge-Mi-Philips-Light](https://github.com/YinHangCode/homebridge-mi-philips-light)

1. 安装 NodeJS：

    ```
    curl -sL https://deb.nodesource.com/setup_7.x | sudo -E bash - 
    sudo apt-get install -y nodejs
    ```

2. 安装 Avahi：

    ```
    sudo apt-get install libavahi-compat-libdnssd-dev
    ```
        
3. 安装 Homebridge 本体和插件：

    ```
    sudo npm install -g --unsafe-perm homebridge # Stretch 需要加入 --unsafe-perm 参数   
    sudo npm install -g --unsafe-perm homebridge-mi-aqara # Aqara 平台插件，用于网关及其连接件      
    sudo npm install -g --unsafe-perm homebridge-yeelight # Yeelight 灯光系统插件
    sudo npm install -g --unsafe-perm homebridge-mi-philips-light # Philips 灯光系统插件
    ```

    安装完成后运行 `homebridge -D` 检查是否能正常运行。

4. 编辑配置文件：

    ```
    mkdir ~/.homebridge # 如果已经创建则跳过
    cd ~/.homebridge
    sudo nano config.json
    ```

    编辑配置文件如下：
    
    ```json
    {
        "bridge": {
            "name": "HomeBridge",
            "username": "B8:27:EB:EE:AF:1B",
            "port": 51888,
            "pin": "199-50-228"
        },
        "platforms": [{
            "platform": "MiAqaraPlatform",
            "gateways": {
                "34ce009525c9": "B51730D119334789"
            },
            "defaultValue": {
                "158d000227648c": {
                    "MotionSensor_MotionSensor": {
                        "name": "Motion Sensor"
                    }
                },
                "158d00019d3a9b": {
                    "Global": {
                        "disable": true 
                    },
                    "Button_StatelessProgrammableSwitch": {
                        "name": "Desktop Button",
                        "disable": false
                    },
                    "Button_Switch_VirtualSinglePress": {
                        "name": "Single Press"
                    },
                    "Button_Switch_VirtualDoublePress": {
                        "name": "Double Press"
                    }
                },
                "158d00019cb82c": {
                    "TemperatureAndHumiditySensor_TemperatureSensor": {
                        "name": "Temperature"
                    },
                    "TemperatureAndHumiditySensor_HumiditySensor": {
                        "name": "Humidity"
                    }
                }
            }
        },
            {
                "platform": "yeelight",
                "name": "Lightstrip"   
            },
            {
                "platform": "MiPhilipsLightPlatform",
                "deviceCfgs": [{
                "type": "MiPhilipsSmartBulb",
                "ip": "192.168.31.114",
                "token": "7b2c6803b67b99f9b7a250cd919858dc",
                "lightName": "Bulb",
                "lightDisable": false
            }]
            }
        ]
    }
    ```

    保存退出，试运行：`homebridge -D`

5. 使用 Screen 保持 Homebridge 在后台运行：

    ```
    sudo apt-get install screen
    ```

    运行 `screen -S homebdg`，在窗口里运行 Homebridge。

## Plex

### 安装 Plex

1. 更新 Raspbian：

    ```
    sudo apt-get update \
    sudo apt-get upgrade \
    sudo apt-get dist-upgrade
    ```

2. 重启树莓派：

    ```
    sudo reboot
    ```

3. 添加 Https 存储库功能：

    ```
    sudo apt-get install apt-transport-https
    ```

4. 添加 Plex Media Server 存储库：

    ```
    echo "deb https://dev2day.de/pms/ jessie main" | sudo tee /etc/apt/sources.list.d/pms.list
    ```

5. 为存储库添加 GPG 密钥：

    ```
    cd /tmp;
    wget https://dev2day.de/pms/dev2day-pms.gpg.key;
    ls;
    sudo apt-key add dev2day-pms.gpg.key
    ```

6. 回到根目录并更新源：

    ```
    cd ~ \
    sudo apt-get update
    ```

7. 安装 Plex Media Server：

    ```
    sudo apt-get install plexmediaserver-installer
    ```

8. 编辑配置文件：

    ```
    sudo nano /etc/default/plexmediaserver.prev
    ```

    修改如下：

    ```diff
    - PLEX_MEDIA_SERVER_USER=plex
    + PLEX_MEDIA_SERVER_USER=pi
    ```

9. 重启 Plex，重启树莓派：

    ```
    systemctl restart plexmediaserver;
    systemctl status plexmediaserver;
    sudo reboot
    ```

### 挂载外置硬盘

1. 查看硬盘安装位置：

    ```
    sudo cat /proc/mounts
    ```

2. 安装 NTFS 驱动器：

    ```
    sudo apt-get install ntfs-3g
    ```

3. 查询硬盘的`UUID`：

    ```
    ls -l /dev/disk/by-uuid
    ```

4. 创建新目录挂载硬盘：

    ```
    sudo mkdir /media/usb
    ```

5. 查询硬盘的`UID`和`GID`：

    ```
    id -u pi;
    id -g pi
    ```

6. 编辑`fstab`文件（用于设置驱动器）：

    ```
    sudo nano /etc/fstab
    ```

    最后一行按如下格式编辑：

    `UUID=[UUID] /media/usb auto nofail,uid=[UID],gid=[GID],noatime 0 0`

7. 弹出硬盘并重新挂载，重启树莓派：

    ```
    sudo umount [硬盘路径];
    sudo mount -a
    ```

    ```
    sudo reboot
    ```















