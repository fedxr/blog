---
title: js判断浏览器类型
date: 2019-06-25 14:31:21
categories: JS
tags:
---

   迄今为止，客户端检测仍然是Web 开发领域中一个饱受争议的话题。一谈到这个话题，人们总会不约而同地提到浏览器应该支持一组最常用的公共功能。在理想状态下，确实应该如此。检测Web 客户端的手段很多，而且各有利弊。但最重要的还是要知道，**不到万不得已，就不要使用客户端检测。只要能找到更通用的方法，就应该优先采用更通用的方法。**一言以蔽之，先设计最通用的方案，然后再使用特定于浏览器的技术增强该方案。

<!-- more -->

### 正则方法判断userAgent(用户代理检测)

   用户代理检测通过检测用户代理字符串来确定实际使用的浏览器。在每一次HTTP 请求过程中，用户代理字符串是作为响应首部发送的，而且该字符串可以通过JavaScript 的navigator.userAgent 属性访问。可以在控制台中直接输出查看。比如：

- > Chrome：`"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/75.0.3770.100 Safari/537.36"`

- > Firefox:`"Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:66.0) Gecko/20100101 Firefox/66.0"` 

- > IE:`"Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; Trident/6.0)"`

   根据以上返回内容可以用正则的方法来判断浏览器的类型

```
navigator.sayswho= (function(){
	var ua= navigator.userAgent, tem,
	M= ua.match(/(opera|chrome|safari|firefox|msie|trident(?=\/))\/?\s*(\d+)/i) || [];
	if(/trident/i.test(M[1])){
		tem=  /\brv[ :]+(\d+)/g.exec(ua) || [];
		return 'IE '+(tem[1] || '');
	}
	if(M[1]=== 'Chrome'){
		tem= ua.match(/\b(OPR|Edge)\/(\d+)/);
		if(tem!= null) return tem.slice(1).join(' ').replace('OPR', 'Opera');
	}
	M= M[2]? [M[1], M[2]]: [navigator.appName, navigator.appVersion, '-?'];
	if((tem= ua.match(/version\/(\d+)/i))!= null) M.splice(1, 1, tem[1]);
	return M.join(' ');
})();
```

   同样的也可以用来判断设备类型mobile or pc

```
function browserRedirect() {
	var sUserAgent = navigator.userAgent.toLowerCase();
	var bIsIpad = sUserAgent.match(/ipad/i) == "ipad";
	var bIsIphoneOs = sUserAgent.match(/iphone os/i) == "iphone os";
	var bIsMidp = sUserAgent.match(/midp/i) == "midp";
	var bIsUc7 = sUserAgent.match(/rv:1.2.3.4/i) == "rv:1.2.3.4";
	var bIsUc = sUserAgent.match(/ucweb/i) == "ucweb";
	var bIsAndroid = sUserAgent.match(/android/i) == "android";
	var bIsCE = sUserAgent.match(/windows ce/i) == "windows ce";
	var bIsWM = sUserAgent.match(/windows mobile/i) == "windows mobile";
	document.writeln("您的浏览设备为：");
	if (bIsIpad || bIsIphoneOs || bIsMidp || bIsUc7 || bIsUc || bIsAndroid || bIsCE || bIsWM) {
		document.writeln("phone");
	}
}
browserRedirect();
```

  在服务器端，通过检测用户代理字符串来确定用户使用的浏览器是一种常用而且广为接受的做法。而在客户端，用户代理检测一般被当作一种**万不得已才用的做法**，其优先级排在能力检测之后。

### 能力检测

​        能力检测的目标不是识别特定的浏览器，而是识别浏览器的能力。采用这种方式不必顾及特定的浏览器如何如何，只要确定浏览器支持特定的能力，就可以给出解决方案。

​        以下代码段针对不同浏览器的特有属性或方法实习了能力检测。

```
// Opera 8.0+ (UA detection to detect Blink/v8-powered Opera)
var isOpera = !!window.opera || navigator.userAgent.indexOf(' OPR/') >= 0;
// Firefox 1.0+
var isFirefox = typeof InstallTrigger !== 'undefined';   
// At least Safari 3+: "[object HTMLElementConstructor]"
var isSafari = Object.prototype.toString.call(window.HTMLElement).indexOf('Constructor') > 0;
// Chrome 1+
var isChrome = !!window.chrome && !isOpera;              
// At least IE6，
//'document.documentMode' returns version number of ie.
var isIE = /*@cc_on!@*/false || !!document.documentMode;   

var output = 'Detecting browsers by ducktyping:<hr>';
output += 'isFirefox: ' + isFirefox + '<br>';
output += 'isChrome: ' + isChrome + '<br>';
output += 'isSafari: ' + isSafari + '<br>';
output += 'isOpera: ' + isOpera + '<br>';
output += 'isIE: ' + isIE + '<br>';
document.body.innerHTML = output;
```

### 	补充

​        最后补充两个针对IE10及以下版本的检测代码，提供多个选择：

1.利用IE10及以下独有属性ActiveXObject判断。

```
//ie10及以下
function(){
	if (window.ActiveXObject) {
		//coding......
	}
}
```

2.利用`<!--[if IE 9]><![endif]-->`这样的判断语句来判断浏览器版本，只能适用于IE10以下版本。

```
var isIE = function (ver) {
	var b = document.createElement('b');
	b.innerHTML = '<!--[if IE ' + ver + ']><i></i><![endif]-->';
	return b.getElementsByTagName('i').length === 1;
}
if (isIE(9)) {
	//coding...
}
```

