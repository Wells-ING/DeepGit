## Git协议

Git中常用的4种协议分别为：本地协议（Local）、HTTP协议、SSH（Secure Shell）协议，以及Git协议

#### 1. 本地协议（Local）

​	本地协议中的远程版本库是client硬盘中的另一个目录。

适用的场景：

​	团队每一个成员对一个共享的文件系统（例如一个挂载的NFS）拥有访问权，或者比较少见的多人共用一台电脑的情况。

​	如果使用共享文件系统，就可以从本地版本库克隆（clone）、推送（push）以及拉取（pull）。

```shell
# 将本地的localGitTest仓库克隆到localGitTest2目录中
## 方式一
git clone /Users/wells/localGitTest localGitTest2
## 方式二
git clone file:///Users/wells/localGitTest localGitTest2
```

​		注意：

​		直接使用目录和在目录前添加file://略有不同。如果仅是指定目录，Git会尝试使用硬连接（hard link）或直接复制所需要的文件。如果指定file://，Git会触发平时用于路径传递资料的进程，那通常是效率较低的方法。

​		那为什么会有file://的存在呢？指定file://的主要目的是取得一个没有外部参考（extraneous references）或对象（object）的干净版本库副本--通常是在其他版本控制系统导入后或一些类似情况需要这么做。

```shell
# 创建本地remote
git remote add local /Users/wells/localGitTest.git
```

​		之后，就可以像在网络上一样从远端版本库推送和拉取更新了。

#### 优点

​		直接使用现有的文件权限和网络访问权限，所以简单。

​		适合于有共享文件系统的团队，可以不用考虑一些网络原因导致的问题。

#### 缺点

​		共享文件系统比较难配置，不方便多个位置访问。

​		如果把文件系统搭建在共享服务器中，那么通过NFS访问版本库要比通过SSH访问慢。

​		该协议不保护仓库避免意外的损坏。原因是所有用户都有完整的shell权限，不可避免他们对仓库进行修改和删除的不法操作。

### 2. HTTP协议

​		HTTP协议包含“智”HTTP协议和“哑”HTTP协议。

#### 1. 智能（Smart）HTTP协议

​		Smart HTTP协议类似于SSH及Git协议。运行在标准的HTTP/S端口上并且可以使用各种HTTP验证机制，也意味着使用起来比SSH协议简单的多。使用用户名/密码的基础授权，免去了设置SSH公钥。

​		Smart HTTP协议支持git://雷伊一样设置匿名服务，也可以像SS后协议一样提供传输时的授权和加密。而且，只用一个URL就能做到，省去了为不同的请求设置不同的URL。如果向需要授权的服务器推送数据，需要输入用户名和密码，从服务器上拉取数据也是同理。

​		类似于你在浏览器中输入URL，需账号和密码登录GitHub一样，只不过浏览器中有Session或Cookie机制，可以把用户名和密码记住，就不需要重复输入。操作系统也有类似的机制，记住了第一次输入，以后就不需要输入。

#### 2. 哑（Dumb）HTTP协议

​		Dumb HTTP协议里web服务器仅把裸版本库当作普通的文件对待，提供文件服务。设置比较简单，如果使用的话，只需要把一个裸版本库放在HTTP根目录，设置一个叫做post- update的挂钩就可以了。此时，许可能访问web服务器上你的版本库，就可以克隆你的版本库。

```shell
cd /var/www/htdocs/
git clone --bare /path/to/git_project gitproject.git
cd gitproject.git
mv hooks/post-update.sample hooks/post-update
chmod a+x hooks/post-update
```

​		这条命令会在你通过SSH向版本库推送之后被执行；然后别人就可以通过类似下面的命令来克隆。

```shell
git clone https://example.com/gitproject.git
```

​		注意：

​		这里使用了Apache服务器。









​		