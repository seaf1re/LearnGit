# 1 git简介和安装

## 1.1 git介绍

- git是一个`分布式` **版本控制**系统

  > 集中式：完整的版本库集中存放在中央服务器，必须联网/局域网才能工作，中央服务器挂了所有的都挂了
  >
  > 分布式：每个人本地保存的都是完整的版本库，commit不需要网络，一个挂掉也可以pull其他地方的副本。必须有网络才能推送/拉取远程服务器（github和gitee充当这个角色）

- 2005年，LINUS花了两周时间用C语言写了git

## 1.2 git安装

```bash
sudo apt-get install git
#设置名字和邮箱,--global参数表示所有git仓库都会使用这个配置
git config --global user.name "Your Name"
git config --global user.email "email@example.com"
```

## 1.3 工作区和版本库

![](/home/seaf1re/learnspace/learngit/img/版本库和工作区.png)

- 工作区：存放git的目录，工作区是版本库/分支/暂存区共享的
- 版本库：即隐藏目录.git，包括`暂存区(stage)` 和 `分支(branch)`。
  - 暂存区：git add <filename>命令将工作区的文件放入暂存区。所有的分支的暂存区也是公共的。
  - 分支：git commit -m <messages> 将暂存区的文件放入当前分支。

# 2 git的本地/主要操作

## 2.1 版本库

### 2.1.1 创建版本库

- 可以从本地创建，然后在github/gitee创建repo，二者关联起来，从本地推送修改到远程

  ```bash
  #本地创建版本库，此时创库为空，但有.git隐藏目录(即版本库)，可以使用ls -ah查看
  git init
  #远程创建和关联见3.1.2
  ```

- 拉取远程repo

  ```bash
  git clone <git address>
  #如果是克隆别人的仓库，建议先fork到自己的仓库，然后克隆到本地，这样就可以修改/提交了
  ```

### 2.1.2 添加/修改文件

文件做了修改之后，需要重新执行下面的步骤。

```bash
git add <filename> #需要指定文件名，可反复添加多个文件
git commit -m <message> #将暂存区的所有文件全部提交到分支
git diff <filename> #查看修改内容
```



## 2.2 版本控制

### 2.2.1 仓库状态查看

```bash
git status
```

### 2.2.2 版本回退

git内部有指向当前版本的`HEAD指针`，版本回退只是移动指针的位置，指向不同的commit

```bash
#查看历史提交，找到需要回退的commit id
git log [--pretty=oneline -abbrev-commit]
#利用commit id 回退
git reset --hard <commit id>
#如果只是在最近的版本进行回退，使用HEAD指针即可
git reset --hard HEAD^ #回退一个版本，^^表示两个版本，类推

#如果回退了历史版本，想要回到未来，可查看命令记录。用提交记录是不可行的，因为已经没有了未来的提交
git reflog
```

### 2.2.3 撤销修改

```bash
#工作区 撤销修改
git checkout -- <filename>
git restore -- <filename>

#暂存区 撤销修改
git restore --staged <filename>
git reset HEAD <filename>

#版本库 撤销修改
#参见2.2.2 版本回退
```

### 2.2.4 删除文件

```bash
#工作区删除
rm <filename>
#版本库删除
git rm <filename>
git commit -m <message>
#工作区删除错误，可以从版本库恢复：其实就算用版本库的版本替换工作区版本
git checkout -- <filename>
```

## 2.3 分支管理

### 2.3.1 创建与合并分支

```bash
#创建分支
git branch <branch-name>
#查看分支
git branch 
#切换分支
git switch <branch-name>
#创建并切换分支
git switch -c <branch-name>
#合并某分支到当前分支
git merge <branch-name>
git merge --no-ff -m <message> <branch-name>
#删除分支
git branch -d <branch-name> #丢弃已经合并过的分支
git branch -D <branch-name> #强制丢弃没有被使用的分支

#可以查看分支合并图
git log --graph
```

