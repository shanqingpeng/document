### 一、Git简介

#### 1、什么是版本控制

#### 2、为什么要用版本控制

#### 3、有哪些版本控制工具

#### 4、Git发展历史

#### 5、Git工作机制

#### 6、Git和代码托管中心



### 二、Git安装

#### 1、官网地址

#### 2、注意事项



### 三、Git常用命令

#### 1、设置用户签名

```ruby
# 设置用户
$ git config --global user.name shanqingpeng

# 设置用户邮箱
$ git config --global user.email shanqingpeng0108@163.com

# 查看设置的签名 (系统家目录下的 .gitconfig 文件)
$ cat ~/.gitconfig
[user]
        name = shanqingpeng
        email = shanqingpeng0108@163.com
[credential]
        helper = manager
```



#### 2、初始化本地库

```ruby
# 初始化本地库后, 会在本地库目录下生成 .git 目录 (默认是隐藏的)
$ git init
Initialized empty Git repository in D:/docs/Markdown/my-project-01/.git/
```



#### 3、查看本地库状态

```ruby
# (1) 使用 git status 查看本地库状态
$ git status
On branch master   # 当前本地库是在master分支下

No commits yet     # 当前没有任何提交

nothing to commit (create/copy files and use "git add" to track)  # 没有可以提交的文件

# (2) 在本地库中添加一个文件hello.txt

# (3) 再次查看本地库状态
$ git status
On branch master

No commits yet		# 当前没有任何提交

Untracked files:	# 存在没有被追踪的文件 hello.txt (也就是：存在没有添加到版本控制的文件)
  (use "git add <file>..." to include in what will be committed)
        hello.txt

# 没有文件可以提交, 但是存在没有被追踪的文件 (使用git add进行追踪)
nothing added to commit but untracked files present (use "git add" to track)
```



#### 4、添加到暂存区

```ruby
# (1) 使用 git add 将文件添加到暂存区
$ git add .
warning: LF will be replaced by CRLF in hello.txt.
The file will have its original line endings in your working directory

# (2) 再次查看本地库状态
$ git status
On branch master

No commits yet

# hello.txt文件可以被提交了, 如果不想提交, 可以使用 git rm --cached 从暂存区中删除
Changes to be committed:
  (use "git rm --cached <file>..." to unstage)
        new file:   hello.txt

# (3) 从暂存区中删除文件
$ git rm --cached hello.txt
rm 'hello.txt'

# (4) 再次添加到暂存区
$ git add .
warning: LF will be replaced by CRLF in hello.txt.
The file will have its original line endings in your working directory
```



#### 5、提交到本地库

```ruby
# (1) 使用 git commit -m "日志信息" 提交到本地库
$ git commit -m "first commit"
[master (root-commit) 7484fdd] first commit
 1 file changed, 1 insertion(+)
 create mode 100644 hello.txt
# 100644 是本次提交的版本号

# (2) 查看本地库状态
$ git status
On branch master
nothing to commit, working tree clean
```



#### 6、修改文件

```ruby
# (1) 修改文件 hello.txt
$ vim hello.txt

# (2) 查看本地库状态
$ git status
On branch master
# 未进行提交的修改 (可以使用 git add 添加到暂存区, 然后就可以提交了)
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   hello.txt

no changes added to commit (use "git add" and/or "git commit -a")

# (3) 添加到暂存区
$ git add .
warning: LF will be replaced by CRLF in hello.txt.
The file will have its original line endings in your working directory

# (4) 再次提交到本地库
$ git commit -m "update file"
[master 3314454] update file
 1 file changed, 6 insertions(+)
```



#### 7、历史版本

```ruby
# (1) 查看版本日志
$ git reflog
3314454 (HEAD -> master) HEAD@{0}: commit: update file
7484fdd HEAD@{1}: commit (initial): first commit

# (2) 查看版本详细日志
$ git log
commit 33144548411ac7a343b0457af59db1033955e16f (HEAD -> master)
Author: shanqingpeng <shanqingpeng0108@163.com>
Date:   Fri May 13 16:28:38 2022 +0800

    update file

commit 7484fdd39b1c8f9f882663ba47bbe6d6ccd148fc
Author: shanqingpeng <shanqingpeng0108@163.com>
Date:   Fri May 13 16:18:30 2022 +0800

# (3) 使用 git reset 进行版本穿梭
git reset --hard 7484fdd
```



#### 8、设置远程库别名

