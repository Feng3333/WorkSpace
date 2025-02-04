# git的使用

# 目录
 - [获取公钥](#获取公钥)
 - [基本操作](#基本操作)
   - [使用vscode时git pull和git push每次都需输入用户名和密码解决方法](#使用-vscode-时-git-pull-和-git-push每次都需输入用户名和密码解决方法)
   - [提交代码到远程分支的基本步骤](#提交代码到远程分支的基本步骤)
   - [add](#add)
   - [add回退](#add回退)
   - [commit](#commit)
   - [commit回退](#commit回退)
   - [git merge](#git-merge)
   - [git branch](#git-branch)
   - [git checkout](#git-checkout)
   - [git log](#git-log)
   - [git pull](#git-pull)
   - [git fetch](#git-fetch)
   - [git远端代码强制覆盖本地代码](#git远端代码强制覆盖本地代码)
 - [stash : 能够将还未 commit 的代码存起来](#stash--能够将还未-commit-的代码存起来)
 - [reset --soft : 回退你已提交的 commit，并将 commit 的修改内容放回到暂存区](#reset---soft--回退你已提交的-commit并将-commit-的修改内容放回到暂存区)
 - [reset --hard 回滚到某个版本,及这个版本及之后的commit全部取消](#reset---hard-回滚到某个版本及这个版本及之后的commit全部取消)
 - [cherry-pick : 将已经提交的 commit，复制出新的 commit 应用到分支里](#cherry-pick--将已经提交的-commit复制出新的-commit-应用到分支里)
   - [应用场景](#应用场景)
   - [命令使用](#命令使用)
   - [cherry-pick代码冲突](#cherry-pick代码冲突)
 - [revert : 将现有的提交还原，恢复提交的内容，并生成一条还原记录](#revert--将现有的提交还原恢复提交的内容并生成一条还原记录)
 - [reflog : 记录了所有的 commit 操作记录，便于错误操作后找回记录](#reflog--记录了所有的-commit-操作记录便于错误操作后找回记录)
 - [git忽视文件](#git忽视文件)
 - [git上传大文件](#git上传大文件)
 - [统计仓库代码量](#统计仓库代码量)
 - [设置git短命令](#设置git短命令)
 - [](#)

## 获取公钥
```
//全局配置 Git 的用户名和邮箱
git config --global user.name "f30040931"
git config --global user.email "fengwenwei2@huawei.com"

//生成 ssh key (一路回车确认不要设置密码)；
ssh-keygen -t rsa -C "fengwenwei2@huawei.com"

//获取ssh key；
cat ~/.ssh/id_rsa.pub 
```

## 基本操作
### 使用方式
```
git gui    # 进入图形化界面
git bash   # 命令行的方式
```

```
# 初始化git仓库，会在当前目录生成一个隐藏文件夹 .git 该文件夹无需修改
git init

# 查看git的状态
git status

# 查看提交log
git log
```

### 使用 vscode 时 git pull 和 git push每次都需输入用户名和密码解决方法
```
git config --global credential.helper store
```
输入上述命令后重启vscode

### 提交代码到远程分支的基本步骤
```
git add 需要提交的文件路径
git commit -m "说明"
git commit --amend (编写标准) // 写好后退出：ctrl+o  ctrl+x
git push origin HEAD:（具体分支名字）
```

### add
```
# 添加某个文件到暂存区，后面可以跟多个文件，以空格区分
git add xxx

# 添加当前更改的所有文件到暂存区。
git add .
git add --all
git add -A

# 添加当前目录下的所有后缀为 .py的文件
git add *.py
```

### add回退
将所有暂存区的文件撤回到工作区
```
git reset HEAD
```
将某个文件撤回到工作区
```
git reset HEAD <文件名>
```

### commit
```
// 提交暂存的更改，会新开编辑器进行编辑
git commit 

// 提交暂存的更改，并记录下备注
git commit -m "you message"

// 等同于 git add . && git commit -m
git commit -am

// 对最近一次的提交的信息进行修改,此操作会修改commit的hash值
git commit --amend
```

### commit回退 
撤销提交但仍然保留commit之前的修改（将暂存区最近一次提交到git目录的文件全部撤回到暂存区）
```
git reset --soft HEAD^
```
将 git commit 和 git add 一并撤回
```
git reset --mixed HEAD^
//等同于该命令
git reset --soft HEAD^ && git reset HEAD
```
不想保留最近一次提交的所有修改
```
git reset --hard HEAD^
```

### git merge
```
// 将dev的分支合并到当前分支
git merge dev
```

### git branch
```
// 新建本地分支，但不切换
git branch <branch-name> 

// 查看本地分支
git branch

// 查看远程分支
git branch -r

// 查看本地和远程分支
git branch -a

// 删除本地分支
git branch -D <branch-name>

// 重新命名分支
git branch -m <old-branch-name> <new-branch-name>
```
### git checkout
```
# 切换分支
git checkout origin/分支名

# 切换分支并在本地重命名
git checkout -b 本地分支名 origin/远程分支名。
```
将暂存区的内容恢复到工作区
```
git checkout 1.txt(filename)
```

### git log
查看提交日志
```
git log            # 查看当前head以及以前的日志
git log --oneline  # 简洁的日志信息
git reflog         # 查看所有的提交变更日志
```

### git pull
```
// 从远程仓库拉取代码并合并到本地，可简写为 git pull 等同于 git fetch && git merge 
git pull <远程主机名> <远程分支名>:<本地分支名> 

// 使用rebase的模式进行合并
git pull --rebase <远程主机名> <远程分支名>:<本地分支名>
```

### git fetch
git fetch操作仅仅只会拉取远程的更改，不会自动进行 merge 操作。对你当前的代码没有影响
```
// 获取远程仓库特定分支的更新
git fetch <远程主机名> <分支名>
// 获取远程仓库所有分支的更新
git fetch --all
```
### git远端代码强制覆盖本地代码
```
git fetch --all
git reset --hard origin/分支名称 
git pull
```

## stash : 能够将还未 commit 的代码存起来
```
// 保存当前未commit的代码
git stash

// 保存当前未commit的代码并添加备注
git stash save "备注的内容"

// 列出stash的所有记录
git stash list

// 删除stash的所有记录
git stash clear

// 应用最近一次的stash
git stash apply

// 应用最近一次的stash，随后删除该记录
git stash pop

// 删除最近的一次stash
git stash drop

// 当有多条 stash，可以指定操作stash，首先使用stash list 列出所有记录：
git stash list
stash@{0}: WIP on ...
stash@{1}: WIP on ...
stash@{2}: On ...
//使用第二条记录
$ git stash apply stash@{1}
```

## reset --soft : 回退你已提交的 commit，并将 commit 的修改内容放回到暂存区
```
# 恢复最近一次 commit
git reset --soft HEAD^

# 恢复最近两次，三次 commit
git reset --soft HEAD~2
在 使用reset --soft 指定 commit 号时，会将该 commit 到最近一次 commit 的所有修改内容全部恢复，而不是只针对该 commit 
```

## reset --hard 回滚到某个版本,及这个版本及之后的commit全部取消
```
# 回滚、取消之前的提交
git reset --hard commit_id
```
```
git reset --hard HEAD~1
# ~1:上一次提交
# ~2:上上次提交
# ~0:当前提交
```


## cherry-pick : 将已经提交的 commit，复制出新的 commit 应用到分支里
### 应用场景
应用场景1：有时候版本的一些优化需求开发到一半，可能其中某一个开发完的需求要临时上，或者某些原因导致待开发的需求卡住了已开发完成的需求上线。这时候就需要把 commit 抽出来，单独处理。  
应用场景2：有时候开发分支中的代码记录被污染了，导致开发分支合到线上分支有问题，这时就需要拉一条干净的开发分支，再从旧的开发分支中，把 commit 复制到新分支  

### 命令使用
```
// 在某一分支下
git log
//将需要的commit的commitHash复制下来
//切换到另一个分支
git cherry-pick 刚刚复制下来的commitHash

//复制多个
git cherry-pick commitHash1 commitHash2

//区间复制  将commitHash1 到 commitHash2 这个区间的 commit 
都应用到当前分支（包含commitHash1、commitHash2），commitHash1 是最早的提交。
git cherry-pick commitHash1^..commitHash2
```
### cherry-pick代码冲突
```
//继续流程
git cherry-pick --continue

//放弃cherry-pick流程
gits cherry-pick --abort

//退出cherry-pick (回到cherry-pick操作之前的版本，就当无事发生)
git cherry-pick quit
```

## revert : 将现有的提交还原，恢复提交的内容，并生成一条还原记录
### revert 普通提交
```
git revert commitHash
```
 revert 会生成一条新的提交记录，这时会让你编辑提交信息，编辑完后 :wq 保存退出就好了  
之后查看log可以看到生成了revert记录  

### revert 合并提交
```
//-m 后面要跟一个 parent number 标识出"主线"，一般使用 1 保留主分支代码。
git revert -m 1 <commitHash>
```
revert 合并提交后，再次合并分支会失效    
使用 revert 后， feature 分支的 commit 还是会保留在 master 分支的记录中，当再次合并进去时，git 判断有相同的 commitHash，就忽略了相关 commit 修改的内容。 这时就需要 revert 掉之前 revert 的合并提交

## reflog : 记录了所有的 commit 操作记录，便于错误操作后找回记录
```
git reflog
```

## git忽视文件
- 在仓库的根目录创建一个 ```.gitignore``` 的文件，文件名是固定的
- 可以将不需要被git管理的文件路径添加到```.gitignore```中
```
# 忽视 .gitignore 文件
.gitignore

# 忽视 css/index.js 下的 index.js 文件
css/index.js

# 忽视 css 目录下的所有 .js 文件
css/*.js

# 忽视 css 目录下的所有文件
css/*.*

# 忽视 filedir 文件夹
filedir
```

## git上传大文件
1. 使用lfs来上传大文件
- 安装LFS
```
git lfs install
```
- 跟踪大文件
```
# 跟踪所有的`.mp4`文件
git lfs track "*.mp4"
```
- 确认大文件已经被git lfs跟踪之后, 再使用git add 和git commit 来提交文件

## 统计仓库代码量
```
git log --author="yourname" --pretty=tformat: --numstat | awk '{ add += $1; subs += $2; loc += $1 - $2 } END { printf "added lines: %s, removed lines: %s, total lines: %sn", add, subs, loc }' -
```

## 设置git短命令 
```
#方法一 
//例子:将push用ps代替
git config --global alias.ps push

#方式二
vim ~/.gitconfig
//写入内容
[alias] 
        co = checkout
        ps = push
        pl = pull
        mer = merge --no-ff
        cp = cherry-pick
#使用
//等同于 git cherry-pick <commitHash>
git cp <commitHash>
```
















                                                                    ----End----
