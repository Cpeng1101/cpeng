Git 是程序员工作中必不可少的工具之一，如何快速高效的正确使用它是提高工作效率的有效方法。为防止遗忘，总结归纳以下使用场景中需要使用的 Git 命令。

---
## 回退修改

 此时文件修改还未提交（既未执行 git add 和 git commit 操作），还处于工作区中，及时发现可以快速解决：
 ```shell
 git restore .
 git restore [your file]
```

如何已经执行过 git add 还未执行 git commit，那么文件还处于暂存区中：
```shell
git restore --staged .
git restore --staged [your file]
```

若已经执行了 git commit，此时需要使用 git reset 命令出马了：
```shell
# 回退到暂存区
git reset --soft HEAD~
# 彻底回退
git reset --hard HEAD~
```

注意，如果执行`git reset --hard`后后悔了，怎么办？放心，Git 有后悔药可以吃：
```shell
# 找到你需要恢复的提交的上一个提交的 commit id
git reflog
# 时光倒流，两级反转
git reset [commit id]
```

还有一种情况是，你只需要将某个文件回退到上一个或指定版本，而不是所有的提交。那么只需：
```shell
# 找到上一个 commit id，或上上个
git log -- [your file]
# 切过去
git checkout [commit id] -- [your file]
```

当你在查 BUG 时，终于发现是中间某条提交引入的。此时只需要回退该笔问题提交即可，那么就需要使用到 git reserve 命名：
```shell
# 祝你好运，不会遇到冲突的问题
git reserve [commit id]
git reserve [commit id] -m 1
```

---
## 统计提交

我们聪明的 leader 总是喜欢用代码量等于工作量的规则去衡量我们的价值，此时我们必须要对我们的产出做到心中有数：
```shell
# 本地提交
git log --stat
git log --stat [file]

# 某次提交
git show --stat [commit id]
```

---

## 删除分支

一般我们为某个需要都会新建一个新的本地分支，日积月累，本地就会有大量的多余分支。

```shell
# 删除本地分支
git branch -d <local>

# 删除远程分支
git push origin --delete <remote>

# 删除除当前分支以外的其它分支
git branch | xargs git branch -d

# 删除包含指定字符串的分支
git branch | grep 'dev*' | xargs git branch -d
```

---

## 制作补丁 && 应用补丁

如何快速且准确地将我们的改动分享给别人也是我们工作中高频的场景，制作补丁以及应用补丁是我们必不可少的技能。

```shell
# 工作区的修改
git diff > your_patch.patch

# 暂存区的修改
git diff --cached > your_patch.patch

# 已提交
git format-patch -1 [commid id]
git format-patch -2 # 最近的 2 个提交
# 生成两个提交之间的所有补丁（不包含 start_commit）
git format-patch start_commit..end_commit

# 应用补丁
git apply your_patch.patch
git apply --check your_patch.patch # 只检查，不实际执行

# 对于 git format-patch 制作的补丁可以
git am your_patch.patch # 自动提交

# 如果应用补丁时，遇到目录不一致的问题，可以指定目录
git apply --directory=src/ your_patch.patch

# 如果补丁的目录多了，也可以忽略补丁中的目录
git apply -p1 your_patch.patch
```