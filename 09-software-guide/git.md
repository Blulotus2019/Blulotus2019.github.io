## Git开发流程

### 流程

- `develop` 开发分支：开发人员每天都需要拉取/提交最新代码的分支
- `test` 测试分支：开发人员开发完并自测通过后，发布到测试环境的分支
- `release` 预发布分支：测试环境测试通过后，将测试分支的代码发布到预发环境的分支（这个得看公司支不支持预发环境，没有的话就可以不采用这条分支）
- `master` 线上分支：预发环境测试通过后，运营/测试会将此分支代码发布到线上环境
- `hotfix` 分支：在 `master` 发现新的 `bug` 时，需要创建一个 `hotfix`，完成后，合并到 `master` 和 `develop` 分支

**大致流程**

- 开发人员每天都需要拉取、提交最新的代码到 `develop` 分支
- 开发完毕后，开始 **集成测试**，测试无误后提交到 `test` 分支，并发布到测试环境，交由测试人员测试
- 测试环境通过后，发布到 `release` 分支上，进行预发布环境测试
- 预发环境通过后，发布到 `master` 分支上并打上标签 `tag`
- 如果线上分支出现 `bug`，这时开发者应该基于预发布（没有预发布环境就用 `master` 分支），新建一个 `bug` 分支用来临时解决 `bug`，处理完成后申请合并到预发布分支（好处是不会影响正在开发中的功能）

### 克隆指定分支

**场景：要求使用非master分支（如next分支）进行开发**

```python
git clone -b next “git地址”
```

### 优雅的在分支上开发

1、克隆到本地的分支为 `next` 分支，但是不能直接在上面开发，而是需要另外创建分支：

```python
# 创建并切换到新的分支 next-dev，其中 add_login	为分支说明（可不加）
git checkout -b next-dev/add_login	
```

2、在 `next-dev` 分支上开发完后， 将代码提交到本地仓库：

```python
git add .	# ".“的意思就是保存添加所有修改到暂存区
git commit -m “注释” 	# 暂存区中的修改提交到本地仓库，commit 规范可参考 6.1 commit 规范
```

3、将 `next-dev` 分支合并到 `next` 分支：

```python
# 先切换到 next 分支
git checkout next	# 切换之前一定要先执行第二步

# 从远端拉取最新的代码，当你合并分支的时候，可能其他同事又提交了新的内容，所以最好先拉取一次最新的代码
git pull origin next

# 合并（如果合并发现有冲突，可以使用可视化工具查看冲突的代码，如：pycharm、BCompare4
git merge next-dev

# 将本地代码推送到远程
git push origin next:next	# ”:“前面的是本地分支的名字，”:"后面的是远程分支的名字
```

> 注意：合并之前先 `commit`，合并之前先 `git pull`

## 常用命令

```python
git clone git@github.com:Bjorne1/springBootDemo.git # 从远程仓库克隆
git init	 # 初始化本地仓库
git add src/ # 添加文件夹 src 到暂存区
git status	 # 查看状态
git add .	 # 添加到暂存库
git add pom.xml # 添加文件到暂存区
git commit -m 'xxx'	# 版本提交(将暂存区的改动提交到版本控制中)
git push -u origin master	# 推送到远端
git reset --hard HEAD^ # 回退到上一个版本，HEAD 表示当前版本
git checkout -- xx.txt # 撤销 xx.txt 文件的修改
git pull -p # 同步远程新增信息
git remote add origin “HTTPS地址”		# 关联远程仓库

# 从远端拉取最新代码到本地
git pull		# 自动合并
git fetch		# 不合并
```

### 添加远程仓库

```python
git init
git remote add origin git@github.com:Bjorne1/mall.git
git push -u origin master # 把本地仓库推送到远程仓库
#我们第一次推送 master 分支时，加上了-u 参数，Git 不但会把本地的 master 分支内容推送的远程新的 master 分支，还会把本地的 master 分支和远程的 master 分支关联起来，在以后的推送或者拉取时就可以简化命令。
git pull origin master --allow-unrelated-histories #git 拒绝解决办法一
git push -u origin dev #把代码推送到远程dev分支
```



### 分支branch相关命令

```python
git checkout -b feat/add-artical	# 创建新的分支 feat，并切到新分支，add-artical 为对这个分支的描述
git branch -d dev	# 删除 dev 分支
git branch # 查看本地分支
git branch -r # 查看远程分支
git branch [name] # 创建本地分支，注意新分支创建后不会自动切换为当前分支
git checkout [name] #切换分支
git checkout -b [name] #创建新分支并立即切换到新分支
git branch -d [name] #删除分支 d选项只能删除已经参与了合并的分支，对于未有合并的分支是无法删除的。如果想强制删除一个分支，可以使用-D选项
git merge [name] #合并分支，将名称为[name]的分支与当前分支合并
git push origin [name] #创建远程分支(本地分支push到远程)
git push origin :heads/[name] #删除远程分支
```

## 拉取最新代码

```python
# 查看已关联的仓库
$ git remote -v
origin  https://gitee.com/hubery_jun/flask-vuejs-madblog (fetch)
origin  https://gitee.com/hubery_jun/flask-vuejs-madblog (push)

# 类似于 git pull，也是用于拉取最新代码
$ git fetch
# 或拉取指定的远程主机上的分支，如 origin 上的 master
$ git fetch origin master
```