```ruby
# (1) 添加远程库别名为 document (若已存在，则添加失败)
$ git remote add document http://39.105.104.109:9090/shanqingpeng/document.git

# (2) 查看当前所有的远程库别名
$ git remote -v
document        http://39.105.104.109:9090/shanqingpeng/document.git (fetch)
document        http://39.105.104.109:9090/shanqingpeng/document.git (push)

# (3) 删除远程库别名
$ git remote rm document

# (4) 修改远程库别名的URL
$ git remote set-url document http://39.105.104.109:9090/shanqingpeng/document.git
```



### 四、Git分支操作

#### 1、什么是分支

#### 2、分支的好处

#### 3、分支有哪些操作

##### 3.1 查看分支

```ruby
# (1) 使用 git branch -v 查看当前有哪些分支
$ git branch -v
* master 3314454 update file
```



##### 3.2 创建分支

````ruby
# (1) 使用 git branch 创建分支
$ git branch hot-fix

# (2) 查看分支
$ git branch -v
  hot-fix 3314454 update file
* master  3314454 update file
````



##### 3.3 切换分支

```ruby
# (1) 使用 git checkout 切换分支
$ git checkout hot-fix
Switched to branch 'hot-fix'

# (2) 查看分支
$ git branch -v
* hot-fix 3314454 update file
  master  3314454 update file
```



##### 3.4 修改分支

````ruby
# (1) 在 hotf-ix 上修改文件
$ vim hello.txt

# (2) 查看本地库状态
$ git status
On branch hot-fix
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   hello.txt

no changes added to commit (use "git add" and/or "git commit -a")

# (3) 添加到暂存区
$ git add .
warning: LF will be replaced by CRLF in hello.txt.
The file will have its original line endings in your working directory

# (4) 提交到本地库
$ git commit -m "hot-fix first commit"
[hot-fix 7732609] hot-fix first commit
 1 file changed, 2 insertions(+)

# (5) 查看文件内容
$ cat hello.txt
hello git
hello git
hello git
hello git
hello git
hello git
hello git

hot-fix
````



##### 3.5 合并分支

```ruby
# (1) 切换到 master 分支
$ git checkout master
Switched to branch 'master'

# (2) 把 hot-fix 分支的修改合并到 master 分支
$ git merge hot-fix
Updating 3314454..7732609
Fast-forward
 hello.txt | 2 ++
 1 file changed, 2 insertions(+)

# (3) 查看 hello.txt 内容
$ cat hello.txt
hello git
hello git
hello git
hello git
hello git
hello git
hello git

hot-fix
```



##### 3.5 解决冲突

```ruby
# (1) 切换到 hot-fix 分支
$ git checkout hot-fix
Switched to branch 'hot-fix'

# (2) 修改 hello.txt 文件
$ vim hello.txt

# (3) 添加到暂存区
$ git add .

# (4) 提交到本地库
$ git commit -m "hot-fix second commit"
[hot-fix 55b1287] hot-fix second commit
 1 file changed, 1 insertion(+), 1 deletion(-)

# (5) 切换到 master 分支
$ git checkout master
Switched to branch 'master'

# (6) 修改 hello.txt 文件
$ vim hello.txt

# (7) 添加到暂存区
$ git add .

# (8) 提交到本地库
$ git commit -m "master commit"
[master 7c67770] master commit
 1 file changed, 1 insertion(+), 1 deletion(-)

# (9) 把 hot-fix 分支的修改合并到 master 分支 (出现冲突)
$ git merge hot-fix
Auto-merging hello.txt
CONFLICT (content): Merge conflict in hello.txt
Automatic merge failed; fix conflicts and then commit the result.

# (10) 打开冲突文件 hello.txt, 修改文件, 解决冲突
hello git
hello git
hello git
hello git
hello git
hello git
<<<<<<< HEAD
hello git  master commit   	# 此处为当前分支的代码 (即master分支)
=======
hello git   hot-fix commit  # 此处为 hot-fix 分支的代码
>>>>>>> hot-fix

hot-fix
    
# (11) 添加到暂存区
$ git add .

# (12) 提交到本地库 (注意: 此时提交不能带文件名, 否则将报错)
$ git commit -m "resolve confict"
[master ce561cb] resolve confict
    
# (13) 查看解决冲突, 提交后的文件
$ cat hello.txt
hello git
hello git
hello git
hello git
hello git
hello git
hello git   master commit
hello git   hot-fix commit

hot-fix
```





