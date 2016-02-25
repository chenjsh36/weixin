##实现接口
* 公众号唯一票据 access_token
* 微信服务器IP地址

##获取access token
>access_token是公众号的全局唯一票据，公众号调用各接口时都需使用access_token。开发者需要进行妥善保存。access_token的存储至少要保留512个字符空间。access_token的有效期目前为2个小时，需定时刷新，重复获取将导致上次获取的access_token失效。

access_token 是由中控服务器提供的，目前的过期时间是7200秒，也就是2小时，中控服务器根据这个有效时间去刷新access_token

公众号可以使用AppID和AppSecret调用接口来获得这个票据

###关于node发送https请求
*错误*：发送https请求报错：[error:getaddrinfo ENOTFOUND]，查资料发现是请求的url地址不能添加’https://'
*错误*：关于[Nodejs Post请求报socket hang up](http://www.jb51.net/article/55607.htm)

###坑
发送https请求遇到了一些奇怪的坑，笔者按照
官方的https的[文档](https://nodejs.org/api/https.html#https_https_get_options_callback)有两个例子，
一个是直接https.get(url, function(){...})
一个是通过设置options参数，再调用https.request(options, function() {...})
笔者第一次使用的是后一种方法，接过接连遇到上面两种错误，尝试了半天都没有出来，后面直接调用了前一种方法，接过就通过了。。。。暂时不清楚是什么问题，总之先越过这个坑了，然后终于获得了正确的accesstoken!
附上https请求样例，有大牛知道`为什么后一种方法错误`请指点下在下
<pre><code>
const https = require('https');

https.get('https://api.weixin.qq.com/cgi-bin/token?grant_type=client_credential&appid=APPID&secret=APPSECRET', (res) =>{

  console.log('statusCode: ', res.statusCode);

  console.log('headers: ', res.headers);

  res.on('data', (d) => {

        process.stdout.write(d);
  });

}).on('error', (e) => {
  console.error(e);
});
</code></pre>
最后把它封装成一个模块来使用，后期可扩展为可自动更新提醒获取的功能，即access_token判断是否过期，过期则重新获取，否则继续使用，重新获取的意识不是更改token值，而是更新它的过期时间。

##获取微信服务器的IP地址
有了获取access_token的前例，获取微信服务器的IP地址列表也是类似的，这次笔者将这个功能封装成模块，并提供回调函数给路由器使用，附上样例（ `代码的缩进样式貌似支持性不佳`）
<pre><code>
// 接口模块
const https = require('https');

var wxUrl = 'https://api.weixin.qq.com';

// 获取微信服务器IP列表

getCallBackIP = function(option, callback) {

  var path = '/cgi-bin/getcallbackip';

  var url = wxUrl + path + '?access_token=' + option.access_token;

  var data = [];

  var httpsReq = https.get(url, (res) => {

    res.on('data', (d) => {
        data.push(d);
    }).on('end', () => {
      var buffer = Buffer.concat(data);
      var str = buffer.toString();
      var obj = JSON.parse(str);
      callback(null, obj);
    });
  }).on('error', (e) => {

    console.log(e);
    callback(e, 'Response error!');
  });

  httpsReq.end();

  httpsReq.on('error', function(e) {

    callback(e, 'Request error!');
  });
}

exports.getCallBackIP = getCallBackIP;
</code></pre>
<pre><code>
// 获取微信服务器的IP列表

app.get('/iplist', function (req, res) {

  weixinInterface.getCallBackIP(wxStatus, function(err, doc){

    if (err) {
      console.log('url:/iplist Fail to get IP list !!!');
      res.send('Get APPlist fail err message is  ' + JSON.stringify(doc));
    } else {
      console.log('url:/iplist OK!');
      res.send('Get APPlist ok! :' + JSON.stringify(doc));
    }
  });
});
</code></pre>
[官方文档](http://mp.weixin.qq.com/wiki/0/2ad4b6bfd29f30f71d39616c2a0fcedc.html)可以多读，帮助我们解决很多疑问和学到挺多东西。
##资源
* [接口文档](http://mp.weixin.qq.com/wiki/11/0e4b294685f817b95cbed85ba5e82b8f.htmlf)
* [https请求](https://nodejs.org/api/https.html)