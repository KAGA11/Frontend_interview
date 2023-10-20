# git 笔记



## 本地操作

### 配置

在使用 Git 进行版本控制时,建议您配置 Git 用户名和电子邮件地址。这是因为 Git 记录每个提交的作者信息,包括用户名和电子邮件地址。配置这些信息可以帮助您在多人协作项目中更好地跟踪和识别提交者。

虽然在使用 Git 之前不是强制要求配置用户名和电子邮件地址,但在进行提交时,Git 会提示您进行配置,以确保每个提交都有相关的作者信息。

```shell
git config --global user.name "Your Name"
git config --global user.email "yourname@example.com"
```



### 文件状态 

​	有git的就是已跟踪(tracked) 		没有git的就是未跟踪(Untracked)

​	已跟踪(tracked) : 在 Git 的版本控制中

​	未跟踪(Untracked)：表示文件不在 Git 的版本控制中,即 Git 对该文件没有任何历史记录。

**git status**  查看当前有没有git

```shell
yy@ydeMacBook-Pro git % git status
fatal: not a git repository (or any of the parent directories): .git
```

**git init** 	初始化git 此时会多一个隐藏文件.git

```shell
yy@ydeMacBook-Pro git % git init
Initialized empty Git repository in /Users/yy/Desktop/git/.git/
```



已跟踪下有三个状态： 未修改 	已修改 	暂存 

​	**未修改(Unmodified)**:表示文件没有被修改过,与最后一次提交的版本完全一致。

​	**已修改(Modified)**:表示文件被修改过,但还没有被暂存(即还没有使用 `git add` 命令将其加入到暂存区)。

​	**已暂存(Staged)**:表示文件被修改过,并且已经使用 `git add` 命令将其加入到了暂存区。



未跟踪(已修改)---> 暂存区

​			git add <file>

​			git add * (全选)

暂存区---> 未修改

​			git commit -m 'xxxx'

​			git commit -a -m 'xxxx'	简化add 和commit

未修改--->已修改

​			修改代码



现在我们创建一个1.txt 此时 该文件属于 untracked:

```shell
git status # 通过git status 查看
On branch main
No commits yet
Untracked files:
  (use "git add <file>..." to include in what will be committed)
        1.txt
```

**git add**  <file>	添加入git: (未跟踪---> 暂存区)

```shell
git add 1.txt
git status   
On branch main
No commits yet
Changes to be committed:
  (use "git rm --cached <file>..." to unstage)
        new file:   1.txt
```

**git commit** **-m "xxxx"** (暂存区---> 未修改 (在仓库中))

```shell
git commit -m '第一次提交txt'  				# m=message 'xxx' = 提示 
[main (root-commit) 476c0f3] 第一次提交txt
 1 file changed, 1 insertion(+)
 create mode 100644 1.txt
```

修改内容		未修改--->已修改

```shell
git status
On branch main
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   1.txt
no changes added to commit (use "git add" and/or "git commit -a")
```

已修改---->未修改 (需要先把已修改 add暂存 然后暂存commit 成未修改)

```shell
git add 1.txt
git commit -m '第二次修改'
```

可以简化为

```shell
git commit -a -m '第二次修改'
```



### 常用命令

​	重制文件

```shell
git restore <file>
git restore --staged <file>  #取消暂存状态
```

​	删除文件

```shell
git rm <file>
git rm <file> -f 强制删除
```

​	恢复删除文件

```shell
git rm 1.txt
rm '1.txt'
git restore --staged 1.txt # 从暂存区拿到untracked区
git restore 1.txt #恢复
```

​	移动文件

```shell
git mv from to #移动文件 重命名文件
git mv 1.txt  2.txt  #重命名文件
```



### 分支

git 在存储文件时 每一次提交都会创建与之对应的节点(存档点) , git就是通过一个个节点来记录代码状态.

节点会构成一个树状结构,默认情况下只有一个分支 叫做 (master或者main)

在使用git时,可以创建多个分支,分支和分支之间相互独立,在一个分支上修改代码不会影响其他分支.

 **注意**: 在以后遇到bug时 先创建一个branch bug1 进行修改 在合并到 原分支

```shell
git log #查看日志
git branch # 查看当前分支
git branch test # 创建一个test分支
git branch -d test  # 删除test分支
git switch test # 切到test分支
git switch -c test # 创建并切到test分支

```

合并

```shell
# 当我们在update上加了新的代码 我们想要main分支也同步 需要:
git switch main # 切到main分支
git merge update  # 将update的代码合并给main
```



### 变基 (rebase)

