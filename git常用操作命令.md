####git常用操作命令
	创建新仓库：git init
	提交代码过程：
		git add .
		git commit -m "代码提交信息"
		git pull origin master
		git commit -m "代码提交信息"
		git push origin master
	
	----
	1）远程仓库相关命令
	clone：git clone [版本库网址]［本地目录名］
	添加远程仓库:git remote add [name][url]
	查看远程仓库:git remote -v
	删除远程仓库:git remote rm [name]
	修改远程仓库:git remote set-url --push[name][newUrl]
	pull:git pull [remoteName][localBranchName]
	push:git push [remoteName][localBranchName]
	
	2)分支的操作命令
	查看分支：git branch
	查看远程分支:git branch -r
	创建本地分支:git branch [name] ---注意新分支创建后不会自动切换为当前分支
	切换分支：git checkout[name]
	创建新分支并立即切换到新分支:git checkout -b [name]
	删除分支:git branch -d [name] -- -d选项只能删除已经参与了合并的分支，对于未有合并的分支是无法删除的。如果想强制删除一个分支，可以使用-D
	合并分支:git merge [name] ---将名字为[name]的分支与当前分支合并
	
	3)标签
	创建1.0.0标签:git tag 1.0.0 1b2e1d63ff (其中1b2e1d63ff是你想要标记的提交ID的前10位字符，可使用下列命令获取提交ID:git log)
	
	4）替换本地改动
	替换掉本地改动:git checkout -- [fileName]
	丢弃本地所有改动和提交:git fetch origin 
						git reset --hard origin/master
	
	5)实用小贴士
	内建的图形化git:gitk
	彩色的git输出:git config color.ui true
	显示历史记录时，每个提交的信息只显示一行:git config format.pretty oneline
	交互式添加文件到暂存区:git add -i
	
	***
	terminal创建公钥:ssh-keygen -t rsa -C " 邮箱地址"
	查看公钥:cat ~/.ssh/id_rsa.pub