**git fetch 与 git pull 的区别**

- ```
  git fetch
  ```

  - 远端跟踪分支：可以更改远端跟踪分支
  - 拉取：**会将数据拉取到本地仓库，但是不会自动合并或修改当前的工作**
  - `commitID`：本地库中 `master` 的 `commitID` 不变，还是等于 1

- ```
  git pull
  ```

  - 远端跟踪分支：无法对远端跟踪分支操作，必须先切回到本地分支然后创建一个新的 `commit` 提交
  - 拉取：**从远处获取最新版本，并合并到本地，会自动合并或修改当前的工作**
  - `commitID`：本地库中 `master` 的 `commitID` 发生改变，变成了 2

**用法**

1、`git pull`

```python
git pull <远程主机名> <远程分支名>:<本地分支名>
    
# 将远端主机 origin 的 master 分支拉下来，与本地 dev 分支合并，若未指定本地分支（那么将与本地当前分支合并）
git pull origin master:dev
    
# 用 git fetch 表示
git fetch origin master:dev
git merge dev
```

2、`git fetch`

```python
# 方法一
git fetch origin master	# 从远端 origin 仓库的 master 分支拉取最新代码
git log -p master.. origin/master	# 比较本地的仓库和远端仓库的区别
git merge origin/master	# 将远端拉取的最新代码合并到本地仓库，远端和本地的合并

# 方法二
git fetch origin master:temp	# 从远端 origin 仓库的 master 分支拉取最新代码到本地并在本地创建一个新的分支 temp
git diff		# 比较 master 分支和 temp 分支的不同
git merge temp	# 合并 temp 分支到 master 分支
git branch -d temp	# 删除 temp 分支
```

> 注意：`git fetch` 更安全也更符合实际要求，可以在合并前，先查看更新情况，根据实际情况再决定是否合并

## git push 常用用法

一般形式：

```python
git push <远程主机名> <本地分支名>  <远程分支名>

# origin 为远程主机名，将本地 next 分支推送到远程 next 分支
git push origin next:next	
```

省略远程分支：

```python
# 表示将本地分支推送到与之存在追踪关系的远程分支（通常两者同名），如果该远程分支不存在，则会被新建
git push origin master
```

如果省略本地分支名，则表示删除指定的远程分支，因为这等同于推送一个空的本地分支到远程分支，等同于 `git push origin --delete master`：

```python
git push origin ：refs/for/master 
```

如果当前分支与远程分支存在追踪关系，则本地分支和远程分支都可以省略，将当前分支推送到 `origin` 主机的对应分支 :

```python
git push origin
```

如果当前分支只有一个远程分支，那么主机名都可以省略，形如 `git push`，可以使用 `git branch -r` ，查看远程的分支名：

```python
git push
```

## 分支管理

```python
git checkout -b dev		# 创建并切换到分支
git branch -d dev		# 删除分支
git branch			# 查看当前分支
```

## 代码提交

### **添加到存储库、合并分支**

```python
git add .		# 添加所有
git commit -m 'xxxx'	# 提交
git checkout master		# 切换到主分支
git merge dev		# 合并 dev 分支到 master 主分支
git branch -d dev	# 删除 dev 分支
git branch		# 查看当前分支
```

### commit 规范

`commit` 的内容也应该遵守规范，一般来说是

- `1. fix:xx` ：表示修改了XX代码
- `2. feat:xx` ：新增了XX需求
- `3. style:xx` ：修改了部分的样式
- `4. delete:xx`： 删除了某些无用的部分

推送到远程

```python
git push -u origin master	# 推送到远端 master 
```

> 注意：合并分支必须先切换分支，删除分支不是必须的！但是流程最好是：**先创建分支 ---> 合并分支 ---> 提交 ---> 删除分支 ----> 创建分支（第二天）**

**打标签**

```python
git tag v0.1	# 打标签 v0.1
git tag		# 查看所有标签
git show v0.1	# 查看 v0.1 标签（内容）

# 1. 同步单个标签
$ git push origin v0.1

# 2. 同步所有标签
$ git push --tags
# 或者：
$ git push origin --tags
```

## 版本回退

- `show history`: newVersion:2965a8e4d76fbf43395a89b438bde069a3afffab
  oldVersion:d6911d54c4526c71948e7899fc8dad92f1d4bea6
- 先在 `history` 中复制需要回退版本的版本号和当前版本号
- Git->Repository->Reset HEAD 中输入 oldVersion,`Reset Type:*Hard`.
- 此时 push 到 github 会提示冲突
- Git->Repository->Reset HEAD 中输入 newVersion,`Reset Type:*Mixed`.
- `commit` 之后再 `push`,就不再有冲突了。

## git 之忽略文件.gitignore

- 在 git bash 中 `vim .gitignore` 或者 `touch .gitignore`
- `i` 进入到编辑模式
- `esc :eq` 保存并退出，`:q` 退出(未修改)，`:!` 强制退出(修改后不保存退出)

## git 结合 idea

- 先在设置中 git，github 中设置好相关内容.
- 本地文件改动后 `commit`。
- 先 `pull`，再 `push`，github 就与本地仓库同步了。

## 常见问题

### Permission denied (publickey)

- ssh-keygen -t rsa -C 973725819@qq.com`
- 生成的 id_rsa.pub 在`C:\Users\wcs\.ssh`
- 复制 id_rsa.pub 到 github SSH key 那里