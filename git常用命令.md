git常用命令

* 用户信息配置
  - `git config --global user.name "xxx" `：配置全局用户名
  - `git config --global user.email "xxx@xx.com"  `：配置全局邮箱
  - `git config --global credential.helper store`: 配置密码
  - `git config --list`: 查看配置
  
* 仓库初始化
  - `git init` : 创建 `.git`, 适合在已存在项目追加版本控制
  - `git init projectname` : 创建 `projectname/.git`, 适合项目开始时加入版本控制

* 提交

  * `git status` : 查看文件变动的状态

  * `git add <file-name>` : 提交某个文件的修改到暂存区
  * `git add .` : 提交所有修改到暂存区
  * `git commit -m "message"` : 将暂存区的文件到版本库
  * `git commit -am "message"`  `git commit -a -m "message"` : 直接将工作区的所有修改提交到版本库 

* 查看文件修改
  - `git diff`: 查看修改[工作树和暂存文件]
  - `git diff --staged`: 查看修改[暂存文件和最近提交文件]
  - `git diff HEAD`: 查看修改[工作树和最近提交文件]
  - `git diff --<file-name>` : 查看某个文件工作树和暂存区之间的区别
  - `git diff --stat`: 查看修改的文件名
  - `git diff <commit-id> <commit-id>` : 查看两个提交之间的区别

* 查看提交log
  - `git log`: 显示提交信息
  - `git log --oneline`: 显示提交 Message
  - `git log --stat`: 显示提交文件名级详细修改信息
  - `git log --patch`: 显示提交文件内容级详细修改信息
  - `git log --graph`: 用图显示提交记录
  - `git log --graph --all --decorate --oneline`: 去除冗余信息，更加直观显示每条分支每次提交
  - `git log --stat -- filename`: 文件提交记录（不记录路径移动）
  - `git log --stat -M --follow -- filename`: 看到完整的文件操作过程

* 分支

  * `git branch ` : 列出本地所有分支
  * `git branch -r` : 列出远程所有分支
  * `git branch -a` : 列出所有本地和远程分支 
  * `git branch -v` : 查看分支的详细信息

  - `git branch <branch-name>` : 创建一个分支,但依然停留在当前分支
  - `git branch -d <branch-name>` : 删除本地分支（已经合并过的）
  - `git branch -D branchname`: 删除本地分支（不管是否合并过）
  - `git push origin --delete branch` : 删除远程分支
  - `git checkout <branch-name>` : 切换到指定分支,并更新工作区
  - `git checkout commitID`: 工作树切换到 commitID 时
  - `git checkout -- filename`: 清理掉最后一次提交内容
  - `git checkout -b <new-branch> <remote-branch>`: 创建新分支并且进入该分支
  - `git merge <branch-name>`: 合并 branch-name 分支到目前所在分支(合并时文件冲突要手动解决)
  - `git cherry-pick <commit-id>` : 挑选一个commit合并到当前分支
  - `git cherry-pick <branch-name>` : 挑选指定分支的最新提交
  - `git rebase <branch-name>`: 将当前分支历史提交合并到 branchname 分支

* 撤销

  	* `git checkout <file>` : 恢复暂存区的指定文件到工作区
  	
  	* `git checkout .` : 恢复暂存区当前目录的所有文件到工作区
  	
  	* `git checkout <commit>` :恢复工作区到指定的commit
  	
  	* `git reset <commit> ` 的意思就是 把HEAD移到<commit> 就是把你现在这个branch的最新的commit移到你指定的commit
  	
  	* `git reset <file>` :重置暂存区指定文件,与上一次commit保持一致,但工作区不变
  	
  	* `git reset --hard` :重置暂存区与工作区,与上一次commit保持一致
  	
  	* `git reset --hard <commit>` :重置当前分支HEAD为指定commit,同时重置 暂存区合工作区与指定commit一致
  	
  	* `--soft` :只动repo, `--mixed` : 动repo和staging(默认参数) `--hard` : 动repo,staging,working三个地方
  	* `git reset HARD` :HARD代表当前commit
  	* `git reset HARD^` :代表上一次commit
  	
  	* `git revert <commit>` : 新建一个commit,用于撤销指定commit
  	
  	* `git stash` :将未提交的变化放在储藏区
  	
  	* `git stash pop` :将储藏区的内容恢复到当前的工作区
