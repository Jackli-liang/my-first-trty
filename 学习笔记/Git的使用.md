# Git的版本控制工具

### 1 仓库的创建流程

##### 1.向仓库添加文件的流程

​	(1)工作区,暂存区,Git仓库

​	(2)在工作区编辑完,git status 查看状态. git add + filename 提交到暂存区,git commit -m + '提交描述'

​	每次更改完之后提交,然后查看状态

​	查看设置git config --list

##### 2.基本信息设置

​	(1) git config --global user.name "Jackli-liang"

​	(2) git config --gobal  user.email "1486842581@qq.com"

​	(3) git config --list 

##### 3.初始化一个新的仓库

​	(1)创意一个文件夹 mkdir test

​	(2)进入cd test +   git init  此时会生成一个隐藏文件.git  查看隐藏文件 ls -a

​	(3)将文件添加到暂存区 git add .  (添加所有文件)

​	(3)将暂存区文件添加到仓库 git commit -m '提交描述'

##### 4.修改仓库文件

(1)git status

(2)vi + filename进入编辑文件状态,按esc退出编辑状态之后:wq

##### 5 删除仓库文件

(1)git rm 'filename'

### 2 Git管理远程仓库

2.1 使用远程仓库的目的

​	作用:备份数据,供多人同时开发使用,代码集中共享化管理

2.2 Git克隆操作

​	将远程仓库的代码复制到本地:git clone +github代码地址

2.3 将本地仓库同步到远程仓库

​	git push

2.4 版本回退

​	git log

​	git reset --hard HEAD^

​	git diff  --read.txt查看工作区与版本库的区别

​	git checkout --file

2.5 取消本地与远程仓库的连接

​	git remote remove origin

​	本地连接远程仓库    git remote add origin https://github.com/Jackli-liang/poster.git

​	推送本地到远程仓库   git push -u origin master

​	内容合并 git pull --rebase origin master

2.6 总结

#从GitHub上克隆项目到本地 git clone git@github.com:han1202012/NDKHelloworld.git

 #注意克隆的时候直接在仓库根目录即可, 不用再创建项目根目录 ;

 #添加文件 git add ./* # 将目录中所有文件添加; #提交缓存 git commit -m '提交'; 

#提交到远程GitHub仓库 git push -u origin master ;

---------新的仓库的提交步骤

```
git init
git add README.md
git commit -m "first commit"
git remote add origin git@github.com:Jackli-liang/mysite.git
git push -u origin master

克隆的两种方式
1 ssh
git clone git@github.com:Jackli-liang/mysite.git
2 https
git clone https://github.com/Jackli-liang/mysite.git

```