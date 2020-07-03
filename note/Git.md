# 集中式与分布式

> Git：分布式；SVN：集中式

集中式：就像图书馆一样，版本库就是图书馆。修改时从图书馆借书修改，然后放回图书馆

- 必须联网工作

---

分布式：每个人的电脑都是完整的版本库，因此无需联网。中央服务器只负责方便交换“修改”

- Git是分布式版本控制系统，所以，每个机器都必须自报家门：名字和Email地址

```bash
$ git config --global user.name "Your Name"
$ git config --global user.email "email@example.com"
```

- `--global`参数，用了这个参数，表示这台机器上所有的Git仓库都会使用这个配置

# 版本库

版本库又名仓库，Repository

- 管理一个文件夹，里面所有文件都能被Git管理，修改、删除都能跟踪

使用`git init`把一个文件夹变为一个仓库

---

所有的版本控制系统，==其实只能跟踪文本文件的改动==，而图片、视频这些二进制文件，虽然也能由版本控制系统管理，但没法跟踪文件的变化，只能把二进制文件每次改动串起来，也就是只知道图片从100KB改成了120KB，但到底改了啥，版本控制系统不知道，也没法知道。

- Microsoft的Word格式是二进制格式
- 不要使用Windows自带的**记事本**编辑任何文本文件，Microsoft开发记事本的团队使用了一个非常弱智的行为来保存UTF-8编码的文件：在每个文件开头添加了0xefbbbf（十六进制）的字

# 提交与查看

需要管理的文件一定要放在仓库目录下，放到其他地方Git再厉害也找不到这个文件

```bash
# 把文件添加到仓库中
git add readme.txt

# 把文件提交到仓库中
git commit -m ""

# 查看仓库当前的状态
git status

# 查看上一次文件修改的地方
git diff readme.txt
```

---

# 版本选择

不断对文件进行修改，然后不断提交修改到版本库里，就好比玩RPG游戏时，每通过一关就会自动把游戏状态存盘，如果某一关没过去，你还可以选择读取前一关的状态

有些时候，在打Boss之前，你会手动存盘，以便万一打Boss失败了，可以从最近的地方重新开始。Git也是一样，每当你觉得文件修改到一定程度的时候，就可以“保存一个快照”，这个快照在Git中被称为`commit`

```bash
# 历史记录
git log
```

在Git中，用`HEAD`表示当前版本，上一个版本是`HEAD^`，上上一个版本是`HEAD^^`，当然往上100个版本写100个`^`比较容易数不过来，所以写成`HEAD~100`

```bash
# 回退
git reset --hard HEAD^

# 到指定版本
git reset --hard 版本id

# 记录了每一次命令，比如可以找到消失的版本id
git reflog
```

# 工作区与暂存区

暂存区：相当于缓存

---

工作区：电脑上的仓库文件夹

版本库：工作区的隐藏文件夹.git，包含

- 暂存区：stage
- master分支：真正的版本库

