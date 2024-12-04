# Git CLI 使用基本方法

> 该笔记参考自[菜鸟教程|Git 教程](https://www.runoob.com/git)、[技术蛋老师|Git工作流和核心原理 | GitHub基本操作 | VS Code里使用Git和关联GitHub](https://www.bilibili.com/video/BV1r3411F7kn)

> Git 是一个开源的分布式版本控制系统，用于敏捷高效地处理任何或小或大的项目。

## 基本概念

- 工作区：电脑中实际看到的项目
- 暂存区：英文叫 stage 或 index。一般存放在 `.git `目录下的 index 文件（.git/index）中，所以我们把暂存区有时也叫作索引（index）
- 版本库：每次提交之后的记录

可以使用`git add`命令将文件提交到暂存区：
```bash
git add filename       # 将单个文件添加到暂存区
git add .              # 将工作区中的所有修改添加到暂存区
git status             # 查看哪些文件在暂存区中
```
当完全准备完成之后，可以一口气将暂存区中的文件提交到本地的版本库中：
```bash
git commit -m "Commit message"   # 将暂存区的更改提交到本地版本库
git commit -a -m "Commit message" # 暂存并且全部提交到本地版本库
git log                          # 查看提交历史
git log --online                 # 查看远程仓库的提交历史
git diff                         # 查看工作区和暂存区之间的差异
git diff --cached                # 查看暂存区和最后一次提交之间的差异
```
之后再使用`git push`将本地仓库推送到远程仓库中：
```bash
git push origin branch-name
```

[![](https://mermaid.ink/img/pako:eNqrVkrOT0lVslJKL0osyFDwCbKOyVMAAsfop9uXPtk752nPrlgFXV2FxJQUIGWn4BT9bFbT07UzQOIQlU4g6eT83NzMErAK5-hnc9Y8nbPhye7JT3dNhipyBikqKC3OACtxiX6xf87zFd0oSlzA5uTk56WC1Tgq6SjlphblJmamAF1XDVITo1SSkZqbGqNkBWQmJRYDWTF5tUB1iaUl-cGVeclKViVFpak6SkX5pekZSlZpiTnFQF5pQUpiSapLZiLQi7lw0YLEvKj8fAQ_NSWzJL_IFxIYyfl5aZnpSrUARcFodQ?type=png)](https://mermaid.live/edit#pako:eNqrVkrOT0lVslJKL0osyFDwCbKOyVMAAsfop9uXPtk752nPrlgFXV2FxJQUIGWn4BT9bFbT07UzQOIQlU4g6eT83NzMErAK5-hnc9Y8nbPhye7JT3dNhipyBikqKC3OACtxiX6xf87zFd0oSlzA5uTk56WC1Tgq6SjlphblJmamAF1XDVITo1SSkZqbGqNkBWQmJRYDWTF5tUB1iaUl-cGVeclKViVFpak6SkX5pekZSlZpiTnFQF5pQUpiSapLZiLQi7lw0YLEvKj8fAQ_NSWzJL_IFxIYyfl5aZnpSrUARcFodQ)

## 几个比较重要的文件状态
1. 工作区内文件
  - 未追踪：untracked、unstage
2. 暂存区内文件
  - 已追踪：stage

## 初始化本地Git
1. git config全局设置
```bash
git config --global user.name "username" # 设置用户名
git config --global user.email username@emailadress # 设置用户邮箱
git config --global init.defaultBranch main # 设置本地默认分支为`main`
```

2. 本地git仓库操作
```bash
git init # 在本地仓库开启版本控制
git status # 查看git信息
git log # 查看git的版本信息
```

下面是`git status`试例
```bash
On branch main # 当前正处于主分支
Your branch is up to date with 'origin/main'.

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed) # 提示需要使用 git add来提交缓存区
  (use "git restore <file>..." to discard changes in working directory) # git restore来回滚版本
  (commit or discard the untracked or modified content in submodules)
        modified:   _config.anzhiyu.yml
        modified:   scaffolds/post.md
        modified:   themes/anzhiyu (modified content, untracked content)

Untracked files: # 这里显示的是尚未提交到缓存区的文件，注意`untracked`
  (use "git add <file>..." to include in what will be committed)
        "source/_posts/Tech/Git\347\254\224\350\256\260.md"

no changes added to commit (use "git add" and/or "git commit -a")
```

`.gitignore`文件的作用：该文件内的文件将不会被提交到git中，可以是单个文件，也可以是一个文件目录

```
.DS_Store
Thumbs.db
db.json
*.log
node_modules/
public/
.deploy*/
_multiconfig.yml
```

## git中的分支

![](https://img.duckk.org/2024/11/d56baad7d538034ae40efb4ff86a0daa.png)

> 使用分支意味着你可以从开发主线上分离开来，然后在不影响主线的同时继续工作。

查看本地存在的所有分支：
```bash
git branch name
```

创建新分支：
```bash
git switch -c name # 创建一个分支，并且跳转到该分支上
```
以下似乎在新版本的git中不推荐使用：
```bash
git branch name # 只创建，并不会直接跳转到该分支
git checkout -b name # 创建并跳转到该分支
```


改变某一分支的名字：
```bash
git branch -m name # 修改当前某一分支名
git branch -M name # 强制修改
```
切换到该分支：
```bash
git checkout name
```

删除某一分支：
```bash
git branch -d name # `d`代表delect
git branch -D name # 强制删除某一分支，在实际生产中并不推荐
```

合并分支：
```bash
git merge name # 注意是从别的分支合并到当前分支
```

## GitHub/GitLab操作

直接使用`Download ZIP`是没有版本控制功能的，应当使用`git clone`

```bash
git clone link.git
```

查看本地仓库和哪些远程仓库源有联系：
```bash
git remote # 只会显示远程仓库的别名
git remote -v # 显示远程仓库的别名和地址
```

这里的`origin`表示的是远程仓库的名字，默认都会使用`origin`来表示远程仓库的名字：
```bash
git remote
origin
---
git remote -v
origin  https://github.com/Aicnal/anzhiyu.git (fetch)
origin  https://github.com/Aicnal/anzhiyu.git (push)
```

添加远程仓库别名：
```bash
git remote add origin link # 其中`origin`是仓库的别名，可以自定义，但一般为`origin`；`link`则为远程仓库的地址
git remote set-url origin link # 其中`set-url`为修改仓库链接到指令，该操作用来修改远程仓库的地址
```

更新本地与远程仓库：
```bash
git push
```
在2021年GitHub已经取消了使用用户名和密码进行`push`，在这里我们需要去设置`Token`或者使用`GitHub CIL`

更新远程代码到本地：
```bash
git fetch
```

此时可以查看本地和远程仓库的区别：
```bash
git diff origin/main
```

确认可以同步之后，将远程仓库和本地仓库整合起来：
```bash
git pull
```

一般来说，应该先将远程仓库中的内容同步到本地：
```bash
git pull origin main
```

如果拉取的时候出现了冲突，可以尝试使用`--rebase`，这里的操作是先将远程的仓库拉到本地，远程仓库的commit变为老的一条，本地的commit则变为最新的一条
```bash
git pull --rebase origin main
```

将本地仓库的`main`分支同步到远程仓库中，本地文件夹名和仓库名可以不一样
```bash
git push origin main # 制定同步的仓库和分支
git push -u origin main # 第一次最好使用这个指令，将本地的与远程的改为默认
```

### Issue
在`commit`的时候可以添加#1来制定专门针对与某一Issue的提交
```bash
git commit -m "针对#1"
```

### Pull Request / Fork
在尝试`pull request`之前，需要自己先创建一个与`main`不同的分支，之后再进行`pull`操作
```bash
git pull origin new-branch # 此时`new-branch`即指定的新分支名
```

在成功`pull request`之后，GitHub会自动删除该分支，但是本地并不会自动删除该分支`new-branch`，此时正确的操作应该是：

1. 先将远程的内容拉取下来`git pull`，先更新本地的`main`分支
2. 再删除`new-branch`小分支，`git branch -d new-branch`，注意最好不要使用`-D`这种参数强制删除，容易犯错误！

`fork`类似于将别人的仓库拷贝到自己的仓库，在进行`fork`操作前，此时应该：

1. 先创建一个新的分支，一切操作将在这个新分支内完成，接着`add`、`commit`，一些操作应该按照原仓库作者的规范
2. 再进行`push`

### Tag / Release
`tag`标签类似于版本号，用来表示某一阶段的工作的完成，`tag`就像照片一样，记录着那一刻的`commits`记录

```bash
git tag -a v1.0.0 -m "comment" # 这里的`a`是`annotation`，注解的意思
git tag -d v1.0.0 # `-d`即为删除对应的tag，删除本地的tag
git push origin --delete v1.0.0 # 删除远程仓库的tag
```

`release`的发行版其实是基于`tag`标签的