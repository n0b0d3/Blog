---
title: Docker 安装 openwrt
date: 2019-10-27 00:38:22
tags: Docker
---

#### 安装 docker

```
sudo pacman -S docker docker-compose
```

#### 替换 docker 源

```
vim /etc/docker/daemon.json
```

```
{
	"registry-mirrors": ["http://hub-mirror.c.163.com"]
}
```

#### 拉取镜像

```
docker pull kanshudj/n1-openwrtgateway:r9
```

#### 设置网络

```
docker network create -d macvlan --subnet=192.168.1.0/24 --gateway=192.168.1.1 -o parent=eth0 macnet
```

注：
- wlan0 虽然设置成功，openwrt 后面会运行失败，目前应该只支持 eth0

#### 运行 openwrt

```
docker run --restart always -d --network macnet --privileged kanshudj/n1-openwrtgateway:r9 /sbin/init
```

#### 修改网络配置

```
docker exec -it "container id" sh
```

修改网络配置：

```
config interface 'lan'
	option ifname 'eth0'
	option proto 'static'
	option ipaddr '192.168.1.124'
	option netmask '255.255.255.0'
	option gateway '192.168.1.1'
	option broadcast '192.168.1.255'
```

重启网络：

```
/etc/init.d/network restart
```

#### 登录 openwrt

Ip：192.168.1.124，上面配置的桥接静态地址
账号：root
密码：password
