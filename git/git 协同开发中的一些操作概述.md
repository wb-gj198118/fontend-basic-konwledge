**版本管理**，**协同开发**

## 了解Git

![162fcc0987bf1c0a_tplv-t2oaga2asx-zoom-in-crop-mark_1304_0_0_0.webp](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/eef0beaa4fc14c7b9f17ddd5d257575c~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

### 组成

-   **工作区**\-workspace：本地电脑存放项目文件的地方。
    
-   **暂存区**\-Index：在使用git管理项目文件的时候，其本地的项目文件会多出一个.git的文件夹，将这个.git文件夹称之为版本库。其中.git文件夹中包含了两个部分，一个是暂存区,顾名思义就是_暂时存放文件_的地方，通常使用`add`命令将工作区的文件添加到暂存区里。
    
-   **本地仓库**\-Repository：.git文件夹里还包括git自动创建的master分支，并且将HEAD指针指向master分支。使用`commit`命令可以将暂存区中的文件添加到本地仓库中。
    
-   **远程仓库**\-Remote：不是在本地仓库中，项目代码在远程git服务器上，比如项目放在github上，就是一个远程仓库，通常使用_clone_命令将远程仓库拷贝到本地仓库中，开发后推送到远程仓库中即可。
    

> 暂存区和本地仓库组成了.git文件。

## git config------**配置相关**

查看配置信息：`git config --list`

设置用户信息

```js
git config --global user.name "XXX"

git config --global user.email 123123123@qq.com

```

只设置当前仓库信息

```js
//方法1  去掉--global
git config  user.name "XXX"
git config  user.email 123123123@qq.com

//方法2 使用--local
git config --local user.name "XXX"
git config --local user.email 123123123@qq.com
```

查看缓存记录：

`git log`

### alias---设置别名

`git config --global alias.新命令名称 要修改的命令名称`

### 设置默认选项

`git config --global 配置项=新值`

> -   \--local 局部配置
> -   \--global 全局配置
> -   \--system 系统配置
> -   git读取优先级：local 》 global 》 system

## 可能的场景

### 将项目克隆到本地

#### 搞一个新本地仓库：

1.  `git init` **初始化仓库**
    
2.  `git clone <url> 新名称(可选参数)` **远程仓库->本地仓库**即把云端代码拷贝到本地。
    

#### 有仓库了，远程仓库有，我也想要：

-   从远程仓库中抓取本地仓库中没有的更新-`git fetch [remote-name]`

> 如`git fetch origin`;使用fetch只是将远端数据拉到本地仓库，并不自动合并到当前工作分支，只能人工合并。

> -   在远程分支的基础上创建新的本地分支`：git checkout -b <branch-name> <remote-name>/<branch-name>` 即：从本地仓库到工作区的过程。

-   直接将远程仓库的文件抓取到工作区-`git pull`

> 如果设置了某个分支关联到远程仓库的某个分支的话，可以使用`git pull`来拉去远程分支的数据，然后将远端分支自动合并到本地仓库中的当前分支；

### 在本地仓库建一个新分支，然后住进去

#### 从无到有

方法1-分步实现

> -   创建分支：`git branch <branch-name>`，如`git branch testing`；
> -   从当前所处的分支切换到其他分支：`git checkout <branch-name>`，如`git checkout testing`；

方法2-一步到位

> -   新建并切换到新建分支上：`git checkout -b <branch-name>`;

#### 装修加固

> -   在远程分支的基础上创建新的本地分支`：git checkout -b <branch-name> <remote-name>/<branch-name>`

### 将项目从本地提交到远程仓库

1.  `git add .` **工作区->暂存区**
    
2.  `git commit -m ‘注释’`**暂存区->本地仓库**
    
3.  `git push` **本地仓库->远程仓库**
    

> add
> 
> -   提交工作区所有文件到暂存区：`git add .`
> -   提交工作区中指定文件到暂存区：`git add <file1> <file2> ...`
> -   提交工作区中某个文件夹中所有文件到暂存区：`git add [dir]`

> commit
> 
> -   将暂存区中的文件提交到本地仓库中，即打上新版本：`git commit -m "commit_info"`;
> -   将所有已经使用git管理过的文件暂存后一并提交，跳过add到暂存区的过程：`git commit -a -m "commit_info"`;
> -   提交文件时，发现漏掉几个文件，或者注释写错了，可以撤销上一次提交：`git commit --amend`;

push

> -   `git push [remote-name] [branch-name]`，如`git push origin master`。
> -   如果想将本地分支推送到远程仓库的不同名分支：`git push <remote-name> <local-branch>:<remote-branch>`，如`git push origin serverfix:awesomebranch`。
> -   如果想删除远程分支：`git push [romote-name] :<remote-branch>`，如`git push origin :serverfix`。这里省略了本地分支，也就相当于将空白内容推送给远程分支，就等于删掉了远程分支。

### 干错了，要把错误证据干碎！！即：撤销

#### 暴力的删除和撤销

-   删除工作区文件，并且也从暂存区删除对应文件的记录：`git rm <file1> <file2>`;
    
-   从暂存区中删除文件，但是工作区依然还有该文件:`git rm --cached <file>`;
    
-   撤销上一次对文件的操作：`git checkout --<file>`。
    

#### 其他场景

-   如果想保留上一次的修改以备以后继续工作，可以使用stashing和分支来处理；
    
-   取消暂存区已经暂存的文件：`git reset HEAD <file>...`;
    
-   隐藏当前变更，以便能够切换分支：`git stash`；
    
-   查看当前所有的储藏：`git stash list`；
    
-   应用最新的储藏：`git stash apply`，如果想应用更早的储藏：`git stash apply stash@{2}`；重新应用被暂存的变更，需要加上`--index`参数：`git stash apply --index`;
    
-   使用apply命令只是应用储藏，而内容仍然还在栈上，需要移除指定的储藏：`git stash drop stash{0}`；如果使用pop命令不仅可以重新应用储藏，还可以立刻从堆栈中清除：`git stash pop`;
    
-   在某些情况下，你可能想应用储藏的修改，在进行了一些其他的修改后，又要取消之前所应用储藏的修改。Git没有提供类似于 stash unapply 的命令，但是可以通过取消该储藏的补丁达到同样的效果：`git stash show -p stash@{0} | git apply -R`；同样的，如果你沒有指定具体的某个储藏，Git 会选择最近的储藏：`git stash show -p | git apply -R`；
    

### 重命名文件，并将已改名文件提交到暂存区

`git mv [file-original] [file-renamed]`

### 查看信息。

查看**工作区**信息：

-   查询当前工作区所有文件的状态：`git status`;
    
-   比较工作区中当前文件和暂存区之间的差异，也就是修改之后还没有暂存的内容：`git diff`；
    
-   指定文件在工作区和暂存区上差异比较：`git diff <file-name>`;
    

查看**暂存区**信息：

-   比较暂存区与上一版本的差异：`git diff --cached`;
    
-   指定文件在暂存区和本地仓库的不同：`git diff <file-name> --cached`;
    
-   查看提交历史：git log
    

> git log
> 
> -   参数`-p`展开每次提交的内容差异，用`-2`显示最近的两次更新，如`git log -p -2`;

### 在本地仓库管理蹂躏分支，玩死她

#### 先了解情况，伺机而动

-   显示本地仓库的所有分支：`git branch`
    
-   查看各个分支最后一个提交对象的信息：`git branch -v`
    
-   查看哪些分支已经合并到当前分支：`git branch --merged`
    
-   查看当前哪些分支还没有合并到当前分支：`git branch --no-merged`
    

#### 嗷嗷！动手！！！

分支删除

> -   删除分支：`git branch -d <branch-name>`

分支合并

> -   将当前分支与指定分支进行合并：`git merge <branch-name>`
> -   把远程分支合并到当前分支：`git merge <remote-name>/<branch-name>`

分支推送及拉取

> -   在跟踪分支上合并远程分支：`git pull`，即直接从远程仓库到本地仓库的过程
> -   在跟踪分支上向远程分支上推送内容：`git push`
> -   将一个分支里提交的改变移到基底分支上重放一遍：`git rebase <rebase-branch> <branch-name>`

> git push
> 
> -   从远程分支checkout出来的本地分支，称之为跟踪分支。在跟踪分支上向远程分支上推送内容：`git push`。该命令会自动判断应该向远程仓库中的哪个分支推送数据；

### 在本地对远程仓库仓库下手

-   查看远程仓库

> `git remote`；在克隆完每个远程仓库后，远程仓库默认为`origin`;加上`-v`的参数后，会显示远程仓库的`url`地址；

-   添加远程仓库

> 一般会取一个简短的别名：`git remote add [remote-name] [url]`，比如：`git remote add example git://github.com/example/example.git`;

-   查看远程仓库的详细信息

> `git remote show origin`；

-   修改某个远程仓库在本地的简称

> `git remote rename [old-name] [new-name]`，如`git remote rename origin org`；

-   移除远程仓库

> `git remote rm [remote-name]`；

### 我有宝贝不想被Git发现要藏起来

**忽略文件的名字叫.gitignore**

一般我们总会有些文件无需纳入 Git 的管理，也不希望它们总出现在未跟踪文件列表。通常都是些自动生成的文件，比如日志文件，或者编译过程中创建的临时文件等。我们可以创建一个名为 .gitignore 的文件，列出要忽略的文件模式。如下例：

```js
# 此为注释 – 将被 Git 忽略
# 忽略所有 .a 结尾的文件
*.a
# 但 lib.a 除外
!lib.a
# 仅仅忽略项目根目录下的 TODO 文件，不包括 subdir/TODO
/TODO
# 忽略 build/ 目录下的所有文件
build/
# 会忽略 doc/notes.txt 但不包括 doc/server/arch.txt
doc/*.txt
# 忽略 doc/ 目录下所有扩展名为 txt 的文件
doc/**/*.txt
```

## 附：文件状态

### 上行

1.  没有被add过的文件叫**untracked**。
    
2.  add之后文件处于**staged**状态等待commite。
    
3.  commit之后文件处于**unmodified**，这里之所以是modified是因为文件会跟仓库中的文件对比。
    

### 变更

1.  当unmodified的文件_被修改_则会变为**modified**状态。
    
2.  modified之后的文件add之后将继续变为staged状态。
    

### 其他

-   unmodifed的文件还有一种可能是已经不再需要了，那么可以remove它_不再追踪变_为**untracked**状态。

## 参考

[git基本操作，一篇文章就够了！](https://juejin.cn/post/6844903598522908686#heading-4 "https://juejin.cn/post/6844903598522908686#heading-4")