---
title: Git 拆分文件夹作为独立仓库
date: 2019-09-17 00:14:52
tags: Git
---

创建临时文件夹作为仓库临时存储

#### Clone 整个源仓库到 .git

```sh
git clone --bare https://github.com/somecode.git .git
```

#### 将所有远程分支全部变为本地分支

```
git config --unset core.bare
git reset --hard
```

#### 拆分源仓库下单独文件夹作为独立仓库

```
git filter-branch --prune-empty --subdirectory-filter back_directory -- --all
```

注：
- back_directory 是源仓库中的一个文件夹

#### 创建个人远程仓库用于存放拆分后的内容

#### 修改 remote 名称

```
git remote rename origin old_origin
```

#### 添加远程仓库 remote

```
git remote add origin http://github.com/somecode.git
```

#### 推送本地分支及 tag

```
git push -u origin --all
git push -u origin --tags
```
