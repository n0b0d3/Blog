---
title: Docker 安装 nextcloud
date: 2019-12-09 00:33:46
tags: Docker
---

#### 替换 docker 源

```
vim /etc/docker/daemon.json
```

```
{
    "registry-mirrors": ["http://hub-mirror.c.163.com"]
}
```

#### 安装 mysql

安装 mysql

```
docker pull mysql:latest
docker run --name mysql -e MYSQL_ROOT_PASSWORD=password -d mysql:latest
```

修改 mysql 配置

```
ALTER USER 'nextcloud'@'localhost' IDENTIFIED BY 'password';
CREATE USER 'nextcloud'@'172.17.0.2' IDENTIFIED BY 'password';
GRANT ALL PRIVILEGES ON *.* TO 'nextcloud'@'172.17.0.2';
flush privileges;
ALTER USER 'nextcloud'@'172.17.0.2' IDENTIFIED WITH mysql_native_password BY 'password';
```

添加 db

```
CREATE DATABASE `nextcloud` CHARACTER SET utf8 COLLATE utf8_general_ci;
```

#### 安装 nextcloud

```
docker pull nextcloud:latest
docker run --name nextcloud -d -p 8080:80 -v /data:/var/www/html/data --link mysql:mysql nextcloud
```

登录 docker 中的 nextcloud

```
docker exec -u www-data -it 14d00e0784f5 /bin/bash
```

