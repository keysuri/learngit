### Git

###### 1. 初始化


```
git config --global user.name "Your Name"
git config --global user.email "email@example.com"

把这个目录变成Git可以管理的仓库
git init
```

###### 2. 提交文件

```
把文件添加到仓库暂存区
git add readme.txt

把暂存区的所有内容提交到当前分支
git commit -m "wrote a readme file"
```

###### 3. 状态信息查看

```
git status

查看修改内容
git diff
```
###### 4. 版本回退

```
HEAD指向的版本就是当前版本
各版本切换
git reset --hard commit_id

要回退到上一版本可以直接用
git reset --hard HEAD^

通过git log [--pretty=oneline]查看提交历史，其中可以查看到commit_id

回退后又要回到未来，用git reflog查看命令历史,找到id
```

###### 5. 撤销修改

```
git restore readme.txt
总之，就是让这个文件回到最近一次git commit或git add时的状态

git restore --staged readme.txt
把暂存区的修改撤销掉,重新放回工作区
```

情况1：在工作区做了修改，并未添加到暂存区，想撤销工作区的修改，用 git restore file;

情况2：在工作区做了修改，并用git add 添加到了暂存区，未提交；想撤销，分两步，1.先撤销暂存区的修改，用 git restore --staged file, 2.然后参考情况1撤销工作区的修改；

情况3：在工作区做了修改，且git add git commit添加并提交了内容，想撤销本次提交，直接用 git reset --hard HEAD^回退版本，即可保证工作区，暂存区，版本库都是上次的内容

###### 6. 删除文件

```
git rm test.txt 
相当于是删除工作目录中的test.txt文件,并把此次删除操作提交到了暂存区

git commit -m "remove test.txt"
提交删除操作，文件从版本库中删除了
```

删除也是一种修改，要想恢复，参考上一节的三种情况

###### 7. 远程仓库

```
本地关联远程仓库
git remote add origin git@github.com:keysuri/learngit.git

第一次推送master分支的所有内容
git push -u origin master

此后的提交
git push origin master

删除远程仓库(解除本地和远程的绑定关系)
先用git remote -v查看远程仓库信息
然后，根据名字删除git remote rm origin

多人协作
在本地创建和远程分支对应的分支，使用git switch -c branch-name origin/branch-name
建立本地分支和远程分支的关联，使用git branch --set-upstream branch-name origin/branch-name

首先，可以试图用git push origin <branch-name>推送自己的修改；
如果推送失败，则因为远程分支比你的本地更新，需要先用git pull试图合并；
如果合并有冲突，则解决冲突，并在本地提交；
没有冲突或者解决掉冲突后，再用git push origin <branch-name>推送就能成功！

```

###### 8. 分支管理

```
创建dev分支，然后切换到dev分支
git switch -c dev
等同于两条命令git branch dev + git switch dev

查看当前分支
git branch

再master分支上合并dev内容
git merge dev
(该模式为fast forward模式，删除分之后会丢掉分支信息)
git merge --no-ff -m "merge with no-ff" dev
表示普通合并，合并后的历史有分支，通过git log可以看出曾经做过合并

删除dev分支
git branch -d dev //要丢弃一个没有被合并过的分支，-D 强制删除

查看分支合并图
git log --graph --pretty=oneline --abbrev-commit
```

不同分支无法自动合并时需要先解决冲突，再提交，合并完成

解决冲突就是把Git合并失败的文件手动编辑为我们希望的内容，再提交。



BUG解决：

```
存储当前工作现场
git stash
------
切换分支完成其他任务
...
------
查看工作现场
git stash list

恢复工作现场
git stash apply  恢复后内容不删除，用git stash drop删除
git stash pop  恢复后同时删除stash内容

可以多次stash，恢复时恢复指定的stash
git stash apply stash@{0}

在master分支上修复的bug，想要合并到当前dev分支，可以用git cherry-pick <commit>命令，把bug提交的修改“复制”到当前分支，避免重复劳动。
```

###### 9. 标签管理

标签其实就是和某个commit绑定

```
在最新提交的commit上打标签
git tag v1.0

查看所有标签
git tag

在过去某一次提交上打标签
git log --pretty=oneline --abbrev-commit找到id
git tag v0.9 id号

创建带说明的标签，用-a指定标签名，-m指定说明文字
git tag -a v0.1 -m "version 0.1 released" 1094adb

查看标签详细信息
git show <tagname>

推送某个标签到远程
git push origin v1.0
或者，一次性推送全部尚未推送到远程的本地标签
git push origin --tags

删除标签
git tag -d v0.1
因为创建的标签都只存储在本地，不会自动推送到远程。所以，打错的标签可以在本地安全删除。
如果标签已经推送到远程,先从本地删除，再从远程删除
git tag -d v0.9
git push origin :refs/tags/v0.9
```

