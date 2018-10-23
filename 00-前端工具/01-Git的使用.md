



## 常见操作

### 配置用户信息

```
git config --global user.name "smyhvae"

git config --global user.email "smyhvae@163.com"
```


## 分支的合并


### 场景：基于master分支的代码，开发一个新的特性

如果你直接在master分支上开发这个新特性，是不好的，万一你在开发`特性1`的时候，领导突然又要叫你去开发`特性2`，就不好处理了。难道开发的两个特性都提交到master？一会儿提交特性1的commit，一会儿提交特性2的commit？这会导致commit记录很混乱。

所以，我给你的建议做法是：给每个特性都单独建一个的新的分支。

比如说，我专门给`特性1`建一个分支`feature_item_recommend`。具体做法如下：

（1）基于master分支，创建一个新的分支，起名为`feature_item_recommend`：

```
$ git checkout -b feature_item_recommend

Switched to a new branch 'feature_item_recommend'
```

上面这行命令，相当于：


```bash
$ git branch feature_item_recommend    // 创建新的分支

$ git checkout feature_item_recommend  //切换到新的分支
```


（2）在新的分支`feature_item_recommend`上，完成开发工作，并 commit 、push。

（3）将分支`feature_item_recommend`上的开发进度**合并**到master分支：

```bash
$ git checkout master  //切换到master分支

$ git merge feature_item_recommend    //将分支 feature_item_recommend 的开发进度合并到 master 分支

```


合并之后，`master`分支和`feature_item_recommend`分支会指向同一个位置。


（3）删除分支`feature_item_recommend`：

> 既然 特性1 开发完了，也放心地提交到master了，那我们就可以将这个分支删除了。

```
git branch -d feature_item_recommend
```

注意，我们当前是处于`master`分支的位置，来删除`feature_item_recommend`分支。如果当前是处于`feature_item_recommend`分支，是没办法删除它自己的。

同理，当我转身去开发`特性2`的时候，也是采用同样的步骤。


### 合并分支时，如果存在分叉


