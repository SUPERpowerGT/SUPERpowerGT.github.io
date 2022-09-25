---
layout: default
title: Git学习笔记
description: 参考廖雪峰先生的教程
---

## Git学习笔记





# Git（廖雪峰板）

前言：

感谢廖雪峰老师的git教程，让我打开了新世界的大门，能更好的学习掌握专业相关知识

### 代码总结（方便查看）：

- $ git config --list --show-origin
  查看所有设置以及它们的来源

- $ mkdir learngit
  创建新的文件夹

- $ cd learngit
  进入当前文件夹目录

- $ pwd
  显示当前文件夹目录

- $ git init
  赋予当前目录git权限

- $ git add readme.txt
  把当前目录或者子目录下的文件添加给git管理
  $ git commit -m "wrote a readme file"
  并将其提交给仓库中，m后面添加注释

  PS：提交给git管理一共需要两个步骤add commit
  add只能依次添加文件，commmit可以将所有已经添加的文件上传到仓库中

- $ git status

  查看工作区当前状态，有无修改，有无已添加未提交的文件

- $ git diff

  要随时掌握工作区的状态，使用`git status`命令。

  如果`git status`告诉你有文件被修改过，用`git diff`可以查看修改内容。

- $ git log

  查看我们版本提交的历史纪录，显示从最近到最远的提交日志

- $ git reset --hard HEAD^

  实现不同历史版本的穿梭，HEAD是当前版面本

- $ cat readme.txt

  用来查看文本内容

- $ git reflog

  用来查看命令历史，知道对应命令的代号，来前往未来版本

- $ git checkout -- readme.txt

  用来清除工作区的修改，返回到最近一次`git commit`或`git add`时的状态。不论是添加还是修改哦

- git reset HEAD readme.txt

  可以把暂存区的修改撤销掉（unstage），重新放回工作区

- git remote **remove** origin

  取消远程关联

- git remote add origin git https://github.com:SUPERpowerGT/learngit.git

  关联本地仓库和远程仓库

- git remote -v 

  确认自己git拉取代码的方式