### 2.3.2 解决冲突

不同的分支在合并的时候，如果产生冲突，将无法自动合并，此时需要将合并失败的文件手动解决冲突，再提交即可（此时将自动完成合并）。

### 2.3.3 暂存工作区

```bash
#暂存工作现场，可以让用户先保存还不能commit的文件，转手去处理比较紧急的bug
git stash
#查暂存区
git stash list
#恢复&&删除
git stash apply
git stash drop
#一条指令完成
git stash pop

#制定某次提交作用在当前分支，可以快速修改bug
git cherry-pick <commit-id>
```



## 2.4 标签管理

### 2.4.1 标签创建

标签是打在某个分支的一次commit上的。也就是说，如果某个打了标签的commit出现在不同分支，那么这些分支都可以看到这个tag。

```bash
#给当前分支的最新一次commit打标签
git tag <tag-name>
#给某个版本的commit打标签
git tag <tag-name> <commit-id>
#指定标签信息
git tag -a <tagname> -m <message>
#查看所有标签
git tag
#查看标签的详细信息
git show <tag-name>
```

### 2.4.2 标签操作

```bash
#删除标签
git tag -d <tag-name>

#推送到远程以及删除远程标签见3.3
```



## 2.5 自定义git

### 2.5.1 忽略特殊文件

```bash
#编辑.gitignore文件，加入版本库即可
#强制添加，忽略该文件
git add -f <filename>
#查看命令
git check-ignore -v <filename>
```

### 2.5.2 配置别名

- 每个仓库的git配置在 `.git/config`文件
- 当前用户的git 配置文件放在`.gitconfig`文件

```bash
git config --global alias.st status
#这条命令非常好用
git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"
```



# 3 git的远程操作

## 3.1 创建远程仓库

### 3.1.1 设置SSH key

```bash
#创建ssh key，在主目录的.ssh目录下会生成`id_rsa`和`id_rsa.pub`文件。前者是私钥，后者是公钥。
ssh-keygen -t rsa -C "youremail@example.com"
#将公钥上传到github/gitee的用户设置
#此时，本地持有私钥，远程利用公钥验证推送者的身份
```

### 3.1.2 添加远程仓库

```bash
#方法一：本地仓库关联远程仓库
#首先在github/gitee创建空仓库
#将本地仓库和远程仓库关联起来
git remote add <origin-name> git@github.com:seaf1re/learngit.git
#查看和本地仓库有关联的远程仓库。本地仓库可以和多个远程仓库联系
git remote -v
#将本地仓库的内容推送到远程
git push -u <origin-name> <branch-name> #第一次使用-u命令，可以关联分支
git push <origin-name> <branch-name> #之后直接推送即可
#删除远程仓库
git remote rm <repo-name>

#方法二：本地克隆远程。适用于克隆已经存在远程的库
git clone git@github.com:seaf1re/learngit.git
```



## 3.2 多人协作

### 3.2.1 抓取分支

```bash
#克隆仓库，一般只能获取master分支
git clone git@github.com:seaf1re/learngit.git

#创建本地其他分支并和远程repo联系。这个针对本地还没有dev分支的情况。如果已经有该分支，只需要关联远程分支
git checkout -b dev origin/dev

#修改，保存，推送
git add <filename>
git commit -m "message"
git push origin dev


#此时，如果另一个人也要对dev分支作修改，此时会提示冲突
#需要先抓取最先版本的dev分支，本地合并解决冲突后上传

#先指定本地分支与远程分支的链接
git branch -set-upstream-to=origin/dev dev
#拉取
git pull
#解决冲突，提交，push
git push origin dev
```



### 3.2.2 rebase

## 3.3 标签操作

本地操作见2.4.相关远程操作如下：

```bash
#推送标签到远程
git push origin <tagname>
#推送所有标签
git push origin --tags

#删除标签
#先删除本地，再删除远程
git push origin :refs/tags/<tagname>
```

