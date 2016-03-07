## 阿里云下ECS下搭建node.js

### 环境
系统：选用centos 6.5版本 64位 系统

语言：NodeJs

包更新是： yum 

### 过程
#### 链接主机
首先当然是链接上主机，可以参考`阿里云下ECS下搭建node.js`这篇文章去做，它用到了xshell这个软件来做链接，相当于节省了你打代码的过程

当然你也可以直接使用ssh root@IP地址 端口号去链接你的主机，感觉都差不多

输入用户名和密码后登陆到主机

#### 更新环境
这个时候最好更新下系统的各种包和资源

> yum -y update

这是阿里云推荐的更新方式，你也可以试试其他的，不过速度方面应该会差些。
yum是一个在Redora和RedHat以及SUSE中的shell前端软件包管理器，基于RPM包管理器。这里说明下参数的意思:

* -y（当安装过程提示选择全部为"yes"）
* update: 安装所有更新软件

#### 安装nodejs
#####命令行下载吧，表示速度能上100k/s至少

> wget http://nodejs.org/dist/node-latest.tar.gz

######解压
> tar zxf node-latest.tar.gz

######预编译处理
> ./configure

######编译
> make

这里笔者遇到个小坑，笔者之前选择的是6.0版本的系统，而6.0的C++和G++过老，在编译node的时候就出错了，这个时候可以选择升级c++或者G++，也可以手动升级系统，
不过最快的做法是直接在阿里云管理平台上直接切换系统，而且是免费的， 如果你也遇到同样的问题，请参考[这篇文章](http://jingyan.baidu.com/article/acf728fd28eafef8e510a3c6.html)

重新执行了以上的步骤，编译的时间比较久，然后开始安装

> make install

#####然后测试一下

> node -v

#### 开启测试服务器
直接写一个监听脚本作为demo测试
> vi app.js

简单的服务器脚本app.js：

<pre><code>
var http = require('http');

http.createServer(function(req,res){
    res.writeHead(200,{'Content-Type':'text/plain'});
    res.end('Hello body!');
}).listen(80,'127.0.0.1');

console.log('NodeJS Server sunning at http://127.0.0.1:80');
</code></pre>

然后启动服务器：

> node app.js

如果想要能给外网访问，将listen中的127.0.0.1改为阿里云分配给你的服务器IP地址就可以了，原因是ECS服务器里面的hosts没有写外网配置，直接写这ip会导致通过外网访问：http://阿里云提供的IP 失败！


### 资源

* [阿里云下ECS下搭建node.js](http://www.jianshu.com/p/01dbdc623cea)
* [ECS 下使用nodejs+express+forever](http://www.jianshu.com/p/f93a09c9e848)
* [yum介绍](http://www.cnblogs.com/chuncn/archive/2010/10/17/1853915.html)

