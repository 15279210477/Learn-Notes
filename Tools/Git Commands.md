# Git Commands

## 一、基础命令

**git init**：初始化仓库

**git clone [url]**：克隆代码

**git pull**：更新代码

**git push**：推送代码

**git fetch**：从远程获取代码库

**git status**：查看当前代码状态

**git add .** ：添加所有代码至暂存区

**git commit -m 'comment'**：提交所有代码

**git rm <file>**：删除文件

```git
git rm <file>

git rm xxx.txt     # 从暂存区和工作区中删除文件，-f 可强制删除已经存在暂存区文件。

git rm –r *    # 递归删除，删除整个目录中的所有子目录和文件
```

**git log**：查看历史提交记录

**git blame <file>**：以列表形式查看指定文件的历史修改记录

----------

## 二、分支命令

**git branch**：查看当前分支

**git branch (name)**：创建分支

**git branch -d (name)**：删除分支

**git checkout (name)**：切换分支

**git checkout (name)**：切换分支

**git checkout -b (name)**：创建并复制新的分支

**git merge (name)**：合并分支

**git push -u origin (name)**：本地分支推送远程

**git branch --set-upstream-to origin**：本地更新未指定远程分支

----------

## 三、进阶命令

**git reset [--soft | --mixed | --hard] [HEAD]**：回退版本

**--mixed**：为默认，可以不用带该参数，用于重置暂存区的文件与上一次的提交(commit)保持一致，工作区文件内容保持不变。

```git
git reset [HEAD]git reset HEAD^            # 回退所有内容到上一个版本  
git reset HEAD^ hello.php  # 回退 hello.php 文件的版本到上一个版本  
git  reset  052e           # 回退到指定版本
```

**--soft**：参数用于回退到某个版本

```git
git reset --soft HEAD
```

**--hard**: 参数撤销工作区中所有未提交的修改内容，将暂存区与工作区都回到上一次版本，并删除之前的所有信息提交。

```git
git reset --hard HEAD$ git reset –hard HEAD~3  # 回退上上上一个版本  
git reset –hard bae128  # 回退到某个版本回退点之前的所有信息。 
git reset --hard origin/master    # 将本地的状态回退到和远程的一样 
```

**注意：** 谨慎使用 –hard 参数，它会删除回退点之前的所有信息。

**HEAD 说明：**

- HEAD or HEAD~0 表示当前版本

- HEAD^ or HEAD~1 上一个版本

## 其他设置

```git
git config --global user.name 'runoob'        # 设置用户名

git config --global user.email test@runoob.com    # 设置邮箱
git config --global http.sslVerify false    # SSL certificate problem 取消验证ssl证书问题
```
