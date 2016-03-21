# Git

<a name="table-of-contents)"></a>
## 目录
1. [Git-Book教程摘录](#course)
2. [常用命令](#order)
3. [.ignore文件](#ignore)
4. [项目规范化](#project)
5. [相关资源](#source)

<a name="course"></a>
## Git-Book教程摘录
### 概念记录
- git三种状态：已提交（committed），已修改（modified）和已暂存（staged）
- 三个工作区域：Git仓库，工作目录，及暂存区域
- 基本工作流：
	1. 在工作目录中修改文件。
	2. 暂存文件，将文件的快照放入暂存区域。
	3. 提交更新，找到暂存区域的文件，将快照永久性存储到 Git 仓库目录。
- Git 仓库中有五个对象：三个 blob 对象（保存着文件快照）、一个树对象（记录着目录结构和 blob 对象索引）以及一个提交对象（包含着指向前述树对象的指针和所有提交信息）。Git 的分支，其实本质上仅仅是指向提交对象的可变指针。
- HEAD指针指向当前所在的本地分支
- “master” 是当你运行 `git init` 时默认的起始分支名字
- “origin” 是当你运行 `git clone` 时默认的远程仓库名字。

#### 跟踪分支
- 当抓取到新的远程跟踪分支时，本地不会自动生成一份可编辑的副本（拷贝）。可以运行 `git merge origin/serverfix` 将这些工作合并到当前所在的分支。如果想要在自己的 serverfix 分支上工作，可以将其建立在远程跟踪分支之上：

	> `git checkout -b serverfix origin/serverfix`  
	> 快捷方式：`git checkout --track origin/serverfix`
- 修改正在跟踪的上游分支(-u)：`git branch -u origin/serverfix`
- 删除一个远程分支:`git push origin --delete serverfix`

#### git协议
- Git 可以使用四种主要的协议来传输资料：本地协议（Local），HTTP 协议，SSH（Secure Shell）协议及 Git 协议

### 配置
#### 配置用户信息
`git config --global user.name "John Doe" ` 
`git config --global user.email johndoe@example.com ` 
#### Git 别名
> git config --global alias.unstage 'reset HEAD --'

- 检查配置信息：`git config --list`，`git config <key>`
- 配置文本编辑器：`git config --global core.editor emacs`

<a name="order"></a>
## 常用命令
- 检查当前文件状态：`git status`
	- 状态简览：`git status -s`
- 查看尚未暂存的文件更新了哪些部分：`git diff`
	- `--staged`或`--cached`：查看已暂存的将要添加到下次提交里的内容
- 添加内容到下一次提交中：`git add`
- 提交更新：`git commit`
	- `-v` 将你所做的改变的 diff 输出放到编辑器中
	- `-m` 直接在命令行写提交信息
	- `-a` 跳过使用暂存区域(git add)，自动把所有已经跟踪过的文件暂存起来一并提交
- 移除文件：`git rm <file>`
	- `-f` 强制删除，用于删除已经修改过放进了暂存区域的文件
	- `--cached`：文件从git仓库和暂存区删除，工作目录保留
- 文件更名 `git mv fileFrom fileTo`
- 查看提交历史：`git log`
	- `-p` 显示每次提交的内容差异（可用于代码审查）a
	- `--stat` 每次提交的简略统计信息
	- `--oneline` 精简信息以一行显示
	- `--all` 所有分支的提交记录
	- `--pretty` 指定使用不同于默认格式的方式展示提交历史  
	
		> 例如: `git log --pretty=oneline`  
		> 另外还有`short`、`full`和`fuller`可用  
		> 格式化(占位符有很多种)：`git log --pretty=formatt:"%h %s"`
	- `--graph`：用一些ASCII字符串来形象地展示你的分支、合并历史
	- `--decorate`：查看各个分支当前所指的对象
	- 筛选
		- `-<n>`：仅显示最近n提交
		- `-S`：列出那些添加或移除了某些字符串的提交
		- `--since`,`--until`：按照时间作限制的选项
	
			> `git log --since=2.weeks`
		- `--grep`：仅显示含指定关键字的提交
		- `--author`：仅显示指定作者相关的提交
		- `--committer`：仅显示指定提交者相关的提交

- `git reflog` 列出了head曾经指向过的一系列commit，检查丢失的提交

### 撤销操作
- 重新提交：`git commit --amend`

	> git commit -m 'initial commit'  
	> git add forgotten_file  
	> git commit --amend  
	> 最终你只会有一个提交,第二次提交将代替第一次提交的结果。
- 取消暂存的文件：`git reset HEAD [file]`
- 撤销对文件的修改：`git checkout -- [file]`

### 远程仓库的使用
- 查看远程仓库：`git remote`
	- `-v`：显示需要读写远程仓库使用的 Git 保存的简写与其对应的 URL
- 查看某一个远程仓库：`git remote show [remote-name]`
- 添加远程仓库：`git remote add [<options>] <name> <url>`

	> git remote add origin https://github.com/vtangdg/summary.git
- 从远程仓库中抓取：

	> `git fetch [remote-name]`：不会自动合并或修改你当前的工作  
	> `git pull [remote-name]` ：自动尝试合并到当前所在的分支。
- 推送到远程仓库：`git push [remote-name] [branch-name]`
- 远程仓库更名：`git remote rename [old-name] [new-name]`
- 移除远程仓库：`git remote rm [remote-name]`

### 打标签
- 列出标签：`git tag`
- 创建标签：
	- `git tag [tag-name]`(轻量标签)
	- `git tag -a [tag-name]`(附注标签)
		- `-m`：指定了一条将会存储在标签中的信息
- 显示相应标签信息：`git show [tag-name]`
- 后期打标签:`git tag -a v1.2 9fceb02`
- 共享标签：`git push origin [tag-name]`
	- `--tags`：推送所有不在远程仓库服务器上的标签
- 检出标签：`git checkout -b [branch-name] [tag-name]`(在特定的标签上创建一个分支)

### 分支
- 创建：`git branch [branchName]`
	- `-d`：删除分支 
	- `-D`:强制删除还有未合并的工作的分支
	- `vv`:查看设置的所有跟踪分支
- 查看：`git branch`
	- `-v`：查看每一个分支的最后一次提交
	- `--merged`：查看哪些分支已经合并到当前分支
	- `--no-merged`：查看哪些分支尚未合并到当前分支
- 切换：`git checkout [branch-name]`
	- `-b`：同时切换到新分支
	
- 合并：`git merge [branch-name]`
	- 遇到冲突时的合并

		> `git status` 命令来查看那些因包含合并冲突而处于未合并（unmerged）状态的文件  
		> 解决冲突      
		> 对每个文件使用 `git add` 命令来将其标记为冲突已解决
		
### 黑科技
- `git blame [file_name]` 将文件中的每一行的作者、最新的变更提交和提交时间展示出来
- `git add -p [file_name]` 暂存文件的部分改动
	> `y n e d s` yes,no,e 手工编辑该块，d 退出或者转到下一个文件，s分割该块
	
- `git rebase -i HEAD~[number]` 压缩多个commit
	> `pick` git会应用这个补丁，以同样的提交信息（commit message）保存提交。  
	> `squash` 把这个提交和前一个提交合并成为一个新的提交  
	> `edit` 编辑提交  
	> 如果把一行删除而不是指定'pick'、'squash'和‘edit''中的任何一个，git会从历史中移除该提交  

- `git stash` 存储变更回到之前状态 `git stash apply` 解除stash并且恢复未提交的变更 `git stash list` 展示 `git stash apply stash@{标识符}`选择性恢复

#### 检查丢失的提交
`git fsck --lost-found` 检查丢失的提交,可以通过运行 `git show [commit_hash]` 查看提交之后的改变或者运行`git merge [commit_hash]` 来恢复到之前的提交。 

`git cherry-pick [commit_hash]`
简而言之，cherry-pick就是从不同的分支中捡出一个单独的commit，并把它和你当前的分支合并。如果你以并行方式在处理两个或以上分支，你可能会发现一个在全部分支中都有的bug。如果你在一个分支中解决了它，你可以使用cherry-pick命令把它commit到其它分支上去，而不会弄乱其他的文件或commit。

<a name="ignore"></a>
## 忽略文件.gitignore
### 格式规范
- 所有空行或者以 ＃ 开头的行都会被 Git 忽略。
- 可以使用标准的 glob 模式匹配。
- 匹配模式可以以（/）开头防止递归
- 匹配模式可以以（/）结尾指定目录。
- 要忽略指定模式以外的文件或目录，可以在模式前加上惊叹号（!）取反。

所谓的 glob 模式是指 shell 所使用的简化了的正则表达式。 星号（*）匹配零个或多个任意字符；[abc] 匹配任何一个列在方括号中的字符（这个例子要么匹配一个 a，要么匹配一个 b，要么匹配一个 c）；问号（?）只匹配一个任意字符；如果在方括号中使用短划线分隔两个字符，表示所有在这两个字符范围内的都可以匹配（比如 [0-9] 表示匹配所有 0 到 9 的数字）。 使用两个星号（*) 表示匹配任意中间目录，比如a/**/z 可以匹配 a/z, a/b/z 或 a/b/c/z等。

<a name="project"></a>
## 项目规范化
### ReadMe的内容应包括但不限于：
- 目录
- 项目介绍
- 安装方法
- demo
- 发布路线图

<a name="source"></a>
## 相关资源
- [Git-Book](https://git-scm.com/book/zh/v2)  官方教程

[回到目录](#table-of-contents)




