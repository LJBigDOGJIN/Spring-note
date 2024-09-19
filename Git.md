#### git工作流程

##### 四个分区

![image-20240919143357595](D:\TXT\图片文件\image-20240919143357595.png)

- workspace：工作区，写代码的地方
- index/stage：暂存区（也叫索引区），临时存放改动文件的地方
- repository：仓库区，安全存放数据的区域，里面有所有提交数据的版本，head指向最新一次提交的记录
- remote：远程仓库，托管代码的服务器

> **文件状态**
>
> 新建一个文件时：Untracked
>
> 添加到暂存区时：Staged
>
> 使用commit命令将文件提交到暂存区时：UnModified
>
> 对Unmodified文件修改：Modified
>
> 使用remove移除Modified：Untracked

git pull 拉取远程仓库的最新代码，会自动merge到本地分支

git fetch 拉取远程仓库最新代码，不会自动merge

在本地分支上修改代码，添加到暂存区，git add 文件名

移除暂存区：git reset 文件名 

撤销commit：

- git reset head^文件名     //回退这个文件到上一个版本
- git reset head ~ 3   //回退三个版本

> git reset 还有三个参数
>
> 1. git reset --mixed： git 默认的方式，将撤回的代码存放到工作区。同时会保留本地未提交的内容。
> 2. git reset --soft：将撤回的代码放到暂存区，同时会保留本地未提交的内容
> 3. git reset --hard: 彻底回退到某个版本，丢弃将撤回的代码，本地没有commit的代码也会被全部擦除掉

添加到本地仓库 git commit -m "这里填一些简要信息，比如说这次提交改动了什么"

![image-20240919150139952](D:\TXT\图片文件\image-20240919150139952.png)

提交到主分支 git push 远程仓库名 分支名

提交分支后合并到主分支 

1. 切换分支  **git checkout 分支名**
2. 合并到主分支 **git merge master** 
3. 合并完成后删除分支 **git branch -d** 分支名 
4. **git push** 远程仓库名 分支名字

删除文件

**git rm** 文件名  //删除的只是工作目录的文件，版本库里还是存在该文件，删除文件会将这个文件存到暂存区

删除的文件如何恢复？

一种说法是 用   **git checkout 文件名**   的形式，当checkout后面跟的是分支名时，会切换到该分支，如果后面跟的是文件名时，则会在.git目录中复制一份该文件存到工作目录中。