![](http://img.smyhvae.com/20180610_1650.png)


比如说上面这张图中，最早的时候，master分支是位于`C2`节点。我基于`C2`节点，new出一个新的分支`iss53`，我在`iss53`上提交了好几个commit。

现在，我准备把`iss53`上的几个commit合并到master上，此时发现，master分支已经前进到C4了。那该怎么合并呢？

合并的命令仍然是：

```bash
$ git checkout master

$ git merge iss53
```

**解释**：

这次合并的实现，并不同于简单的并入方式。这一次，我的开发历史是从更早的地方开始分叉的。

由于当前 master 分支所指向的commit (C4)并非想要并入分支（iss53）的直接祖先，Git 不得不进行一些处理。就此例而言，Git 会用两个分支的末端（C4 和C5）和它们的共同祖先（C2）进行一次简单的三方合并计算。

Git 没有简单地把分支指针右移，而是对三方合并的结果作一新的快照，并自动创建一个指向它的commit（C6）（如下图所示）。我们把这个特殊的commit 称作合并提交（mergecommit），因为它的祖先不止一个。

值得一提的是Git 可以自己裁决哪个共同祖先才是最佳合并基础；这和CVS 或Subversion（1.5 以后的版本）不同，它们需要开发者手工指定合并基础。所以此特性让Git 的合并操作比其他系统都要简单不少。

![](http://img.smyhvae.com/20180610_1710.png)

### 解决合并时发生的冲突

![](http://img.smyhvae.com/20180610_1740.png)

如果 feature1和feature2修改的是同一个文件中**代码的同一个位置**，那么，把feature1合并到feature2时，就会产生冲突。这个冲突需要人工解决。步骤如下：

（1）手动修改文件：手动修改冲突的那个文件，决定到底要用哪个分支的代码。

（2）git add：解决好冲突后，输入`git status`，会提示`Unmerged paths`。这个时候，输入`git add`即可，表示：**修改冲突成功，加入暂存区**。

（3）git commit 提交。

然后，我们可以继续把 feature1 分支合并到 master分支，最后删除feature1、feature2。

**注意**：两个分支的同一个文件的不同地方合并时，git会自动合并，不会产生冲突。

比如分支feture1对index.html原来的第二行之前加入了一段代码。
分支feature2对index.html在原来的最后一行的后面加入了一段代码。
这个时候在对两个分支合并，git不会产生冲突，因为两个分支是修改同一文件的不同位置。
git自动合并成功。不管是git自动合并成功，还是在人工解决冲突下合并成功，提交之前，都要对代码进行测试。


## 日常操作积累


### 修改密码（曲线救国）


> 网上查了很久，没找到答案。最终，在cld童鞋的提示下，采取如下方式进行曲线救国。

```bash
# 设置当前仓库的用户名为空
git config  user.name ""
```


然后，当我们再输入`git pull`等命令行时，就会被要求重新输入*新的*账号密码。此时，密码就可以修改成功了。最后，我们还要输入如下命令，还原当前仓库的用户名：

```
git config user.name "smyhvae"
```


### 修改已经push的某次commit的作者信息

已经push的记录，如果要修改作者信息的话，只能 通过--force命令。我反正是查了很久，但最终还是不敢用公司的仓库尝试。

参考链接：


- [git 修改已提交的某一次的邮箱和用户信息](https://segmentfault.com/q/1010000006999861)

看最后一条答案。

- [修改 git repo 历史提交的 author](http://baurine.github.io/2015/08/22/git_update_author.html)



### 将 `branch1`的某个`commit1`合并到`branch2`当中

切换到branch2中，然后执行如下命令：

```
git cherry-pick commit1
```


## git客户端推荐

20180623时，网上看了下Git客户端的推荐排名：

![](http://img.smyhvae.com/20180623_1210.png)

上面的Git客户端我基本都用过了，我最推荐的一款Git客户端是：**Tower**。

**SmartGit**：

商业用途收费， 个人用户免费：

![](http://img.smyhvae.com/20180623_1305.png)






## 推荐书籍

- 《pro.git中文版》



## 推荐连接


### 2018-06

- [聊下git pull --rebase](https://www.cnblogs.com/wangiqngpei557/p/6056624.html)



# [Git Submodule管理项目子模块](https://www.cnblogs.com/nicksheng/p/6201711.html)

## 使用场景

当项目越来越庞大之后，不可避免的要拆分成多个子模块，我们希望各个子模块有独立的版本管理，并且由专门的人去维护，这时候我们就要用到git的submodule功能。

## 常用命令

```
git clone <repository> --recursive 递归的方式克隆整个项目
git submodule add <repository> <path> 添加子模块
git submodule init 初始化子模块
git submodule update 更新子模块
git submodule foreach git pull 拉取所有子模块
```

## 如何使用

### 1. 创建带子模块的版本库

例如我们要创建如下结构的项目

```
project
  |--moduleA
  |--readme.txt
```

创建project版本库，并提交readme.txt文件

```
git init --bare project.git
git clone project.git project1
cd project1
echo "This is a project." > readme.txt
git add .
git commit -m "add readme.txt"
git push origin master
cd ..
```

创建moduleA版本库，并提交a.txt文件

```
git init --bare moduleA.git
git clone moduleA.git moduleA1
cd moduleA1
echo "This is a submodule." > a.txt
git add .
git commit -m "add a.txt"
git push origin master
cd ..
```

在project项目中引入子模块moduleA，并提交子模块信息

```
cd project1
git submodule add ../moduleA.git moduleA
git status
git diff
git add .
git commit -m "add submodule"
git push origin master
cd ..
```

使用`git status`可以看到多了两个需要提交的文件，其中`.gitmodules`指定submodule的主要信息，包括子模块的路径和地址信息，`moduleA`指定了子模块的commit id，使用`git diff`可以看到这两项的内容。这里需要指出父项目的git并不会记录submodule的文件变动，它是按照commit id指定submodule的git header，所以`.gitmodules`和`moduleA`这两项是需要提交到父项目的远程仓库的。

```
On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)
    new file:   .gitmodules
    new file:   moduleA
```

### 2. 克隆带子模块的版本库

方法一，先clone父项目，再初始化submodule，最后更新submodule，初始化只需要做一次，之后每次只需要直接update就可以了，需要注意submodule默认是不在任何分支上的，它指向父项目存储的submodule commit id。

```
git clone project.git project2
cd project2
git submodule init
git submodule update
cd ..
```

方法二，采用递归参数`--recursive`，需要注意同样submodule默认是不在任何分支上的，它指向父项目存储的submodule commit id。

```
git clone project.git project3 --recursive
```

### 3. 修改子模块

修改子模块之后只对子模块的版本库产生影响，对父项目的版本库不会产生任何影响，如果父项目需要用到最新的子模块代码，我们需要更新父项目中submodule commit id，默认的我们使用`git status`就可以看到父项目中submodule commit id已经改变了，我们只需要再次提交就可以了。

```
cd project1/moduleA
git branch
echo "This is a submodule." > b.txt
git add .
git commit -m "add b.txt"
git push origin master
cd ..
git status
git diff
git add .
git commit -m "update submodule add b.txt"
git push origin master
cd ..
```

### 4. 更新子模块

更新子模块的时候要注意子模块的分支默认不是master。

方法一，先pull父项目，然后执行`git submodule update`，注意moduleA的分支始终不是master。

```
cd project2
git pull
git submodule update
cd ..
```

方法二，先进入子模块，然后切换到需要的分支，这里是master分支，然后对子模块pull，这种方法会改变子模块的分支。

```
cd project3/moduleA
git checkout master
cd ..
git submodule foreach git pull
cd ..
```

### 5. 删除子模块

网上有好多用的是下面这种方法

```
git rm --cached moduleA
rm -rf moduleA
rm .gitmodules
vim .git/config
```

删除submodule相关的内容，例如下面的内容

```
[submodule "moduleA"]
      url = /Users/nick/dev/nick-doc/testGitSubmodule/moduleA.git
```

然后提交到远程服务器

```
git add .
git commit -m "remove submodule"
```

但是我自己本地实验的时候，发现用下面的方式也可以，服务器记录的是`.gitmodules`和`moduleA`，本地只要用git的删除命令删除moduleA，再用git status查看状态就会发现.gitmodules和moduleA这两项都已经改变了，至于.git/config，仍会记录submodule信息，但是本地使用也没发现有什么影响，如果重新从服务器克隆则.git/config中不会有submodule信息。

```
git rm moduleA
git status
git commit -m "remove submodule"
git push origin master
```