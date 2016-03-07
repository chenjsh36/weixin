# 开发微信公众号【3】接收消息

>上一篇实现了基础支持，接下来可以实现一些简单的功能了！

###接收普通消息
当普通微信用户向公众号发送消息的时候，微信服务器将POST消息的XML数据包发到开发者填写的URL上，所以我们之前设置的用来验证服务器是否是我们的那个URL便成了服务器与微信通信的唯一接口，不过它这次不用GET方法，而是用POST，并且发送的是XML，且要求我们回复XML
####nodejs解析xml格式的请求
之前用的是body-parser,尝试直接使用req.body获取不到相应的数据包,原因是body-parser只对req.body 为json格式的做解析，所以对于xml格式的，解析后就req.body就变成空对象了
通过百度查询找到了两个xml转化模块，一个是xml2js，一个是express-xml-bodyparser
两个模块都可以转化xml为js对象，笔者因为使用了express框架，而且只需要在请求中调用转换，因此选择了后者，如果你想在其他地方使用到转化或者前端框架不是express的化，那么前者则是更好的选择。
然后在路由中调用：
<pre><code>
var xmlparser = require('express-xml-bodyparser');

app.post('你设置的公众号URL', xmlparser({trim: false, explicitArray: false}), function(req, res) {

  console.log(req.body);

})
</code></pre>
####nodejs返回xml格式
返回xml格式需要在res中提前设置Content-Type为application/xml
<pre><code>
var xmlparser = require('express-xml-bodyparser');

app.post('你设置的公众号URL', xmlparser({trim: false, explicitArray: false}), function(req, res) {

  console.log(req.body);

  data = '<xml><ToUserName>chenjsh36</ToUserName></xml>';

  res.writeHead(200, {'Content-Type': 'application/xml'});

  res.end(data);

})
</code></pre>
####测试接口
* 通过微信公众号平台提供的[接口](https://mp.weixin.qq.com/debug/cgi-bin/apiinfo?t=index&type=%E6%B6%88%E6%81%AF%E6%8E%A5%E5%8F%A3%E8%B0%83%E8%AF%95&form=%E6%96%87%E6%9C%AC%E6%B6%88%E6%81%AF)去测试
* 直接访问自己的公众号，并向自己的公众号发送消息来测试

###结语

不过作为个人开发者公众号，开放的会话功能只有接收消息、接收事件推送、接收语音识别结果（暂时不知道是什么鬼）、自动回复这些接口
一些好玩的接口如获取用户列表、获取用户地理位置、素材管理、微信支付、分享接口等都没有开发给个人运营者

###资源
* [接收普通消息](http://mp.weixin.qq.com/wiki/10/79502792eef98d6e0c6e1739da387346.html)
* [将XML解析成JSON](http://ourjs.com/detail/54b1ce8c232227083e000004)
* [express-xml-bodyparser](https://www.npmjs.com/package/express-xml-bodyparser)
* [xml2js](https://blog.nraboy.com/2015/01/parse-xml-response-nodejs/?utm_source=ourjs.com)
* [nodejs 输出xml](https://segmentfault.com/q/1010000002661505/a-1020000002661618)