* 远程仓库

  - `git remote add origin https://xxx.com/user/repo.git` :本地和远程关联
  - `git clone https://xxx.com/user/repo.git ` : 克隆远程仓库
  - `git remote set-url origin newUrl`: 改变 URL
  - `git remote rm origin`: 删除
  - `git remote -v`: 查看远程仓库信息
  - `git fetch origin`: 抓取远程分支，本地会有一个 `remotehostname/branchname`的分支，一般用于查看伙伴代码
  - `git pull origin`: 和 fetch 类似，但是是取回远程更新和本地合并。相当于先 fetch 再 merge。
  - `git push origin`: push 到远程仓库
  -  `git remote set-url origin newUrl`: 改变 URL
  - git remote rm origin`: 删除`
  - `git remote -v`: 查看远程仓库信息
  - git fetch origin`: 抓取远程分支，本地会有一个 `remotehostname/branchname`的分支，一般用于查看伙伴代码`
  - `git pull origin`: 和 fetch 类似，但是是取回远程更新和本地合并。相当于先 fetch 再 merge。
  - git push origin`: push 到远程仓库




* Git Cheat Sheet

  ![Git 指令](/Users/zhenhua.shen/Desktop/git内幕/1981571249383_.pic_hd.jpg)

  

  

* git 基本操作 [Pro Git](https://gitee.com/progit/) 

* 找回丢失的commit

  一般情况下 `git log` 查看历史记录

  ```
  $ git log
  * 98abc5a (HEAD, master) more stuff added to foo
  * b7057a9 initial commit
  ```

   现在重置第一次提交的状态

  ```
  $ git reset --hard b7057a9
  $ git log
  * b7057a9 (HEAD, master) initial commit
  ```

  这样看起来我们丢掉了我们第二次提交,本地的修改也消失了,没有办法找回来.但是reflog可以解决这个问题.简单来说,它会记录所有HEAD的历史,也就是说当你做reset,checkout等操作时候,这些操作会被记录在reflog中

  ```
  $ git reflog
  b7057a9 HEAD@{0}: reset: moving to b7057a9
  98abc5a HEAD@{1}: commit: more stuff added to foo
  b7057a9 HEAD@{2}: commit (initial): initial commit
  ```

  所以要找回第二次commit,只需如下操作

  `git reset --hard 98abc5a`

  来看一下git记录

  ```
  $ git log
  * 98abc5a (HEAD, master) more stuff added to foo
  * b7057a9 initial commit
  ```

* 记录保存问题

  我们前面说到在Git上做的所有操作都被保存到记录里，一般是从你本地Git库执行clone开始的所有操作都保存了下来，所以不用担心很久之前的一些Commit log找不到，你或许期望去为已删除的提交设置一个更长的保存周期。例如：
  `$ git config gc.pruneexpire "30 days"`
  意思是一个被删除的提交会在删除30天后，且运行 *git gc* 以后，被永久丢弃。
  你或许还想关掉 *git gc* 的自动运行：
  `$ git config gc.auto 0`
  在这种情况下提交将只在你手工运行 *git gc* 的情况下才永久删除。
  
* Git 之 revert

  * `revert`可以取消指定的提交内容.当讨论`revert`时,需要分两种情况,因为`commit`分两种:一种是常规`commit`,也就是用`git commit` 提交的`commit`,另一种是`merge commit` 使用`git merge` 合并两个分支后得到一个新的`merge commit` 

  * `merge commit` 和普通 `commit`的不同之处在于 `merge commit`包含两个` parent commit`，代表该 `merge commit` 是从哪两个` commit `合并过来的。

  * ```
    merge commit:
    $ git show bd86846
    commit bd868465569400a6b9408050643e5949e8f2b8f5
    Merge: ba25a9d 1c7036f
    这代表该 merge commit 是从 ba25a9d 和 1c7036f 两个 commit 合并过来的。
    
    常规 commit:
    $ git show 3e853bd
    commit 3e853bdcb2d8ce45be87d4f902c0ff6ad00f240a
    而常规的 commit 则没有 Merge 行
    ```

  * `revert`常规`commit`

    使用 `git revert ` 即可，git 会生成一个新的 commit，将指定的 commit 内容从当前分支上撤除。

  * `revert merge commit`

    添加 `-m` 选项以代表这次` revert` 的是一个` merge commit` 其中 `-m` 接收的参数是一个数字，数字取值为 1 和 2，也就是 Merge 行里面列出来的第一个还是第二个。即`git revert -m 1 bd86846`

  * revert之后重新上线

    现有分支

    ```
    a -> b -> c -> f -- g -> h (master)
               \      /
                d -> e   (test)
    ```

    突然发现test分支有bug, 需要 revert 掉,于是把 g 这个 `merge commit revert` 掉了，记为 G，如下：

    ```
    a -> b -> c -> f -- g -> h -> G (master)
               \      /
                d -> e   (test)
    ```

    test分支修改bug后,重新合并到master上,直觉上只需要再 merge 到 master 即可

    像下面这样

    ```
    a -> b -> c -> f -- g -> h -> G -> i (master)
               \      /               /
                d -> e -> j -> k ----    (test)
    ```

    i 是新的 merge commit。但需要注意的是，这 **不能** 得到我们期望的结果。因为 d 和 e 两个提交曾经被丢弃过，如此合并到 master 的代码，并不会重新包含 d 和 e 两个提交的内容. 相当于只有 test 上的新 commit 被合并了进来，而 test 分支之前的内容，依然是被 revert 掉了

    所以，如果想恢复整个 goudan/a-cool-feature 所做的修改，应该先把 G revert 掉：

    ```
    a -> b -> c -> f -- g -> h -> G -> G' -> i (master)
               \      /                     /
                d -> e -> j -> k ----------    (test)
    ```

    其中 G' 是对 G 的 revert 操作生成的 commit，把之前撤销合并时丢弃的代码恢复了回来，然后再 merge test的分支，把解决 bug 写的新代码合并到 master 分支。