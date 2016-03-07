#node 开发微信公众号

在上一篇文章我们主要学会了如何在阿里云上搭建一个持久运行的服务器，并且使用了express框架来快速实现，在这一章便利用这个服务器来与微信公众号平台对接，来开发一个公众号提供各种功能

###申请公众号
申请公众号请前往`微信开发平台`进行申请

###配置环境
通过前面几篇文章我们已经有了开发的环境

####开发->基本配置->服务器配置
这里可以设置你自己的服务器地址、Token、EncodingAESKey还有消息加解密方式，这样用户消息和开发者需要的事件推送，将会被转发到你设定的URL中
####验证原理
配置的时候微信公众号会向你设置的URL发送一个GET请求作为验证（这里需要查看[指南](http://mp.weixin.qq.com/wiki/8/f9a0b8382e0b77d87b3bcc1ce6fbc104.html)）
请求包括四个参数：
signature：微信的加密签名，这个签名结合了开发者填写的token参数和请求中的timestamp参数、nonce参数
timestamp是时间戳、nonce是随机数、echostr是随机字符串

这个验证将会根据你的服务器是否放回原来的echostr随机字符串来判断那是不是你的服务器，当然你可以不做任何检测直接传回echostr，不过假如别人也填了你的URL，那就嘿嘿了。
####服务器实现
写验证函数，需要引用crypto库，使用这个库里面的函数对传过来的参数nonce、echostr和你自己填写的令牌（请好好保存，并确保平台和你服务器使用的是同一个token，否则就算签名算法一样也没有办法）

写一个验证模块validateToken.js
<code>
var crypto = require('crypto');
function validateToken(req, res) {
        var query = req.query;
        var echostr = query.echostr;
        var signature = query.signature;
        var timestamp = query['timestamp'];
        var nonce = query.nonce;
        console.log('show request params: ', signature, timestamp, nonce, echostr);
        var ori_arr = new Array();
        ori_arr[0] = nonce;
        ori_arr[1] = timestamp;
        ori_arr[2] = "webfeofchenjsh36";
        ori_arr.sort()
        var original = ori_arr[0] + ori_arr[1] + ori_arr[2];
        console.log('original str' + original);
        console.log('signature ' + signature);
        var scyptoString = sha1(original);
        console.log('after sha: ', scyptoString, signature);
        if (signature == scyptoString) {
                res.send(echostr);
        }
        else {
                res.send('bad token');
        }
}
function sha1(str) {
        var md5sum = crypto.createHash('sha1');
        md5sum.update(str);
        str = md5sum.digest('hex');
        return str;
}
function getAccessToken() {
        var appid = "";
        var appsecret = "";
        var url = "https://api.weixin.qq.com/cgi-bin/token?grant_type=client_credential&appid=fdgf&secret=dfdsgfrfsss";
}
exports.validateToken = validateToken;
</code>

然后再路由中引入这个模块并调用对应的函数
var validateTokenFunc = require('./validateToken').validateToken;
app.get('/',function(req, res){
    validateTokenFunc(req,res);
});
最后运行服务器，在公众号平台提交配置，显示’提交成功‘便成功通过验证，如果失败则检查下传参是否正确，签名是否一样，token是否一致等问题。

##LINK
* [使用node开发微信公众号](http://www.jb51.net/article/54705.htm)
* [微信开发者平台](https://open.weixin.qq.com/cgi-bin/showdocument?action=dir_list&t=resource/res_list&verify=1&id=open1419318183&token=&lang=zh_CN)