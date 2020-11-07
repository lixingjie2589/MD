# Git   开淦

### Git简介
#### 安装Git
**Linux安装Git**
sudo apt-get install git
其他安装忽略！
**安装后设置名字和Email**
``` bash
$ git config --global user.name "Your Name"
$ git config --global user.email "email@example.com"
```
#### 创建版本库
``` bash
# 首先，选择一个合适的地方，创建一个空目录：
$ mkdir learngit
$ cd learngit
$ pwd
/Users/michael/learngit
# 第二步，通过git init命令把这个目录变成Git可以管理的仓库：
$ git init
Initialized empty Git repository in /Users/michael/learngit/.git/
```
**把文件添加到版本库**

``` bash
# 第一步，用命令git add告诉Git，把文件添加到仓库：
$ git add readme.txt
# 第二步，用命令git commit告诉Git，把文件提交到仓库：
# -m后面输入的是本次提交的说明
$ git commit -m "wrote a readme file"
[master (root-commit) eaadf4e] wrote a readme file
 1 file changed, 2 insertions(+)
 create mode 100644 readme.txt
```
### 时光穿梭机
#### 版本退回
**显示从最近到最远的提交日志  HEAD 表示当前版本**
```bash
$ git log
commit 48f79c1e80ba280261a53b9129d8919906024081 (HEAD -> master)
Author: lixingjie2589 <2111920436@qq.com>
Date:   Thu Oct 22 15:33:16 2020 +0800

    two
    
commit 19f9fe948eb8843bb590ff73dee7d908055ae92d
Author: lixingjie2589 <2111920436@qq.com>
Date:   Thu Oct 22 15:32:31 2020 +0800

    one

commit 96eeddb9863db5add26c241a15288010c0de0383
Author: lixingjie2589 <2111920436@qq.com>
Date:   Thu Oct 22 15:16:48 2020 +0800

    xxx
```
**回退到上一个版本**
```bash
# 上一个版本就是HEAD^，上上一个版本就是HEAD^^，当然往上100个版本写100个^比较容易数不过来，所以写成HEAD~100
$ git reset --hard HEAD^   或   git reset --hard commit_id
HEAD is now at 19f9fe9 one
```
**记录你的每一次命令**

```bash
$ git reflog
19f9fe9 (HEAD -> master) HEAD@{0}: reset: moving to HEAD^
48f79c1 HEAD@{1}: commit: two
19f9fe9 (HEAD -> master) HEAD@{2}: commit: one
96eeddb HEAD@{3}: commit (initial): xxx
```
![](./image/gitimage/image-20201022160202290.png)
#### 工作区和暂存区
**查看状态**
```bash
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	modified:   readme.txt

Untracked files:
  (use "git add <file>..." to include in what will be committed)

	readme2.txt

no changes added to commit (use "git add" and/or "git commit -a")
```
#### 管理修改
**查看工作区和版本库里面最新版本的区别**
```bash
$ git diff HEAD -- readme.txt 
diff --git a/readme.txt b/readme.txt
index 76d770f..a9c5755 100644
--- a/readme.txt
+++ b/readme.txt
@@ -1,4 +1,4 @@
 Git is a distributed version control system.
 Git is free software distributed under the GPL.
 Git has a mutable index called stage.
-Git tracks changes.
+Git tracks changes of files.
```
![](./image/gitimage/202010221639.png)

#### 撤销修改
**丢弃工作区的修改**

```bash
$ git checkout -- readme.txt
# 命令git checkout -- readme.txt意思就是，把readme.txt文件在工作区的修改全部撤销，这里有两种情况：
# 一种是readme.txt自修改后还没有被放到暂存区，现在，撤销修改就回到和版本库一模一样的状态；
# 一种是readme.txt已经添加到暂存区后，又作了修改，现在，撤销修改就回到添加到暂存区后的状态。
# 总之，就是让这个文件回到最近一次git commit或git add时的状态。
```
**把暂存区的修改撤销掉**
```bash
$ git reset HEAD readme.txt
Unstaged changes after reset:
M	readme.txt
```
![](./image/gitimage/微信图片_20201022174638.png)
#### 删除文件
**从版本库中删除文件**

```bash
$ git rm test.txt
rm 'test.txt'

$ git commit -m "remove test.txt"
[master d46f35e] remove test.txt
 1 file changed, 1 deletion(-)
 delete mode 100644 test.txt
```
**删错了**
```bash
$ git checkout -- test.txt
```
### 远程仓库
#### 添加远程库
先在GitHub创建一个仓库，在到本地仓库下运行命令：
```bahs
$ git remote add origin git@github.com:你的GitHub名/learngit.git
```
**推送**
```bash
$ git push -u origin master
Counting objects: 20, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (15/15), done.
Writing objects: 100% (20/20), 1.64 KiB | 560.00 KiB/s, done.
Total 20 (delta 5), reused 0 (delta 0)
remote: Resolving deltas: 100% (5/5), done.
To github.com:michaelliao/learngit.git
 * [new branch]      master -> master
Branch 'master' set up to track remote branch 'master' from 'origin'.
```
可能有SSH警告！！！
![](./image/gitimage/202010231043.png)

