# git

## 常见命令

- `git init` ：初始化一个受版本控制的仓库
- `git add src/` ：添加文件夹 src 到暂存区
- `git add pom.xml` ：添加文件到暂存区
- `git commit -m "comment"` ：版本提交(将暂存区的改动提交到版本控制中)
- `git reset --hard HEAD^` ：回退到上一个版本，HEAD 表示当前版本
- `git checkout -- xx.txt` ：撤销 xx.txt 文件的修改

## 添加远程仓库

- `git init`
- `git remote add origin git@github.com:Bjorne1/mall.git`
- `git push -u origin master` ：把本地仓库推送到远程仓库
  - 我们第一次推送 master 分支时，加上了-u 参数，Git 不但会把本地的 master 分支内容推送的远程新的 master 分支，还会把本地的 master 分支和远程的 master 分支关联起来，在以后的推送或者拉取时就可以简化命令。
  - git 拒绝解决办法一：`git pull origin master --allow-unrelated-histories`

## 从远程仓库克隆

- `git clone git@github.com:Bjorne1/springBootDemo.git`

## 版本回退

- `show history`: newVersion:2965a8e4d76fbf43395a89b438bde069a3afffab
  oldVersion:d6911d54c4526c71948e7899fc8dad92f1d4bea6
- 先在 `history` 中复制需要回退版本的版本号和当前版本号
- Git->Repository->Reset HEAD 中输入 oldVersion,`Reset Type:*Hard`.
- 此时 push 到 github 会提示冲突
- Git->Repository->Reset HEAD 中输入 newVersion,`Reset Type:*Mixed`.
- `commit` 之后再 `push`,就不再有冲突了。

## Permission denied (publickey)

- `ssh-keygen -t rsa -C 973725819@qq.com`
- 生成的 id_rsa.pub 在`C:\Users\wcs\.ssh`
- 复制 id_rsa.pub 到 github SSH key 那里

## git 之忽略文件.gitignore

- 在 git bash 中 `vim .gitignore` 或者 `touch .gitignore`
- `i` 进入到编辑模式
- `esc :eq` 保存并退出，`:q` 退出(未修改)，`:!` 强制退出(修改后不保存退出)

## git 结合 idea

- 先在设置中 git，github 中设置好相关内容.
- 本地文件改动后 `commit`。
- 先 `pull`，再 `push`，github 就与本地仓库同步了。