我们通过merge合井分支时，在提交记录中会将所有的分支创建和分支合并的过程全部都是示出来，这样当项目比较复杂，开发过程比较波折时，我必须要反复的创建、合并、副除分支。这样一来将会使得我们代码的提交记录变得极为混乱。

### 原理

1.当我们发起变基时，git会首先找到两条分支的最近的共同祖先

2 ﻿﻿对比当前分支相对于祖先的历史提交，并且将它们提取出来存储到一个临时文件中

3 将当前部分指向目标基底

4 以当前基底开始 重新执行历史操作

```shell
git rebase master #将当前分支的基底连到master
git switch master #切回主分支
git merge iss2 #iss在master分支后 合并到iss2
git branch -d iss2 
```



## 远程仓库

目前对于git所有操作都是在本地进行的。在开发中不能这样，这时我们就需要一个远程的git仓库。远程的git仓库和本地的本质没有什么区别，不同点在于远程的仓库可以被多人同时访问使用，方使我们协同开发。在实际工作中，git的服务器通常由公司搭建内部使用或是购买一些公共的私有git服务器, 比如**GitHub**和**Gitee**



```shell
git remote 			# 列出当前关联的远程库
git remote add <远程库名> <URL> 
git remote add origin https://github.com/KAGA11/git123.git		# 这个在GitHub里就叫git123
git branch -M main 		#修改分支的名字为main 现在vscode都是main应该不用了
git push -u origin main # origin和add 后的名字一致    main和branch 里改的主分支名字一致

git remote -v
origin  https://github.com/KAGA11/git.git (fetch) #下载
origin  https://github.com/KAGA11/git.git (push)	#上传

# 需要现在本地commit之后再push 直接push不变
git push -u <远程库名> <分支名> 
git push 	# 上部操作关联了 可省略

git clone <URL> #从远程库下载代码
git fetch  #从远程获取代码库 不会和本地代码进行合并 使用fetch拉取代码后 需要手动与远程合并
					 # git merge origin/main 和远程main分支合并 
git pull   # 下载代码并合并	fetch + merge


git remote remove origin #删库跑路
```



### 标签

如果你达到一个重要的阶段，并希望永远记住那个特别的提交快照，你可以使用 git tag 给它打上标签。

比如说，我们想为项目发布一个"1.0"版本。 我们可以用 git tag -a v1.0 命令给最新一次提交打上（HEAD）"v1.0"的标签。

-a 选项意为"创建一个带注解的标签"。 不用 -a 选项也可以执行的，但它不会记录这标签是啥时候打的，谁打的，也不会让你添加个标签的注解。 

```shell
git tag -a v1.0   # 给该文件打一个1.0的tag
git tag 					# 查看所有版本
git switch v1.0   # 直接返回这个打tag的节点
git push 远程仓库 标签名
git tag -d 标签名  # 删除标签
git push 远程仓库 -delete 标签名  # 删除远程标签
```



#### 分离头指针

当头指针没有执行某个分支的头部时，这种状态我们环为分离头指针 (HEAD detached)，分离头指针的状态下也可以操作代码，

```shell
git log --oneline --decorate --graph # 这个前面的字符(哈希值)就是节点名字
* 70f3067 (HEAD -> main, tag: v1.0) 选购
* 9370622 (origin/main) 修改电棍
* 7474935 3
* 098b77f modified 2
* 4b7f0f5 create

git switch -c <分支名> <提交id> # 创建节点 提交id 就是前面的字符
```



#### gitignore

`.gitignore` 是一个用于指定 Git 忽略哪些文件和文件夹的配置文件。当你在 Git 仓库中创建一个 `.gitignore` 文件并列出要忽略的文件和模式时，Git 将不会跟踪或包括这些文件和模式的更改。比如node_modules 和 yarn.lock



#### GitHub 静态页面

在GitHub中 可以将自己的静态页面部署到GitHub中 他给我们提供一个新的地址使我们的页面变成一个新的网站 可以供用户访问

要求

​		分支必须叫做 gh-pages

```shell
git branch -M gh-pages
git push -u origin gh-pages
```

在GitHub settings里找pages 



### docusaurus

facebook推出的开源的静态的内容管理系统 通过它可以快速布置一个网站

网址

```markdown
https://docusaurus.io/
```

安装

```shell
npx create-docusaurus@latest my-website classic
```

启动项目

```
npm start
```

配置项目

docusaurus.config.js 项目配置文件

​						url 换成 github.io

​						deploymentBranch 指定分支'gh-pages'

​						环境变量 GIT_USER : KAGA11



详情: https://www.bilibili.com/video/BV1124y117Dr?p=14&spm_id_from=pageDriver&vd_source=e11047dcc58c275a9f01e970db0fdc33

