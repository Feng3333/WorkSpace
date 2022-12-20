## 获取公钥 ：
```
//全局配置 Git 的用户名和邮箱
git config --global user.name "f30040931"
git config --global user.email "fengwenwei2@huawei.com"

//生成 ssh key (一路回车确认不要设置密码)；
ssh-keygen -t rsa -C "fengwenwei2@huawei.com"

//获取ssh key；
cat ~/.ssh/id_rsa.pub 
```

## 基本操作 ：
### 提交代码到远程分支的基本步骤
```
git add 需要提交的文件路径
git commit -m "说明"
git commit --amend (编写标准) // 写好后退出：ctrl+o  ctrl+x
git push origin HEAD:（具体分支名字）
```

### add
```
// 添加某个文件到暂存区，后面可以跟多个文件，以空格区分
git add xxx

// 添加当前更改的所有文件到暂存区。
git add .
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
//切换分支
git checkout origin/分支名

//切换分支并在本地重命名
git checkout -b 本地分支名 origin/远程分支名。
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
// 恢复最近一次 commit
git reset --soft HEAD^

//恢复最近两次，三次 commit
git reset --soft HEAD~2
在 使用reset --soft 指定 commit 号时，会将该 commit 到最近一次 commit 的所有修改内容全部恢复，而不是只针对该 commit 
```

## reset --hard 回滚到某个版本,及这个版本及之后的commit全部取消。
```
// 回滚、取消之前的提交
git reset --hard commit_id
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

## reflog : 记录了所有的 commit 操作记录，便于错误操作后找回记录。
```
git reflog
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