#### 从远程库克隆
先在GitHub创建一个仓库，然后在本地克隆：
```bahs
$ git clone git@github.com:michaelliao/gitskills.git
Cloning into 'gitskills'...
remote: Counting objects: 3, done.
remote: Total 3 (delta 0), reused 0 (delta 0), pack-reused 3
Receiving objects: 100% (3/3), done.
```
![](./image/gitimage/20201023105212.png)
### 分支管理
#### 创建与合并分支
**首先，我们创建dev分支，然后切换到dev分支：**
```bash
$ git checkout -b dev
Switched to a new branch 'dev'
```
**git checkout命令加上-b参数表示创建并切换，相当于以下两条命令：**
```bash
$ git branch dev
$ git checkout dev
Switched to branch 'dev'
```
**然后，用git branch命令查看当前分支：有星的表示当前分支 **
```bash
$ git branch
* dev
  master
```
删除远程分支

```bash
git push origin --delete 分支名
```

**git merge命令用于合并指定分支到当前分支**

```bash
$ git merge dev
Updating d46f35e..b17d20e
Fast-forward
 readme.txt | 1 +
 1 file changed, 1 insertion(+)
```
**更科学地切换分支**
```bash
# 创建并切换到新的dev分支，可以使用：
$ git switch -c dev
# 直接切换到已有的master分支，可以使用：
$ git switch master
```
![](./image/gitimage/QQ截图20201023112623.png)
#### 解决冲突
**合并**

```bash
$ git merge feature1
Auto-merging readme.txt
CONFLICT (content): Merge conflict in readme.txt
Automatic merge failed; fix conflicts and then commit the result.
```
Git告诉我们，readme.txt文件存在冲突，必须手动解决冲突后再提交。git status也可以告诉我们冲突的文件：
```bash
$ git status
On branch master
Your branch is ahead of 'origin/master' by 2 commits.
  (use "git push" to publish your local commits)

You have unmerged paths.
  (fix conflicts and run "git commit")
  (use "git merge --abort" to abort the merge)

Unmerged paths:
  (use "git add <file>..." to mark resolution)

	both modified:   readme.txt

no changes added to commit (use "git add" and/or "git commit -a")
```
我们可以直接查看readme.txt的内容：
```bahs
Git is a distributed version control system.
Git is free software distributed under the GPL.
Git has a mutable index called stage.
Git tracks changes of files.
<<<<<<< HEAD
Creating a new branch is quick & simple.
=======
Creating a new branch is quick AND simple.
>>>>>>> feature1
```
Git用<<<<<<<，=======，>>>>>>>标记出不同分支的内容
手动修改文件的冲突再提交就完成了！
![](./image/gitimage/202011051711.png)

#### 分支管理策略

合并`dev`分支，请注意`--no-ff`参数，表示禁用`Fast forward`

因为本次合并要创建一个新的commit，所以加上`-m`参数，把commit描述写进去。

```bash
$ git merge --no-ff -m "merge with no-ff" dev
Merge made by the 'recursive' strategy.
 readme.txt | 1 +
 1 file changed, 1 insertion(+)
```

**分支策略**

在实际开发中，我们应该按照几个基本原则进行分支管理：
首先，`master`分支应该是非常稳定的，也就是仅用来发布新版本，平时不能在上面干活；
那在哪干活呢？干活都在`dev`分支上，也就是说，`dev`分支是不稳定的，到某个时候，比如1.0版本发布时，再把`dev`分支合并到`master`上，在`master`分支发布1.0版本；
你和你的小伙伴们每个人都在`dev`分支上干活，每个人都有自己的分支，时不时地往`dev`分支上合并就可以了。

所以，团队合作的分支看起来就像这样：
![](.\image\gitimage\image-20201106102514871.png)

![](.\image\gitimage\20201106.png)

#### Bug分支

当需要修改一个bug时，在`dev`上进行的工作还没有提交

```bash
$ git status
On branch dev
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

	new file:   hello.py

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	modified:   readme.txt
```

如果工作只进行到一半，还没法提交，bug比较急，Git还提供了一个`stash`功能，可以把当前工作现场“储藏”起来，等以后恢复现场后继续工作：

```bash
$ git stash
Saved working directory and index state WIP on dev: f52c633 add merge
```

在用`git status`查看工作区，就是干净的

首先确定要在哪个分支上修复bug，假定需要在`master`分支上修复，就从`master`创建临时分支：

```bash
$ git checkout master
Switched to branch 'master'
Your branch is ahead of 'origin/master' by 6 commits.
  (use "git push" to publish your local commits)

$ git checkout -b issue-101
Switched to a new branch 'issue-101'
```

修改后提交

```bash
$ git add readme.txt 
$ git commit -m "fix bug 101"
[issue-101 4c805e2] fix bug 101
 1 file changed, 1 insertion(+), 1 deletion(-)
```

