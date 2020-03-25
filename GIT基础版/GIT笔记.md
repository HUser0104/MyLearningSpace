

### 课时1:git原理及配置  版本控制软件

​	.1)分布式版本控制软件

![1585019581805](C:\Users\Admin\AppData\Roaming\Typora\typora-user-images\1585019581805.png)

​	![1585019666633](C:\Users\Admin\AppData\Roaming\Typora\typora-user-images\1585019666633.png)

.2)一些命令

​	1. git add:将本地文件   增加到暂时存放区	

​	2. git commit:将暂存区的内容提交到本地仓库

​	3. git push:将本地仓库的内容  推送到 远程分支(也就是别人的分支)

​	4. git pull：将远程仓库(也就是别人的分支)的东西拉取到本地分支

​	5. 默认的主分支为:master

.3) 安装git客户端:msysgit.github.io 这个网址下载 安装很简单  下一步下一步

​	1.Use git from git bash only..,其他默认下一-步.

​	2. 配置环境变量

​	3. 配置用户名和邮箱来标识唯一仓库   别人可以找到我们

​		3.1 配置用户名

```git
$ git config --global user.name "HUser0104"
```

​		3.2 配置邮箱

```git
$ git config --global user.email "huserjava@yeah.net"
```

​		3.3 查看是否配置好

![1585020865092](C:\Users\Admin\AppData\Roaming\Typora\typora-user-images\1585020865092.png)

​	4.搭建git服务器(远程):统一的托管网站:https://github.com/

​	5.为了在本地和远程仓库之间进行免密钥登录，可以配置ssh

​		5.1 配置ssh

​			5.1.1 第一步 在本地配置ssh 发送给远程

```git
ssh-keygen -t rsa -C 加邮箱地址
然后回车三次
```

​			5.1.2 第二步 发送远程

​				5.1.2.1 

![1585021788884](C:\Users\Admin\AppData\Roaming\Typora\typora-user-images\1585021788884.png)

​			

![1585021827391](C:\Users\Admin\AppData\Roaming\Typora\typora-user-images\1585021827391.png)

​				5.1.2.2 将本地的复制

![1585022131781](C:\Users\Admin\AppData\Roaming\Typora\typora-user-images\1585022131781.png)

​			5.1.2.3 粘贴到

![1585022189479](C:\Users\Admin\AppData\Roaming\Typora\typora-user-images\1585022189479.png)

​	6.测试连通性

```git
ssh -T git@github.com

Warning: Permanently added the RSA host key for IP address '13.229.188.59' to the list of known hosts.
Hi HUser0104! You've successfully authenticated, but GitHub does not provide shell access.
出现上面这个玩意  证明已经建立与远程通信

```

.4)测试示例

​	1.在本地新建git项目,并且发送给远程

​		1.1 本地建立项目

​			1.1.1 在项目的根目录 ----右击选择git bash--输入命令git init初始化

![1585022864017](C:\Users\Admin\AppData\Roaming\Typora\typora-user-images\1585022864017.png) 

​		2.2 在远程创建项目

​			2.2.1 new---新建项目---生成---https://github.com/HUser0104/testGitRemote.git

​	2.本地项目-----远程项目关联

```git
git remote add origin https://github.com/HUser0104/testGitRemote.git
```

### 课时2:git常见操作

.1)git常见操作

​	1.第一次发布项目(本地项目---》远程)

​		1.1 git add .     // 文件-暂存区

​		1.2 git commit -m "注释内容"    //提交到本地版本库--本地分支(默认master)

​		1.3 git push -u origin  master

​	2.第一次下载项目 (远程---》本地项目)

```git
git  clone https://github.com/HUser0104/testGitRemote.git
```

​	3.更新项目(远程---》本地项目) 第N次更新

```git
git pull
```

​	4.提交项目(本地项目---》远程) 第N次提交

​		1.1 git add .     // 文件-暂存区

​		1.2 git commit -m "注释内容"    //提交到本地版本库--本地分支(默认master)

​		1.3 git push  origin  master

### 课时3:使用github团队协作

.1)使用github团队协作

​	1.git多个人   团队协作完成项目   在项目的那里点击setting

![1585040457158](C:\Users\Admin\AppData\Roaming\Typora\typora-user-images\1585040457158.png)

 2. 输入邀请人的邮箱或者用户名

    ![1585040535906](C:\Users\Admin\AppData\Roaming\Typora\typora-user-images\1585040535906.png)

    3.邀请完成之后通知被邀请人 复制这个地址发送给邀请人

    ![1585040615169](C:\Users\Admin\AppData\Roaming\Typora\typora-user-images\1585040615169.png)

    4.邀请人收到后  登录自己的github   在地址栏粘贴邀请人发来的地址 点击接受即可加入一起开发

    ![1585040833600](C:\Users\Admin\AppData\Roaming\Typora\typora-user-images\1585040833600.png)

    5.被邀请人clone项目进来即可  随时可以提交或者更新操作

    ![1585040928741](C:\Users\Admin\AppData\Roaming\Typora\typora-user-images\1585040928741.png)