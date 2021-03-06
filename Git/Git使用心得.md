# Git使用心得

## Branch

### 重命名分支

```
git branch -m <oldname> <newname> # 重命名分支
git branch -m <newname> # 重命名当前分支
```

### 删除分支

```
git branch -D <branch_name>         # 删除本地分支    
git push <remote> :<remote_branch>  # 删除远程分支
```

## Commit

### 显示特定Commit改动

```
git show <commit_id>
```

## 暂存

把改动暂存，只需一条简单的命令既可：

```
git stash
```

这样，会把最近的改动缓存下来，回归最近一次Commit状态。
同时，用

```
git stash list
```

就可以查看目前该Repo的所有暂存列表。
要拉出暂存也很简单，只需用`apply`次级指令就可以把最近一次缓存取出来：

```
git stash apply
```

## 搜索信息

### 搜索相关Commit

```
git log -g --grep="任务"
```