修复完成后，切换到`master`分支，并完成合并，最后删除`issue-101`分支：

```bash
$ git switch master
Switched to branch 'master'
Your branch is ahead of 'origin/master' by 6 commits.
  (use "git push" to publish your local commits)

$ git merge --no-ff -m "merged bug fix 101" issue-101
Merge made by the 'recursive' strategy.
 readme.txt | 2 +-
 1 file changed, 1 insertion(+), 1 deletion(-)
```

切换到`dev`分支，查看工作区时干净的，用`git stash list`命令查看：

```bash
$ git stash list
stash@{0}: WIP on dev: f52c633 add merge
```

工作现场还在，Git把stash内容存在某个地方了，但是需要恢复一下，有两个办法：

一是用`git stash apply`恢复，但是恢复后，stash内容并不删除，你需要用`git stash drop`来删除；

另一种方式是用`git stash pop`，恢复的同时把stash内容也删了：

```bash
$ git stash pop
On branch dev
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

	new file:   hello.py

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	modified:   readme.txt

Dropped refs/stash@{0} (5d677e2ee266f39ea296182fb2354265b91b3b2a)
```

再用`git stash list`查看，就看不到任何stash内容了

你可以多次stash，恢复的时候，先用`git stash list`查看，然后恢复指定的stash，用命令：

```bash
$ git stash apply stash@{0}
```

到dev分支工作并把主分支修改好的bug提交过来，为了方便操作，Git专门提供了一个`cherry-pick`命令，让我们能复制一个特定的提交到当前分支：

```bash
$ git branch
* dev
  master
$ git cherry-pick 4c805e2
[master 1d4b803] fix bug 101
 1 file changed, 1 insertion(+), 1 deletion(-)
```

也可以在dev分支 修改bug，避免上面的操作。

![image-20201106142312514](.\image\gitimage\image-20201106142312514.png)

#### Feature分支

开发一个新feature，最好新建一个分支；

如果要丢弃一个没有被合并过的分支，可以通过`git branch -D <name>`强行删除。

#### 多人协作

#### Rebase
### 标签管理

Git标签是一个版本库的快照，就是指向某一个`commit`的指针，可以快速的找到某个版本的`commit`

#### 创建标签

命令`git tag <name>`就可以打一个新标签：

```bash
$ git tag v1.0
```

命令`git tag`查看所有标签：

```bash
$ git tag
v1.0
```

查看历史提交的commit id

```bash
$ git log --pretty=oneline --abbrev-commit
12a631b (HEAD -> master, tag: v1.0, origin/master) merged bug fix 101
4c805e2 fix bug 101
e1e9c68 merge with no-ff
f52c633 add merge
cf810e4 conflict fixed
5dc6824 & simple
14096d0 AND simple
b17d20e branch test
d46f35e remove test.txt
b84166e add test.txt
519219b git tracks changes
e43a48b understand how stage works
1094adb append GPL
e475afc add distributed
eaadf4e wrote a readme file
```

给指定的commit打标签

```bash
$ git tag v0.9 f52c633
```

注意，标签不是按时间顺序列出，而是按字母排序的。可以用`git show <tagname>`查看标签信息：

```bash
$ git show v0.9
commit f52c63349bc3c1593499807e5c8e972b82c8f286 (tag: v0.9)
Author: Michael Liao <askxuefeng@gmail.com>
Date:   Fri May 18 21:56:54 2018 +0800

    add merge

diff --git a/readme.txt b/readme.txt
...
```

还可以创建带有说明的标签，用`-a`指定标签名，`-m`指定说明文字：

```bash
$ git tag -a v0.1 -m "version 0.1 released" 1094adb
```

![](.\image\gitimage\20201107.png)

#### 操作标签

删除标签

```bash
$ git tag -d v0.1
Deleted tag 'v0.1' (was f15b0dd)
```

推送某个标签到远程，使用命令`git push origin <tagname>`：

```bash
$ git push origin v1.0
Total 0 (delta 0), reused 0 (delta 0)
To github.com:michaelliao/learngit.git
 * [new tag]         v1.0 -> v1.0
```

一次性推送全部尚未推送到远程的本地标签：

```bash
$ git push origin --tags
Total 0 (delta 0), reused 0 (delta 0)
To github.com:michaelliao/learngit.git
 * [new tag]         v0.9 -> v0.9
```

如果标签已经推送到远程先从本地删除,然后，从远程删除：

```bash
$ git tag -d v0.9
Deleted tag 'v0.9' (was f52c633)

$ git push origin :refs/tags/v0.9
To github.com:michaelliao/learngit.git
 - [deleted]         v0.9
```

![image-20201107145906461](.\image\gitimage\202011071459.png)

### 自定义Git

让Git显示颜色，会让命令输出看起来更醒目：

```bash
$ git config --global color.ui true
```

#### 忽略特殊文件

#### 配置别名

#### 搭建Git服务器

