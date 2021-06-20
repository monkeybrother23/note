## 常用命令

### 新建仓库

```shell
# 在当前目录新建一个Git代码库
git init
# 下载一个git项目
git clone 项目url
```
### 配置
```shell
# 显示当前的git配置
git config --list

# 设置提交代码时的用户信息
git config [--global] user.name "[name]"
git config [--global] user.email "[email address]"
```
### 增加/删除
```shell
# 添加指定目录到暂存区
git add [file1] [file2] ...

# 提交所有变化 
git add -A   
# 提交被修改(modified)和被删除(deleted)文件，不包括新文件(new)  
git add -u
# 提交新文件(new)和被修改(modified)文件，不包括被删除(deleted)文件
git add .  
# 删除工作区文件，并且将这次删除放入暂存区
git rm [file1] [file2] ...

# 改名文件，并且将这个改名放入暂存区
git mv [file-old-name] [file-new-name]
```
###  提交
```shell
# 提交暂存区到仓库区
git commit -m [message]

# 提交时显示所有diff信息
git commit -v
```
### 分支
```shell
# 列出所有本地分支
git branch
# 列出所有远程分支
git branch -r
 # 列出所有本地分支和远程分支
git branch -a

# 新建一个分支，但依然停留在当前分支
git branch [branch-name]
# 新建一个分支，并切换到该分支
git checkout -b [branch]

# 切换到指定分支，并更新工作区
git checkout [branch-name]

# 删除分支
git branch -d [branch-name]
# 删除远程分支
git push origin --delete [branch-name]
```
### 查看信息
```shell
# 显示有变更的文件
git status

# 显示当前分支的版本历史
git log

# 显示commit历史，以及每次commit发生变更的文件
git log --stat
```
###  远程同步
```shell
# 下载远程仓库的所有变动
git fetch [remote]

# 显示所有远程仓库
git remote -v

# 增加一个新的远程仓库，并命名
git remote add [shortname] [url]

# 删除地址
git remote remove origin

# 上传本地指定分支到远程仓库
git push [remote] [branch]
```

### 回滚

```shell
git reset --hard HEAD^ 回退到上个版本
git reset --hard HEAD~3 回退到前3次提交之前，以此类推，回退到n次提交之前
git reset --hard commit_id 退到/进到 指定commit的sha码

强推到远程：
git push origin HEAD --force
```

### 新项目

```shell
git init
git remote add origin ****note.git
git commit -m "first commit"
git push -u origin master
```

