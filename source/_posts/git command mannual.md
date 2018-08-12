---
title: git命令手册
---

## 基本命令
```
##查看用户名和邮箱地址
git config user.name
git config user.email

##修改····
git config --global user.name "username"
git config --global user.email "email"

##查看仓库当前状态
git status

##查看当前分支
git branch
```
## 版本控制

* 创建版本库

```terminal
mkdir learngit
cd learngit
pwd
##把这个目录变成git可以管理的库
git init
```

* 添加文件到仓库

```terminal
##分两步
git add filename
git commit -m "本次提交的说明"
```
ps:可以`git add`多个次，最后一次性`git commit`

* 比较差别
`git diff filename`

* 查看日志

```
git log --pretty=oneline

##查看分支合并图
git log --graph
```

* 版本回退

```terminal
git reset --hard HEAD^
##想回到最新的版本
git reset --hard commitId（具体版本号）
##查看版本号
git reflog
```
`HEAD`表示当前版本， `HEAD^`表示上一个版本，`HEAD^^`表示上上一个版本，`HEAD~100`表示上100个版本 

* 撤销修改

```
##撤销在工作区的修改
git checkout -- filename

##如果已经git add到缓冲区
git reset HEAD filename
#再
git checkout --filename
```
`git reset`命令既可以回退版本，也可以把暂存区的修改回退到工作区。当我们用`HEAD`时，表示最新的版本。

conclusion:
场景1：当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，用命令`git checkout -- file`。

场景2：当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，第一步用命令`git reset HEAD file`，就回到了场景1，第二步按场景1操作。

场景3：已经提交了不合适的修改到版本库时，想要撤销本次提交，参考版本回退一节，不过前提是没有推送到远程库。

### 删除文件
当工作区中的文件删除后，会导致工作区和版本库不一致，有两种解决方案
1、确定要删除文件，则把版本库中的文件也删除。

```terminal
git rm filename
git commit
```
2、删错了，想要还原文件:
`git check -- filename`


## 远程仓库(github)
1. 创建ssh公钥
2. 在github上创建一个仓库，名为`learngit`
3. 把本地仓库与github上的仓库关联起来

```
git remote origin git@github.com:houchenyu/learngit.git
```
4 把本地库中的内容推送到远程库上

```terminal
##第一次推送想要加 -u
git push -u origin master
##之后就不需要了
git push origin master
```

5 从远程仓库克隆到本地

```
git clone git@github.com:michaelliao/gitskills.git(远程仓库地址)
```

## 分支
* 创建分支
`git branch branchName`，例如`git branch dev`

* 切换分支
`git checkout dev`

* 创建并切换分支
`git checkout -b dev`

* 合并某分支到当前分支

`git merge <name>`
注意：默认会使用fast-forward方式，直接把当前分支移到<name>分支上
`git merge --no-ff -m "说明的信息" <name>`
使用`--no-ff`参数可以禁用fast-forward，合并后会闯进一个新的commit，合并后的历史有分支，能看出来曾经做过合并，而fast forward 合并看不出来。

* 删除分支
`git branch -d <name>`

### Bug分支
修复bug时，我们会通过创建新的bug分支进行修复，然后合并，最后删除；

当手头工作没有完成时，先把工作现场`git stash`一下，然后去修复bug，修复后，再`git stash pop`，回到工作现场。

```
git stash
```
可以把当前工作现场“储藏”起来，等以后恢复现场后继续工作。此时可以切换到其他分支上去干其他事情。当回到此分之后，可以利用`git stash list`查看保存的工作现场。

恢复现场:

```
##先恢复
git stash apply <stash number>
##再删除
git stash drop <stash number>

##或者直接恢复并删除最近的
git stash pop
```