- git config --global http.proxy [http://127.0.0.1:7890](http://127.0.0.1:7890/)（自己代理的端口号）

  git config --global https.proxy [https://127.0.0.1:7890](https://127.0.0.1:7890/)（自己代理的端口号）

  重新配置http端口号

- $ git push -u origin main

  将本地库上传到远程仓库中

- $ git clone gitgit clone https://github.com/SUPERpowerGT/gitskills.git

  克隆远程仓库到本地

- git branch

  查看分支

- git branch <name>

  创建分支

- git checkout <name> git switch <name>

  切换分支

- git checkout -b <name>git switch -c <name>

  创建+切换分支

- git merge <name>

  合并某分支到当前分支

- git branch -d <name>

- 删除分支

### git简介

git：分布式版本控制系统

简单理解就是，该软件能在协作管理软件更新有奇效，方便不同人对同一软件修改更新版本迭代，同时能查看别人的更改相关信息

![image-20220924150808266](C:\Users\XZY\AppData\Roaming\Typora\typora-user-images\image-20220924150808266.png)

### 集中式和分布式

集中式：SVN，CVS，ClearCase

版本库储在中央服务器中，其他人需要从中央处理器调用版本更改，并重新推送给中央服务器

![image-20220924152401342](C:\Users\XZY\AppData\Roaming\Typora\typora-user-images\image-20220924152401342.png)

缺点：联网，上传，下载速度堪忧

分布式：Git

每个人电脑上都存储一个版本库，如何协作？当两个人修改各自电脑的版本，只需要把自己修改的地方推送给对方就OK了

优点：方便快捷安全

### 设置git

git config语句来配置外观操作方式的所有方面的配置变量

您可以使用以下方式查看所有设置以及它们的来源：

```console
$ git config --list --show-origin
```

![image-20220924153913587](C:\Users\XZY\AppData\Roaming\Typora\typora-user-images\image-20220924153913587.png)

我们还需要注册身份信息

```console
$ git config --global user.name "John Doe"
$ git config --global user.email johndoe@example.com
```

### 创建版本库

**repository**可以理解为目录，目录文件记载每一次的更新迭代，同时可以被git管理起来。可以查看修改历史并复原

创建版本库：

```
$ mkdir learngit
$ cd learngit
$ pwd
/Users/michael/learngit
```

![image-20220924155303544](C:\Users\XZY\AppData\Roaming\Typora\typora-user-images\image-20220924155303544.png)

赋予git管理权限：

```
$ git init
Initialized empty Git repository in /Users/michael/learngit/.git/
```

ls -ah可以查看到隐藏的git目录（不要修改）

![image-20220924155326404](C:\Users\XZY\AppData\Roaming\Typora\typora-user-images\image-20220924155326404.png)

### 把文件添加到版本库

这里需要明确的一点就是所有的版本控制系统，只能跟踪文本文件的改动，而音频视频图片等二进制文件是只能记录更改大小的，无法记录具体更改细节的，不幸的是，微软word恰恰是二进制的形式，所我们是无法对word文件进行改动的，需要进行纯文本的方式编写文件，强烈建议使用标准的UTF-8编码，因为其具有更高的普遍性

编写一个txt文件在库中

![image-20220924155916632](C:\Users\XZY\AppData\Roaming\Typora\typora-user-images\image-20220924155916632.png)

![image-20220924155930309](C:\Users\XZY\AppData\Roaming\Typora\typora-user-images\image-20220924155930309.png)

注意一定要放在learngit目录下或者子目录

接着我们把它添加到git 仓库中

```
$ git add readme.txt
```

![image-20220924160124563](C:\Users\XZY\AppData\Roaming\Typora\typora-user-images\image-20220924160124563.png)

利用git commit命令告诉git 把文件提交到仓库当中

```
$ git commit -m "wrote a readme file"
```

简单解释一下`git commit`命令，`-m`后面输入的是本次提交的说明，可以输入任意内容，当然最好是有意义的，这样你就能从历史记录里方便地找到改动记录。

![image-20220924160331466](C:\Users\XZY\AppData\Roaming\Typora\typora-user-images\image-20220924160331466.png)

`git commit`命令执行成功后会告诉你，`1 file changed`：1个文件被改动（我们新添加的readme.txt文件）；`2 insertions`：插入了两行内容（readme.txt有两行内容）。

为什么Git添加文件需要`add`，`commit`一共两步呢？因为`commit`可以一次提交很多文件，所以你可以多次`add`不同的文件，比如：

```
$ git add file1.txt
$ git add file2.txt file3.txt
$ git commit -m "add 3 files."
```

### 对版本库进行修改

我们已经成功地添加并提交了一个readme.txt文件，现在，是时候继续工作了，于是，我们继续修改readme.txt文件，改成如下内容：

```
Git is a distributed version control system.
Git is free software.
```

![image-20220924160621707](C:\Users\XZY\AppData\Roaming\Typora\typora-user-images\image-20220924160621707.png)

现在，运行`git status`命令看看结果：

```
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	modified:   readme.txt

no changes added to commit (use "git add" and/or "git commit -a")
```

`git status`命令可以让我们时刻掌握仓库当前的状态，上面的命令输出告诉我们，`readme.txt`被修改过了，但还没有准备提交的修改。

虽然Git告诉我们`readme.txt`被修改了，但如果能看看具体修改了什么内容，自然是很好的。比如你休假两周从国外回来，第一天上班时，已经记不清上次怎么修改的`readme.txt`，所以，需要用`git diff`这个命令看看：

![image-20220924160736791](C:\Users\XZY\AppData\Roaming\Typora\typora-user-images\image-20220924160736791.png)

`git diff`顾名思义就是查看difference，显示的格式正是Unix通用的diff格式，可以从上面的命令输出看到，我们在第一行添加了一个`distributed`单词。

知道了对`readme.txt`作了什么修改后，再把它提交到仓库就放心多了，提交修改和提交新文件是一样的两步，第一步是`git add`：

```
$ git add readme.txt
```

同样没有任何输出。在执行第二步`git commit`之前，我们再运行`git status`看看当前仓库的状态：

![image-20220924160907381](C:\Users\XZY\AppData\Roaming\Typora\typora-user-images\image-20220924160907381.png)

`git status`告诉我们，将要被提交的修改包括`readme.txt`，下一步，就可以放心地提交了：

```
$ git commit -m "add distributed"
[master e475afc] add distributed
 1 file changed, 1 insertion(+), 1 deletion(-)
```

提交后，我们再用`git status`命令看看仓库的当前状态：

```
$ git status
On branch master
nothing to commit, working tree clean
```

- 要随时掌握工作区的状态，使用`git status`命令。
- 如果`git status`告诉你有文件被修改过，用`git diff`可以查看修改内容。

### 版本回退

我们再对文件进行修改并提交

```
Git is a distributed version control system.
Git is free software distributed under the GPL.
```

```
$ git add readme.txt
$ git commit -m "append GPL"
[master 1094adb] append GPL
 1 file changed, 1 insertion(+), 1 deletion(-)
```

我们可以用git log来查看历史纪录：命令显示从最近到最远的提交日志

![image-20220924162930854](C:\Users\XZY\AppData\Roaming\Typora\typora-user-images\image-20220924162930854.png)

在Git中，用`HEAD`表示当前版本

现在，我们要把当前版本`append GPL`回退到上一个版本`add distributed`，就可以使用`git reset`命令：

```
$ git reset --hard HEAD^
HEAD is now at e475afc add distributed
```

`--hard`参数有啥意义？这个后面再讲，现在你先放心使用。

看看`readme.txt`的内容是不是版本`add distributed`：

![image-20220924163503883](C:\Users\XZY\AppData\Roaming\Typora\typora-user-images\image-20220924163503883.png)

![image-20220924163515986](C:\Users\XZY\AppData\Roaming\Typora\typora-user-images\image-20220924163515986.png)

amazing！！！

```
$ cat readme.txt
Git is a distributed version control system.
Git is free software.
```

![image-20220924163955919](C:\Users\XZY\AppData\Roaming\Typora\typora-user-images\image-20220924163955919.png)

还可以继续回退到上一个版本`wrote a readme file`，不过且慢，让我们用`git log`再看看现在版本库的状态：

```
$ git log
commit e475afc93c209a690c39c13a46716e8fa000c366 (HEAD -> master)
Author: Michael Liao <askxuefeng@gmail.com>
Date:   Fri May 18 21:03:36 2018 +0800

    add distributed

commit eaadf4e385e865d25c48e7ca9c8395c3f7dfaef0
Author: Michael Liao <askxuefeng@gmail.com>
Date:   Fri May 18 20:59:18 2018 +0800

    wrote a readme file
```

最新的那个版本`append GPL`已经看不到了！

只要上面的命令行窗口还没有被关掉，你就可以顺着往上找啊找啊，找到那个`append GPL`的`commit id`是`1094adb...`，于是就可以指定回到未来的某个版本：

```
$ git reset --hard 1094a
HEAD is now at 83b0afe append GPL
```

![image-20220924164053201](C:\Users\XZY\AppData\Roaming\Typora\typora-user-images\image-20220924164053201.png)

git是用C语言写的，版本的变化只是取决于指针的修改

顺便把工作区的文件更新

现在，你回退到了某个版本，关掉了电脑，第二天早上就后悔了，想恢复到新版本怎么办？找不到新版本的`commit id`怎么办？

在Git中，总是有后悔药可以吃的。当你用`$ git reset --hard HEAD^`回退到`add distributed`版本时，再想恢复到`append GPL`，就必须找到`append GPL`的commit id。Git提供了一个命令`git reflog`用来记录你的每一次命令：



```
$ git reflog
e475afc HEAD@{1}: reset: moving to HEAD^
1094adb (HEAD -> master) HEAD@{2}: commit: append GPL
e475afc HEAD@{3}: commit: add distributed
eaadf4e HEAD@{4}: commit (initial): wrote a readme file
```

终于舒了口气，从输出可知，`append GPL`的commit id是`1094adb`，现在，你又可以乘坐时光机回到未来了。

牛逼！！！！

### 工作区和暂存区

工作区：

就是你在电脑里能看到的目录，比如我的`learngit`文件夹就是一个工作区：

![image-20220924165140252](C:\Users\XZY\AppData\Roaming\Typora\typora-user-images\image-20220924165140252.png)

版本库

工作区有一个隐藏目录`.git`，这个不算工作区，而是Git的版本库。

Git的版本库里存了很多东西，其中最重要的就是称为stage（或者叫index）的暂存区，还有Git为我们自动创建的第一个分支`master`，以及指向`master`的一个指针叫`HEAD`。

![image-20220924165824344](C:\Users\XZY\AppData\Roaming\Typora\typora-user-images\image-20220924165824344.png)

第一步是用`git add`把文件添加进去，实际上就是把文件修改添加到暂存区；

第二步是用`git commit`提交更改，实际上就是把暂存区的所有内容提交到当前分支。

因为我们创建Git版本库时，Git自动为我们创建了唯一一个`master`分支，所以，现在，`git commit`就是往`master`分支上提交更改。

你可以简单理解为，需要提交的文件修改通通放到暂存区，然后，一次性提交暂存区的所有修改

![image-20220924170621106](C:\Users\XZY\AppData\Roaming\Typora\typora-user-images\image-20220924170621106.png)

![image-20220924170626037](C:\Users\XZY\AppData\Roaming\Typora\typora-user-images\image-20220924170626037.png)

### 管理修改

为什么Git比其他版本控制系统设计得优秀，因为Git跟踪并管理的是修改，而非文件。

你会问，什么是修改？比如你新增了一行，这就是一个修改，删除了一行，也是一个修改，更改了某些字符，也是一个修改，删了一些又加了一些，也是一个修改，甚至创建一个新文件，也算一个修改。

现在，你又理解了Git是如何跟踪修改的，每次修改，如果不用`git add`到暂存区，那就不会加入到`commit`中。

### 撤销修改

![image-20220924171330983](C:\Users\XZY\AppData\Roaming\Typora\typora-user-images\image-20220924171330983.png)

xshhh

```
$ git checkout -- readme.txt
```

命令`git checkout -- readme.txt`意思就是，把`readme.txt`文件在工作区的修改全部撤销，这里有两种情况：

一种是`readme.txt`自修改后还没有被放到暂存区，现在，撤销修改就回到和版本库一模一样的状态；

一种是`readme.txt`已经添加到暂存区后，又作了修改，现在，撤销修改就回到添加到暂存区后的状态。

总之，就是让这个文件回到最近一次`git commit`或`git add`时的状态。

现在，看看`readme.txt`的文件内容：

```
$ cat readme.txt
Git is a distributed version control system.
Git is free software distributed under the GPL.
Git has a mutable index called stage.
Git tracks changes of files.
```

庆幸的是，在`commit`之前，你发现了这个问题。用`git status`查看一下，修改只是添加到了暂存区，还没有提交：

```
$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

	modified:   readme.txt
```



Git同样告诉我们，用命令`git reset HEAD <file>`可以把暂存区的修改撤销掉（unstage），重新放回工作区：

```
$ git reset HEAD readme.txt
Unstaged changes after reset:
M	readme.txt
```

`git reset`命令既可以回退版本，也可以把暂存区的修改回退到工作区。当我们用`HEAD`时，表示最新的版本。

再用`git status`查看一下，现在暂存区是干净的，工作区有修改：

```
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	modified:   readme.txt
```

就算你提交到版本库也没有关系，他毕竟是本地的，但是一旦你推送到远程恭喜你

寄！

又到了小结时间。

场景1：当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，用命令`git checkout -- file`。

场景2：当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，第一步用命令`git reset HEAD <file>`，就回到了场景1，第二步按场景1操作。

场景3：已经提交了不合适的修改到版本库时，想要撤销本次提交，参考[版本回退](https://www.liaoxuefeng.com/wiki/896043488029600/897013573512192)一节，不过前提是没有推送到远程库。

### 删除文件

命令`git rm`用于删除一个文件。如果一个文件已经被提交到版本库，那么你永远不用担心误删，但是要小心，你只能恢复文件到最新版本，你会丢失**最近一次提交后你修改的内容**。

在Git中，删除也是一个修改操作，我们实战一下，先添加一个新文件`test.txt`到Git并且提交：

```
$ git add test.txt

$ git commit -m "add test.txt"
[master b84166e] add test.txt
 1 file changed, 1 insertion(+)
 create mode 100644 test.txt
```

一般情况下，你通常直接在文件管理器中把没用的文件删了，或者用`rm`命令删了：

```
$ rm test.txt
```

这个时候，Git知道你删除了文件，因此，工作区和版本库就不一致了，`git status`命令会立刻告诉你哪些文件被删除了：

```
$ git status
On branch master
Changes not staged for commit:
  (use "git add/rm <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	deleted:    test.txt

no changes added to commit (use "git add" and/or "git commit -a")
```

现在你有两个选择，一是确实要从版本库中删除该文件，那就用命令`git rm`删掉，并且`git commit`：

```
$ git rm test.txt
rm 'test.txt'

$ git commit -m "remove test.txt"
[master d46f35e] remove test.txt
 1 file changed, 1 deletion(-)
 delete mode 100644 test.txt
```

现在，文件就从版本库中被删除了。

另一种情况是删错了，因为版本库里还有呢，所以可以很轻松地把误删的文件恢复到最新版本：

```
$ git checkout -- test.txt
```

`git checkout`其实是用版本库里的版本替换工作区的版本，无论工作区是修改还是删除，都可以“一键还原”。

### 远程仓库

[GitHub](https://github.com/)的神奇的网站，从名字就可以看出，这个网站就是提供Git仓库托管服务的，所以，只要注册一个GitHub账号，就可以免费获得Git远程仓库。

详情参考链接

git 

https://cloud.tencent.com/developer/article/1933640

“有了远程仓库，妈妈再也不用担心我的硬盘了。”——Git点读机

### 添加远程仓库

这一块属实有点花时间，因为配置问题导致花了1个多小时才解决，但也确实收获了很多，现在让我们看一下如何配置吧

1. 首先注册一个github账号
2. 之后新建仓库，仓库的名称为想关联工作区名称即可，其他默认
3. 目前github库为空，因为我们还未将本地的库和其关联并上传，根据系统提示，我们可以选择三种方式创建或者直接关联本地库。由于我们前面已经创建本地仓库，现在输入命令如下：

```
git remote add origin git https://github.com:SUPERpowerGT/learngit.git
```

关联本地数据库和github上的远程仓库

下一步，就可以把本地库的所有内容推送到远程库上：

```
$ git push -u origin master
```

这里就遇到问题了，git bash总是会出现各种错误，再经过几经查阅后，发现问题出自于本地网络用户代理地址端口号需要重新在git bash配置（dddd），以及现在默认的主线为main而非master

git设置代理的方式

1. 首先确认自己git拉取代码的方式
   git remote -v （以https方式连接远程仓库）
   origin https://github.com/（fetch）
   origin https://github.com/（push）
2. 设置代理
   http
   git config --global http.proxy [http://127.0.0.1:7890](http://127.0.0.1:7890/)（自己代理的端口号）
   git config --global https.proxy [https://127.0.0.1:7890](https://127.0.0.1:7890/)（自己代理的端口号）
   ssh
   git config --global http.proxy 'socks5://127.0.0.1:1080'
   git config --global https.proxy 'socks5://127.0.0.1:1080'
3. 查看代理是否成功
   git config --get --global http.proxy（未设置就没有返回值）
   [http://127.0.0.1:7890](http://127.0.0.1:7890/)

4.查看git配置
git config --global --list

1. 取消代理
   git config --global --unset http.proxy
   git config --global --unset https.proxy

输入代码重新上传

```
$ git push -u origin main
```

![image-20220924201812267](C:\Users\XZY\AppData\Roaming\Typora\typora-user-images\image-20220924201812267.png)

成功！！

![image-20220924201825373](C:\Users\XZY\AppData\Roaming\Typora\typora-user-images\image-20220924201825373.png)

我们可以在主界面查看我们上传的代码了（泪目

### 从远程仓库克隆

首先我们可以在github上创建一个新的仓库

我们勾选`Initialize this repository with a README`，这样GitHub会自动为我们创建一个`README.md`文件。

然后再在git bash中

用命令`git clone`克隆一个本地库：

```
$ git clone https://github.com/SUPERpowerGT/gitskills.git
Cloning into 'gitskills'...
remote: Counting objects: 3, done.
remote: Total 3 (delta 0), reused 0 (delta 0), pack-reused 3
Receiving objects: 100% (3/3), done.
```

注意把Git库的地址换成你自己的，然后进入`gitskills`目录看看，已经有`README.md`文件了：

```
$ cd gitskills
$ ls
README.md
```

### 分支管理

简单来说，我们可以在main基础上创建自己的分支，类比于平行宇宙的概念，这样可以实现并行开发互不干扰同时我们也能上传保证进度不会丢失

但Git的分支是与众不同的，无论创建、切换和删除分支，Git在1秒钟之内就能完成！无论你的版本库是1个文件还是1万个文件。

### 创建和合并分支

head指向当前分支，master（现在为main）才是指向提交的，严格来说

一开始的时候，`master`分支是一条线，Git用`master`指向最新的提交，再用`HEAD`指向`master`，就能确定当前分支，以及当前分支的提交点：

![image-20220924203257554](C:\Users\XZY\AppData\Roaming\Typora\typora-user-images\image-20220924203257554.png)

每次提交，`master`分支都会向前移动一步，这样，随着你不断提交，`master`分支的线也越来越长。

当我们创建新的分支，例如`dev`时，Git新建了一个指针叫`dev`，指向`master`相同的提交，再把`HEAD`指向`dev`，就表示当前分支在`dev`上：

![image-20220924203605517](C:\Users\XZY\AppData\Roaming\Typora\typora-user-images\image-20220924203605517.png)

所以Git合并分支也很快！就改改指针，工作区内容也不变！

合并完分支后，甚至可以删除`dev`分支。删除`dev`分支就是把`dev`指针给删掉，删掉后，我们就剩下了一条`master`分支：

![image-20220924203803377](C:\Users\XZY\AppData\Roaming\Typora\typora-user-images\image-20220924203803377.png)



创建新的dev分支，并切换到dev分支

```
$ git checkout -b dev
Switched to a new branch 'dev'
```

![image-20220924204508842](C:\Users\XZY\AppData\Roaming\Typora\typora-user-images\image-20220924204508842.png)

`git checkout`命令加上`-b`参数表示创建并切换，相当于以下两条命令：

```
$ git branch dev
$ git checkout dev
Switched to branch 'dev'
```

然后，用`git branch`命令查看当前分支：

![image-20220924204636889](C:\Users\XZY\AppData\Roaming\Typora\typora-user-images\image-20220924204636889.png)

`git branch`命令会列出所有分支，当前分支前面会标一个`*`号。

然后，我们就可以在`dev`分支上正常提交，比如对`readme.txt`做个修改，加上一行：

```
Creating a new branch is quick.
```

然后提交：

```
$ git add readme.txt 
$ git commit -m "branch test"
[dev b17d20e] branch test
 1 file changed, 1 insertion(+)
```

现在，`dev`分支的工作完成，我们就可以切换回main分支：

```
$ git checkout main


```

切换回`master`分支后，再查看一个`readme.txt`文件，刚才添加的内容不见了！因为那个提交是在`dev`分支上，而`master`分支此刻的提交点并没有变：

![image-20220924210107142](C:\Users\XZY\AppData\Roaming\Typora\typora-user-images\image-20220924210107142.png)

现在，我们把`dev`分支的工作成果合并到`master`分支上：

```
$ git merge dev
Updating d46f35e..b17d20e
Fast-forward
 readme.txt | 1 +
 1 file changed, 1 insertion(+)
```

`git merge`命令用于合并指定分支到当前分支。合并后，再查看`readme.txt`的内容，就可以看到，和`dev`分支的最新提交是完全一样的。

注意到上面的`Fast-forward`信息，Git告诉我们，这次合并是“快进模式”，也就是直接把`master`指向`dev`的当前提交，所以合并速度非常快。

当然，也不是每次合并都能`Fast-forward`，我们后面会讲其他方式的合并。

合并完成后，就可以放心地删除`dev`分支了：

```
$ git branch -d dev
Deleted branch dev (was b17d20e).
```

删除后，查看`branch`，就只剩下`master`分支了：

```
$ git branch
* master
```

因为创建、合并和删除分支非常快，所以Git鼓励你使用分支完成某个任务，合并后再删掉分支，这和直接在`master`分支上工作效果是一样的，但过程更安全。

### switch

我们注意到切换分支使用`git checkout <branch>`，而前面讲过的撤销修改则是`git checkout -- <file>`，同一个命令，有两种作用，确实有点令人迷惑。

实际上，切换分支这个动作，用`switch`更科学。因此，最新版本的Git提供了新的`git switch`命令来切换分支：

创建并切换到新的`dev`分支，可以使用：

```
$ git switch -c dev
```

直接切换到已有的`master`分支，可以使用：

```
$ git switch master
```

![image-20220924205405825](C:\Users\XZY\AppData\Roaming\Typora\typora-user-images\image-20220924205405825.png)

![image-20220924210012700](C:\Users\XZY\AppData\Roaming\Typora\typora-user-images\image-20220924210012700.png)

### 解决冲突

分支产生冲突是很常见的事情，通常我们会需要手动修改代码解决冲突后再重新合并分支

### 分支管理策略

通常情况下git会使用fast forward模式，在这种模式下删除分支后会丢掉分支信息

如果要强制禁用fast forward模式Git就会在merge时生成一个新的commit，这样，从分支历史上就可以看出分支信息。

下面我们实战一下`--no-ff`方式的`git merge`：

### 分支策略

在实际开发中，我们应该按照几个基本原则进行分支管理：

首先，`master`分支应该是非常稳定的，也就是仅用来发布新版本，平时不能在上面干活；

那在哪干活呢？干活都在`dev`分支上，也就是说，`dev`分支是不稳定的，到某个时候，比如1.0版本发布时，再把`dev`分支合并到`master`上，在`master`分支发布1.0版本；

你和你的小伙伴们每个人都在`dev`分支上干活，每个人都有自己的分支，时不时地往`dev`分支上合并就可以了。

所以，团队合作的分支看起来就像这样：

![image-20220924210726193](C:\Users\XZY\AppData\Roaming\Typora\typora-user-images\image-20220924210726193.png)

### bug分支

Git还提供了一个`stash`功能，可以把当前工作现场“储藏”起来，等以后恢复现场后继续工作：

```
$ git stash
Saved working directory and index state WIP on dev: f52c633 add merge
```

现在，用`git status`查看工作区，就是干净的（除非有没有被Git管理的文件），因此可以放心地创建分支来修复bug。

首先确定要在哪个分支上修复bug，假定需要在`master`分支上修复，就从`master`创建临时分支：

```
$ git checkout master
Switched to branch 'master'
Your branch is ahead of 'origin/master' by 6 commits.
  (use "git push" to publish your local commits)

$ git checkout -b issue-101
Switched to a new branch 'issue-101'
```

现在修复bug，需要把“Git is free software ...”改为“Git is a free software ...”，然后提交：

```
$ git add readme.txt 
$ git commit -m "fix bug 101"
[issue-101 4c805e2] fix bug 101
 1 file changed, 1 insertion(+), 1 deletion(-)
```

修复完成后，切换到`master`分支，并完成合并，最后删除`issue-101`分支：

```
$ git switch master
Switched to branch 'master'
Your branch is ahead of 'origin/master' by 6 commits.
  (use "git push" to publish your local commits)

$ git merge --no-ff -m "merged bug fix 101" issue-101
Merge made by the 'recursive' strategy.
 readme.txt | 2 +-
 1 file changed, 1 insertion(+), 1 deletion(-)
```

太棒了，原计划两个小时的bug修复只花了5分钟！现在，是时候接着回到`dev`分支干活了！

```
$ git switch dev
Switched to branch 'dev'

$ git status
On branch dev
nothing to commit, working tree clean
```

工作区是干净的，刚才的工作现场存到哪去了？用`git stash list`命令看看：

```
$ git stash list
stash@{0}: WIP on dev: f52c633 add merge
```

工作现场还在，Git把stash内容存在某个地方了，但是需要恢复一下，有两个办法：

一是用`git stash apply`恢复，但是恢复后，stash内容并不删除，你需要用`git stash drop`来删除；

另一种方式是用`git stash pop`，恢复的同时把stash内容也删了：

```
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

再用`git stash list`查看，就看不到任何stash内容了：

```
$ git stash list
```

你可以多次stash，恢复的时候，先用`git stash list`查看，然后恢复指定的stash，用命令：

```
$ git stash apply stash@{0}
```

在master分支上修复了bug后，我们要想一想，dev分支是早期从master分支分出来的，所以，这个bug其实在当前dev分支上也存在。

那怎么在dev分支上修复同样的bug？重复操作一次，提交不就行了？

有木有更简单的方法？

有！

同样的bug，要在dev上修复，我们只需要把`4c805e2 fix bug 101`这个提交所做的修改“复制”到dev分支。注意：我们只想复制`4c805e2 fix bug 101`这个提交所做的修改，并不是把整个master分支merge过来。

为了方便操作，Git专门提供了一个`cherry-pick`命令，让我们能复制一个特定的提交到当前分支：

```
$ git branch
* dev
  master
$ git cherry-pick 4c805e2
[master 1d4b803] fix bug 101
 1 file changed, 1 insertion(+), 1 deletion(-)
```

Git自动给dev分支做了一次提交，注意这次提交的commit是`1d4b803`，它并不同于master的`4c805e2`，因为这两个commit只是改动相同，但确实是两个不同的commit。用`git cherry-pick`，我们就不需要在dev分支上手动再把修bug的过程重复一遍。

有些聪明的童鞋会想了，既然可以在master分支上修复bug后，在dev分支上可以“重放”这个修复过程，那么直接在dev分支上修复bug，然后在master分支上“重放”行不行？当然可以，不过你仍然需要`git stash`命令保存现场，才能从dev分支切换到master分支。

### feature分支

软件开发中，总有无穷无尽的新的功能要不断添加进来。

添加一个新功能时，你肯定不希望因为一些实验性质的代码，把主分支搞乱了，所以，每添加一个新功能，最好新建一个feature分支，在上面开发，完成后，合并，最后，删除该feature分支。

现在，你终于接到了一个新任务：开发代号为Vulcan的新功能，该功能计划用于下一代星际飞船。

于是准备开发：

```
$ git switch -c feature-vulcan
Switched to a new branch 'feature-vulcan'
```

5分钟后，开发完毕：

```
$ git add vulcan.py

$ git status
On branch feature-vulcan
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

	new file:   vulcan.py

$ git commit -m "add feature vulcan"
[feature-vulcan 287773e] add feature vulcan
 1 file changed, 2 insertions(+)
 create mode 100644 vulcan.py
```

切回`dev`，准备合并：

```
$ git switch dev
```

一切顺利的话，feature分支和bug分支是类似的，合并，然后删除。

但是！

就在此时，接到上级命令，因经费不足，新功能必须取消！

虽然白干了，但是这个包含机密资料的分支还是必须就地销毁：

```
$ git branch -d feature-vulcan
error: The branch 'feature-vulcan' is not fully merged.
If you are sure you want to delete it, run 'git branch -D feature-vulcan'.
```

销毁失败。Git友情提醒，`feature-vulcan`分支还没有被合并，如果删除，将丢失掉修改，如果要强行删除，需要使用大写的`-D`参数。。

现在我们强行删除：

```
$ git branch -D feature-vulcan
Deleted branch feature-vulcan (was 287773e).
```



[back](./)