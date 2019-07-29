# Git 实践

主要涉及到一下几个部分

1. 基本使用规范
2. 基本使用操作
3. 合并多个 commit
4. 解决冲突
5. 常用操作

## 基本使用规范

git 使用，需要有一个规范，让大家更好一起开发，比较代码混乱。

下面大意就是，分多个分支管理不同环境的代码，commit 信息简单明了， commit 条数需要克制一下。

- 三个主要分支，一堆其他分支
  - master: 生产环境代码
  - pre：预发布环境代码，真实生产环境和数据，用于上线前校验功能
  - dev：开发分支，用于给测试老师测试用，该分支 commit 几十个没关系，但是合到 pre，master 的需要减少 commit 次数。
  - featue/zx/xxxx: 各种需求分支
- 一个需求，就拉出一个写的分支来写代码
- 分支的名称规范
  - 新需求 : `feature/your_name/feature_name`
  - 修复线上 bug: `hotfix/your_name/bug_name`
  - 添加文档 : `doc/your_name/doc_name`
- commit 的信息需要分类
  - 目的是：写清楚这个 commit 是干嘛的，中英文没严格要求
  - 新需求提交: `git commit -am 'feature: add some feature'`
  - 测试老师的 bug 修复： `git commit -am 'fixed: bug1, bug2'`
- 合并到 master
  - 合并到 master 不要有几十个 commit（正常是修复几个 bug，就上测试环境给测试老师回归，因此这么多）
  - 合并 commit 信息 `git rebase -i origin/master`
- merge 到 master 的分支，则可以删除掉
- 如果长期迭代的产品，建议在打各个版本的 Tag（eg: v2.0.0, v2.5.1）

> 规范是自己团队来定，并没有『望之之天下皆可用』的标准。只要能够让大家更好的配合，协调就是一个有用的规范。

## 常规使用

```bash
# 添加一个新文件
echo 'hello world' > 1.txt

# 查看一下 git 状态
git status

# 添加到暂存区
git add .

# 创建一个 commit，并添加 commit 信息
git commit -am 'feature: add 1.txt'

# 提交代码
git push
```

## 合并多个 Commit

```bash
# 从 master 来一个新分支来进行新需求的开发
git checkout -b feature/zx/feature1 origin/master

# 一顿操作，写一堆 BUG，然后提交到 dev分支，部署给测试老师测试
# 测试老师一顿操作，给你提了一堆 BUG
# 因此你开始下面的 fixed bug 征途

# 修复一个 bug
echo 'fixed bug1' > 2.txt
git add .
git commit -am 'hotfix: bug1'
git push

# 又修复一个 bug
echo 'fixed bug2' > 3.txt
git add .
git commmit -am 'hotfix: bug2'
git push

# merge 到 dev ，继续给测试老师做回归测试

# 测试老师验证没问题，准备上线， 把多个 commit 提交到 master会让 master 的 commit 看起来很乱，不优雅。
# 那么是否可以把这个需求的所有 commit 合并成一个。

# feature/zx/feature1 分支下
git rebase -i origin/master

# 给出大概这样的提示
hint: Waiting for your editor to close the file...
1 pick 99cdb9d hotfix: package.json add start command
2 pick 1909b7f feature: add react
3 pick 4f1ac67 feature: add .gitignore
# ... 省略一部分

# 简单解释下 pick , squash , fixup 几个命令得使用。 其他命令没怎么用过，需要的话自己去了解下。
# pick [p]  ：继续使用该 commit， 不合并commit
# squash [s]：合并该 commit，并继续用该 commit 的提交信息（eg: feature: add react）
# fixed [f]  ：合并该 commit，但是不使用 commit 的提交信息

# 比如改成这样
hint: Waiting for your editor to close the file...
1 pick 99cdb9d hotfix: package.json add start command
2 s 1909b7f feature: add react
3 f 4f1ac67 feature: add .gitignore

# 最终合并出来的 commit 有两个
# 第一个 commit
hotfix: package.json add start command
feature: add react

#  第二个 commit
add .gitignore
```

## 解决冲突

多人协作的 Git 仓库中，代码冲突是一个比较常见的问题。

问题所在：有两个人修改了同一个文件，同一段代码，git 不知道要与你们谁的代码为准，就让你们自己去解决冲突，在合并到 master。

正常来说，先提交代码的人不会冲突，然后第二个人提交代码，就发现自己冲突了。 因为自己和上一个人提交的代码冲突了。

常用的解决方案：

```bash
#把 master 的代码合并到自己分支
git merge origin/master

# 哪里冲突，解决哪里（最好问下上一个人，哪些是他的最新的代码，避免把别人代码搞没了）
# 解决完成

git add .
git commit -am 'fixed confict'
git push

```

## 常用操作

### 拉取最新代码

```bash
# 拉取远程 master 分支的最新代码，并合并到当前分支
git pull --rebase origin/master
```
