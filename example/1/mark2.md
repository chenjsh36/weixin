#在阿里云上使用node+express+forever构建完整服务器 

##使用forever
通过node直接运行的脚本一旦断开连接就无法运行，使用forever来让服务持久运行在远程的服务器上，forever简单来说就是node的一个扩展工具， 让node程序持续地运行而不断开

###过程

####安装forever
>npm install forever -gd

####run
>forever start app.js

#### 如果想要关闭
>forever stop app.js

#### 更多参数
>forever -help

##使用express
因为之前用过express做过小的项目，express是一个轻量级的快速构建web项目的框架，因此也推荐大家使用，当然你也可以直接裸写或者使用其他框架
###安装express(更多详细说明可以查看下方的express官网)
####新建文件夹
>mkdir yourappname

####进入你的文件夹
>cd yourappname

####npm初始化
>npm init

#### 安装express
>npm install express --save

####安装forever
>npm install forever --save

####服务器脚本
<pre><code>
var express = require('express');

var app = express();

app.get('/', function (req, res) {

    res.send('Hello World!');});

    var server = app.listen(80,'阿里云的IP地址（选填，不填外网无法访问）'， function () { 

        var host = server.address().address; 

        var port = server.address().port; 

        console.log('Example app listening at http://%s:%s', host, port);

});
</code></pre>

###修改配置，使用forever
在script选项中，
添加：
>"start": "forever start index.js"

####运行
这个时候我们就可以使用forever运行我们的服务器了：
> npm start


##资源
* [forever官网](https://github.com/foreverjs/forever)
* [express官网](http://www.expressjs.com.cn/starter/installing.html)