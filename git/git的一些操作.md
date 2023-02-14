# GIT 操作总结

## 1. git 版本回退

### 三棵树

首先来了解三棵树

|树|用途|
|:---:|:---:|
|HEAD|上一次提交的快照，下一次提交的父结点|
|Index|git add [filename] 存放的索引（暂存区）|
|Working Directory|工作区|

#### 1. 在我们修改代码，没有 git add, 也没有 git commit 的时候，代码存在于工作区，如图
![](https://user-gold-cdn.xitu.io/2019/3/13/16975a2ff36abc3c?w=679&h=521&f=png&s=31765)


#### 2. 当你 `git add file.txt` 的时候，索引区 index 便有了此文件
![](https://user-gold-cdn.xitu.io/2019/3/13/16975a7d3a41dec1?w=672&h=572&f=png&s=42432)

#### 3. 接着运行 git commit，它会取得索引中的内容并将它保存为一个永久的快照，然后创建一个指向该快照的提交对象，最后更新 master 来指向本次提交。

![](https://user-gold-cdn.xitu.io/2019/3/13/16975abdedaffd11?w=681&h=581&f=png&s=58542)

#### 4. 修改文件也是按照 file ——（change）—— Working Directory ——（git add）—— Index ——（commit）—— HEAD 的顺序进行，当然我们可以使用 `git status`来查看当前所处的步骤

### git reset 的作用

为了演示这些例子，假设我们再次修改了 file.txt 文件并第三次提交它。 现在的历史看起来是这样的：

![](https://user-gold-cdn.xitu.io/2019/3/13/16975b42fae5c123?w=676&h=545&f=png&s=71768)

git reset 的作用便是将 HEAD 指向的版本进行变更，不同的参数三棵树的状况不一样

## git rebase

### 切开发分支
  ``` 
  git checkout -b feat-a 
  ```
### 将分支合并到feat-a分支
  ```
  git siwtch feat-a

  git rebase dev

  // 如果有冲突解决冲突

  git rebase --continue
  ```
  
#### 推送

##### 若当前feat-a分支只有你自己在开发，没有其他成员的操作，个人建议可用强制推送，一般不建议使用哈
```
git push --force origin feat-a
```

##### 如果有其他人在这个分支上和你一起开发的话，使用命令
```
git push --force-with-lease origin feature
```

## git merge和git rebase的优缺点

git merge

优点：不会破坏原分支的提交记录。
缺点：会产生额外的提交记录，并进行两条分支线的合并。


git rebase

优点：无需新增提交记录到目标分支，reabse后可以直接将对象分支的提交历史加到目标分支上，形成线性提交历史记录，更加直观。
缺点：不能在一个共享分支上进行reabse操作，会带来分支安全问题。

## git merge和git rebase的正确使用

合代码到公共分支的时候使用git merge，书写正确规范的merge commits留下记录。
合代码到个人分值的时候使用git rebase，可以不污染分支的历史提交记录，形成简介的线性记录。


写法 `git reset HEAD~2` `git reset 4564sf5s`

1. `git reset --soft HEAD`

Git重置HEAD到另外一个commit，索引区index，工作区无变化

2. `git reset HEAD`

即 git reset --mixed HEAD , 它将重置HEAD到另外一个commit,并且重置index以便和HEAD相匹配，工作区无变化。

3. `git reset --hard`

--hard参数将会 blow out everything.它将重置HEAD返回到另外一个commit，重置index以便反映HEAD的变化，并且重置工作区也使得其完全匹配起来。

这是一个比较危险的动作，具有破坏性，数据因此可能会丢失！如果真是发生了数据丢失又希望找回来，那么只有使用：git reflog命令了。
