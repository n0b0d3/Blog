---
title: Git 打标签
date: 2019-03-01 00:23:12
tags: Git
---

#### 列出本地标签

```
git tag
```

#### 新建 tag

```
git tag <tag 名字>
```

#### 删除本地 tag

```
git tag -d <tag 名字>
```

#### 推送 tag 到远程分支

```
git push origin <tag 名字>
or
git push upstream <tag 名字>
```

#### 删除远程标签

```
git push origin :refs/tags/标签名
or
git push origin :refs/tags/protobuf-2.5.0rc1
```

