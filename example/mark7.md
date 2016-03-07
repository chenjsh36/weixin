>资源

[webot项目地址](https://github.com/node-webot)


>导语

接触node已经有一段时间了，通过这一次使用node进行微信开发，学习了挺多新的知识：

1、使用node + express 搭建服务器
2、node进行https请求，数据交互方式接触了xml，知道了需要声明header的Content-Type为application/xml
3、将代码进行模块化，抽离出配置、函数，实践了下回调机制
4、编码问题，如动态创建的url请求需要使用encodeURI进行编码处理

由于微信的其他接口基本类似，因此就不再逐一做笔记浪费时间，接下来的计划是看能否申请到一个服务号或者企业号，以实现一些比较复杂的功能，与此同时拜读下webot团队大牛们写的微信开发模块，相信可以学到很多新的知识。

>笔记

###wechat-api
这是一个微信公众平台功能API模块，它将多个功能集合在了一起供开发者直接调用

#### index.js 入口
代码中可以看到首先引用了一个api_common模块，并声明为变量API，然后通过这个模块的mixin函数，将其他模块的功能拓展到变量API上，最后再exports = API，使得变量只有一个
#### index_common.js 基础模块
模块的代码定义了一个API对象和AccessToken对象，其中API对象对外公开，而accessToken对象则供API调用
* 在创建AccessToken的对象的时候用到了 _安全模式的工厂方法_ 设计模式 
<pre><code>
var AccessToken = function (accessToken, expireTime) {

  if (!(this instanceof AccessToken)) {

    return new AccessToken(accessToken, expireTime);

  }

  this.accessToken = accessToken;

  this.expireTime = expireTime;
};
</code></pre>
防止了使用者在不知情或忘记的情况下直接使用var tmp = AccessToken(a,b)去创建对象导致了错误。

* 在判断accessToken是否过期的时候使用了双感叹号，一个感叹号是取非，双感叹号则是非非得正，意义其实在于它隐式地将值转化为bool类型 
<pre><code>
AccessToken.prototype.isValid = function () {

    return !!this.accessToken && (new Date().getTime()) < this.expireTime;
};
</code></pre>
* API对象定义了基础函数，如保存token和appsecret并获取token的方法，它还定义了一个mixin函数，以拓展其他功能
<pre><code>
API.mixin = function (obj) {
	for (var key in obj) {
		if (API.prototype.hasOwnProperty(key)) {
			throw new Error('Don\'t allow override existed prototype method. method: '+ key);
		}
		API.prototype[key] = obj[key];
	}
};
</code></pre>
通过遍历传进来的对象属性，如果不发生覆盖就赋值给API，从而实现对象的合并

#### api_template.js 客服消息
剩下的模块都是基于api_common.js对象的功能拓展
<pre><code>
exports.sendText = function (openid, text, callback) {

      this.preRequest(this._sendText, arguments);

};
</code></pre>
这个模块只是[webot](https://github.com/node-webot)其中一个板块，还有微信公共平台消息接口服务中间件、微信企业版第三方应用接口、微信公共帐号自动回复机器人等，使用这些板块进行开发基本5分钟可以搞定，源码待研究~~