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

移除暂存区：git reset 文件名   (有三种模式 mixed 、soft、hard，默认的就是mixed)

撤销commit：

- git reset head^文件名     //回退这个文件到上一个版本
- git reset head ~ 3   //回退三个版本

> git reset 有三个参数
>
> 1. git reset --mixed： git 默认的方式，将撤回的代码存放到工作区。同时会保留本地未提交的内容。将文件都变为Untracked状态
> 2. git reset --soft：将撤回的代码放到暂存区（staged），同时会保留本地未提交的内容
> 3. git reset --hard: 彻底回退到某个版本，丢弃将撤回的代码，本地没有commit的代码也会被全部擦除掉
>
> > 这里说明一些注意点：使用git reset --hard 命令要慎重，因为一旦回退到某个版本，那么该版本之后add到暂存区的文件将全部丢失(不过根据资料说好像还能找回，但是我又没试过，索性就不在这写了，反正使用 hard选项时一定要慎重)

添加到本地仓库 git commit -m "这里填一些简要信息，比如说这次提交改动了什么"

![image-20240919150139952](D:\TXT\图片文件\image-20240919150139952.png)

提交到主分支 git push 远程仓库名 分支名

提交分支后合并到主分支 

1. 切换分支  **git checkout 分支名**

2. 合并到主分支 **git merge master** 

3. 合并完成后删除分支 **git branch -d** 分支名 

4. **git push** 远程仓库名 分支名字

   > ``` powershell
   > 强制提交: git push -u origin master -f //会导致远程修改丢失
   > ```

删除文件

**git rm** 文件名  //删除的只是工作目录的文件，版本库里还是存在该文件，删除文件会将这个文件存到暂存区

删除的文件如何恢复？

一种说法是 用   **git checkout 文件名**   的形式，当checkout后面跟的是分支名时，会切换到该分支，如果后面跟的是文件名时，则会在.git目录中复制一份该文件存到工作目录中。



> [!NOTE]
>
> https://git-scm.com/book/zh/v2/Git-%E5%88%86%E6%94%AF-%E5%88%86%E6%94%AF%E7%9A%84%E6%96%B0%E5%BB%BA%E4%B8%8E%E5%90%88%E5%B9%B6

分支合并 

才疏学浅，先简单记录一点使用上的命令，理论我也没看。

先搞清楚要合并到哪个分支。想将代码合并到哪个分支，就切换到哪个分支，举个例子，例如我们有一个main分支和一个dev分支，我们要将dev上的代码合并到main：

```shell
先切换到要合并到的目标分支
git checkout main
进行合并操作
git merge dev

对合并后的结果进行提交
git commit -m "branch merge"
推送到远程仓库
git push origin main
```

##### 关联远程仓库

我们在本地使用命令初始化一个本地仓库,初始化成功后会出现一个 .git 文件(是一个隐藏文件)

```shell
git init
```

关联远程仓库

```shell
git remote add origin <远程仓库地址>
```

> 不过现在在远程创建仓库默认的主分支名字叫 main (不在使用之前的master)，但是我在我本地初始化的本地仓库默认分支为master(不确定是不是我git版本的问题)，用以上两个命令就能完成对本地仓库和远程仓库的关联，接下来就可以提交和拉取文件了。

可以给本地仓库的默认分支修改一个名字和远程仓库保持一致

```shell
修改分支名称的命令
git branch -m <旧分支名> <新分支名>
```



