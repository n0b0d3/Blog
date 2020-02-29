---
title: Git Stash
date: 2019-07-01 00:28:55
tags: Git
---

在切换分支前，又不希望提交此次更改时，可以使用 `git stash`。

#### git stash

保存当前工作进度，会把暂存区和工作区的改动保存起来。使用 `git stash save 'message...'` 可以添加一条注释。

*注： `git stash` 命令可以多次执行。*

#### git stash list

显示保存进度的列表

#### git stash pop [-index][stash_id]

- `git stash pop` 恢复最新的进度到工作区
- `git stash pop --index` 恢复最新的进度到工作区和暂存区
- `git stash pop stash@{1}` 恢复指定的进度到工作区

*注： `git stash` 会删除当前进度。*

#### git stash apply [index][stash_id]

不删除恢复的进度，其余和 `git stash pop` 命令一样。

#### git stash drop [stash_id]

删除一个储存进度。如果不指定 stash_id，默认删除最新的储存进度。

#### git stash clear

删除所有的储存进度。
