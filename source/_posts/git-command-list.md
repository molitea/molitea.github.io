---
title: git 常用命令
tags: Git
---

常用git命令归纳：

| 命令                                                     | 说明                                                         |
| -------------------------------------------------------- | ------------------------------------------------------------ |
| git init                                                 | 初始化仓库                                                   |
| git add <file>                                           | 添加指定文件到暂存区                                         |
| git add .                                                | 添加所有文件到暂存区                                         |
| git commit -m "xxxx"                                     | 提交到仓库，git commit只会提交在暂存区中的内容               |
| git status                                               | 查看状态                                                     |
| git clone <url>                                          | 克隆仓库的当前分支                                           |
| git clone -b <branch-name> <url>                         | 克隆仓库的指定分支                                           |
| git diff                                                 | 查看修改的内容                                               |
| git log                                                  | 查看历史提交                                                 |
| git log --pretty=oneline                                 | 查看历史提交，并且输出信息在一行显示                         |
| git reset --hard HEAD^                                   | 回退到上一个版本，HEAD指向的版本就是当前版本，HEAD^ 当前版本的上一个版本 ，HEAD^^ 上上个版本 ，HEAD~100 当前往上100个版本 |
| git reset --hard <commit_id>                             | 回退到某个版本                                               |
| git reflog                                               | 查看历史提交记录                                             |
| git checkout -- <file>                                   | 恢复工作区文件的原始内容                                     |
| git checkout .                                           | 恢复工作区的所有文件                                         |
| git reset HEAD file                                      | 撤销暂存区的内容                                             |
| git rm file     git commit -m "xxxx"                     | 删除文件                                                     |
| git remote add origin git@server-name:path/repo-name.git | 关联远程库                                                   |
| git push -u origin master                                | 关联后，第一次推送master分支的所有内容                       |
| git push origin master                                   | 此后，每次推送到master分支                                   |
| git checkout -b xxxx  、git switch -c xxxx（新版本）     | 创建并切换到xxxx分支，git checkout -b xxxx 相当于两条命令：git branch xxxx、 git checkout xxxx（新版本git switch xxxx） |
| git branch                                               | 查看当前分支                                                 |
| git merge xxxx                                           | 合并指定分支到当前分支(Fast forward  模式) ，例如：合并xxxx分支到当前分支 |
| git branch -d xxxx                                       | 删除本地分支                                                 |
| git branch -D xxxx                                       | 强制删除本地分支                                             |
| git push origin --delete xxxx                            | 删除远程分支                                                 |
| git merge --no-ff -m "msg" xxxx                          | 合并分支时，加上--no-ff参数就可以用普通模式合并，合并后的历史有分支，能看出来曾经做过合并，而fast forward合并就看不出来曾经做过合并 |
| git pull <远程主机名> <远程分支名>:<本地分支名>          | 从远程获取代码并合并本地的版本                               |
| git pull origin master:brantest                          | 将远程主机 origin 的 master 分支拉取过来，与本地的 brantest 分支合并 |
| git pull origin master                                   | 如果远程分支是与当前分支合并，则冒号后面的部分可以省略       |