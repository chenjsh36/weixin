###资源
[阿里云mongodb安装的启动](http://www.tuicool.com/articles/rIj6byM)
[centos mongodb](http://www.tuicool.com/articles/JbAVvu)
[官方:centos or redhat 安装和使用mongodb](https://docs.mongodb.org/manual/tutorial/install-mongodb-on-red-hat/)

###环境
系统：centos 7 64位
mongodb： 3.2版本
方式： yum

###过程
推荐参考资源的官方文档，当然如果你是centos6的，也可以看看其他文章，笔者用的是7，mongodb想用3以上的版本，加上阿里云推荐使用yum进行下包（速度支持快很多），于是便看着官方文档上来

####修改yum的包管理系统

>vim /etc/yum.repos.d/mongodb-org-3.2.repo

####填写配置信息：
>[mongodb-org-3.2]name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.2/x86_64/gpg
check=0
enabled=1

####安装mongodb
>yum install -y mongodb-org

如果想安装其他版本的可以看官方文档里的详细说明

####使用mongodb
启动
>service mongod start

停止
>service mongod stop

重启
>service mongod restart

客户端
> mongo

至此，配置就完成了，就可以在你的项目里面像本地一样使用数据库服务了。