![](https://gitee.com/DtreeL/Image-Hosting-Service/raw/master/img/20200616185855.png)

`git add`：把文件提交到暂存区

`git commit`：往分支上提交更改，一个提交就是一个版本，提交后，暂存区清空

---

git管理的是修改

- 提交到暂存区保存了工作区文件的修改，暂存区的文件就已经固定了，此时无论修改文件多少次，提交后都是暂存区的第一次修改
- 继续`git add`使得没提交但是add的文件的第二次、第三次...的修改生效

---

场景1：当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，用命令`git checkout -- readme.txt`。

场景2：当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，第一步用命令`git reset HEAD readme.txt`，就回到了场景1，第二步按场景1操作。

场景3：已经提交了不合适的修改到版本库时，想要撤销本次提交，版本回退，不过前提是没有推送到远程库。

----

工作区中删除了文件

一是确实要从版本库中删除该文件，那就用命令`git rm`删掉，并且`git commit`

另一种情况是删错了，因为版本库里还有呢，所以可以很轻松地把误删的文件恢复到最新版本：

```bash
git checkout -- test.txt
```

删除使用`git checkout`其实是用版本库里的版本替换工作区的版本，无论工作区是修改还是删除，都可以“一键还原”

# 远程库

```bash
git remote add origin git@github.com:账户名/仓库名.git

# 把当前分支master推送到远程
git push -u origin master

# -u参数，Git不但会把本地的master分支内容推送的远程新的master分支，还会把本地的master分支和远程的master分支关联起来，在以后的推送或者拉取时就可以简化命令
```

从现在起，只要本地作了提交，就可以通过命令：

```bash
$ git push origin master
```

---

```bash
git clone git@github.com:账户名/仓库名.git
```

# 分支管理

分支相当于平行宇宙

问题：准备开发一个新功能，但是需要两周才能完成，第一周你写了50%的代码，如果立刻提交，由于代码还没写完，不完整的代码库会导致别人不能干活了。如果等代码全部写完再一次提交，又存在丢失每天进度的巨大风险。

- 创建了一个属于你自己的分支，别人看不到，还继续在原来的分支上正常工作，而你在自己的分支上干活，想提交就提交，直到开发完毕后，再一次性合并到原来的分支上，这样，既安全，又不影响别人工作。

---

master指针与分支指针，HEAD指向分支指针

```bash
# -c 创建并切换
git switch -c dev

# 相当于以下两个命令
git branch dev
git switch dev

# 查看所有分支
git branch
```

```bash
# 切换到master分支
git switch master

# 合并指定分支到当前分支
git merge dev

# 删除分支
git branch -d dev
```

# 使用

一个git仓库分为

- 工作区：电脑上git仓库中的文件或者文件夹
- `.git`
  - 暂存区：.git文件夹里，相当于缓存（随时可以撤销，提交后自动清空）
  - 本地库：同样`.git`文件夹里像时间线似的版本线（**每一个版本就是一个快照**）

git只能管理暂存区和本地库的文件

## 创建Git仓库

想管理的文件的同级目录下，便创建了一个仓库：

```bash
git init
```

## 设置签名（区分开发人员身份）

```bash
git config --global user.name Sanctuary_L
git config --global user.email 17674702076@163.com
```

`--global`：在本机所有git仓库中生效，不加则在当前库生效

- 优先级：就近原则

## 常用命令

```bash
# 查看工作区和缓存区的状态
git status

# 工作区增删改添加到到暂存区，把一个操作添加到暂存区
git add ReadMe.md

# 清空缓存区，作为版本库线的一个版本
git commit ReadMe.md -m "创建ReadMe.md文件"

# 版本库线完整信息
git log
	# 简洁信息,只能显示过去
	git log --pretty=oneline
	git log --oneline
	# 版本变化指针移动步数信息，过去未来都能显示
	git reflog
	
# 版本变化
git reset --hard 索引值
	# 只能退后不能往前
	git reset --hard Head^
	git reset --hard Head~3
	
	reset
		--soft：只在本地库移动Head指针（本地库后移，显得暂存区前移了）
		--mixed：移动Head指针，并且清空缓存区（本地库、暂存区后移，显得工作区前移）
		--hard：移动Head指针，清空缓存区并且重置工作区

# 文件改动比较
	# 工作区文件和暂存区进行比较，暂存区为空，则显示工作区的差异
	git diff ReadMe.md
	
	# 工作区与一个版本进行比较
	git diff HEAD Read.me
```

## 分支

从主分支中最新版本复制出来作为另一个分支的根

![](https://gitee.com/DtreeL/Image-Hosting-Service/raw/master/img/20200703092917.png)

- 多个功能同时并行开发
- 分支互不影响

master指针与分支指针，HEAD指向分支指针

```bash
# -c 创建并切换
git switch -c dev

# 相当于以下两个命令
git branch dev
git switch dev

# 查看所有分支
git branch -v
```

```bash
# 切换到master分支
git switch master

# 合并dev分支到当前分支
git merge dev

# 删除分支
git branch -d dev

# 冲突处理。master在一个地方做了修改提交，分支也在同一个地方修改做提交合并。这时会产生冲突进入合并模式
# 在文件上解决冲突后，add，commit（不能写文件名）
```

## Github

```bash
#查看所有远程库
git remote -v

# 添加一个名为origin的远程库，origin代替了仓库地址
git remote add origin git@github.com:账户名/仓库名.git

# 把当前分支master推送到远程，-u参数，Git不但会把本地的master分支内容推送的远程新的master分支，还会把本地的master分支和远程的master分支关联起来，在以后的推送或者拉取时就可以简化命令
git push -u origin master

# 推送origin远程库的master分支
git push origin master

# 克隆。完整把远程库下载到本地，创建origin远程地址别名，初始化本地库
git clone git@github.com:账户名/仓库名.git
```

邀请一个用户成为自己的开发库的团队中的一员，以便能够push

- 库的Settings-->Collaborators（输入想要邀请的人的github账号）-->Copy invite link发送给被邀请人
- 邀请人跳转到链接接受邀请即可加入团队开始push了

```bash
pull = fetch + merge

# 下载下来origin库的master分支内容，并没有合并到自己的分支中，所以工作区文件不会改变
git fetch origin master

# 合并了远程库的master分支后，文件发生变化
git merge origin/master

# 不太会产生冲突时
git pull origin master
```

```bash
当落后master一个版本时，即在老版本上修改，此时推送到master产生冲突，需要解决冲突

先pull进入MERGING状态解决冲突后再push
```

---

### 跨团队协作

一个问题你解决不了请不是你们公司的人解决（不在团队中）

点击Fork复制出来一个远程库，该远程库属于执行Fork的人

- clone下来，自己推送，搞定之后
- 点击Create pull request可以对话，通过审核后点击Merge pull request合并到原来的库中

## SSH

第1步：创建SSH Key。在用户主目录下，看看有没有.ssh目录，如果有，再看看这个目录下有没有`id_rsa`和`id_rsa.pub`这两个文件，如果已经有了，可直接跳到下一步。如果没有，打开Shell（Windows下打开Git Bash），创建SSH Key：

```bash
ssh-keygen -t rsa -C 17674702076@163.com
```

第2步：登陆GitHub，打开“Account settings”，“SSH Keys”页面：

- 然后，点“Add SSH Key”，填上任意Title，在Key文本框里粘贴`id_rsa.pub`文件的内容：