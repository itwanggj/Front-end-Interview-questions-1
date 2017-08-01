---
title: javascript_Ajax
date: 2016-05-14 17:33:01
tags: 
	- Ajax 
	- Javascript
---
# Ajax
AJAX即“Asynchronous Javascript And XML”（异步JavaScript和XML），是指一种创建交互式网页应用的网页开发技术。

AJAX = 异步 JavaScript和XML（标准通用标记语言的子集）。

AJAX 是一种用于创建快速动态网页的技术。

通过在后台与服务器进行少量数据交换，AJAX 可以使网页实现异步更新。这意味着可以在不重新加载整个网页的情况下，对网页的某部分进行更新。

传统的网页（不使用 AJAX）如果需要更新内容，必须重载整个网页页面。

Ajax 的核心是 JavaScript 对象 XMLHttpRequest。该对象在 Internet Explorer 5 中首次引入，它是一种支持异步请求的技术。简而言之，XMLHttpRequest使您可以使用 JavaScript 向服务器提出请求并处理响应，而不阻塞用户。

<!-- more -->
# XMLHttpRequest
创建XMLhttpRequest对象

	var request = new XMLHttpRequest();

由于有浏览器兼容性的问题，所以我们必须要考虑这个问题，我们可以封装一个函数来解决

	 function createXHR(){
	         if(typeof XMLHttpRequest!="undefined"){
	             return new XMLHttpRequest();
	         }else if(typeof ActiveXObject!="undefined"){
	             if(typeof arguments.callee.activeXString!="string"){
	                 var versions=["MSXML2.XMLHttp.6.0","MSXML2.XMLHttp.3.0","MSXML2.XMLHttp"],
	                     i,len;
	                 for(i=0,len=versions.length;i<len;i++){
	                     try{
	                         new ActiveXObject(versions[i]);
	                         arguments.callee.activeXString=versions[i];
	                         break;
	                     }catch(ex){
	     
	                     }
	                 }
	             }
	             return new ActiveXObject(arguments.callee.activeXString);
	         }else{
	             throw new Error("NO XHR object available");
	         }
	     }
	     
	     var xhr=new createXHR();

# XHR 的使用方法
## 发布同步的请求
使用 XHR 时，首先要调用 open() 方法，传递三个参数：

- 要发送的请求类型（ get , post 等）
- 请求的 url
- 是否异步发送


要发送特定的请求，必需像下面这样调用 send() 方法


	
	xhr.open("get",".request.php",false);
	xhr.send(null);

这里 send() 方法接收一个参数，作为请求主体发送的数据。如果不需要通过请求主体发送数据，这里必须传入 null ，因为这个参数对有些浏览器来说是必需的。调用 send() 之后，请求就会被分派到服务器。
由于这次请求是同步的，JavaScript 代码会等到服务器响应之后再继续执行。在收到响应之后，相应的数据会自动填充XHR对象的属性，相关的属性简介如下：

- responseText: 作为响应主体被返回的文本。
- responseXML: 如果响应的内容类型是 “text/xml”或”application/xml”，这个属性中将保存包含着相应数据的XML DOM文档。
- status: 响应的HTTP状态。
- statusText: HTTP状态的说明。

接受响应之后，第一步是检查 status 属性，以确定响应已经成功返回。状态码：

- 200 表示成功
- 304 表示请求的资源并没有修改，可以直接使用浏览器中缓存的版本，响应也是有效的

下面状态码举例说明如何检查两种状态码的状态：

	xhr.open("get","request.txt",false);
	     xhr.send(null);
	     
	     if((xhr.status >= 200 && xhr.status < 300)|| xhr.status == 304){
	         alert(xhr.responseText);
	     }else{
	         alert("Request was unsuccessful: " + xhr.status);
	     }

## 发送异步请求
 

向前面这样发送同步请求当然没问题，但多数情况下，我们还是要发送异步请求，才能让 JavaScript 继续执行而不必等待响应。此时，可以检测 XHR 对象的 readyState 属性，该属性表示请求/响应过程中的当前活动阶段。这个属性可取的值如下：



- 0：未初始化。尚未调用 open() 方法。
- 1：启动。已经调用 open() 方法，但尚未调用 send() 方法。
- 2：发送。已经调用 send() 方法，但尚未接收响应。
- 3：接收。已经接收到部分响应数据。
- 4：完成。已经接收到全部响应数据，而且已经可以在客户端使用了。

只要 readyState 属性的值由一个值变为另一个值，都会触发一次 readystatechange 事件。可以利用这个事件来检测每次状态变化后的 readyState 的值，通常，我们只对 readyState 值为 4 的阶段感兴趣，因为这时所有的数据都已经就绪。不过，必须在调用 open() 之前指定 onreadyState 事件处理程序才能确保跨浏览器兼容性。例子如下：

	var xhr = createXHR();
	     xhr.onreadyStatechange = function(){
	         if(xhr.readyState == 4){
	             if((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304){
	                 alert(xhr.responseText);
	             }else{
	                 alert("Request was unsuccessful:" + xhr.status );
	             }
	         }
	     };
	     xhr.open("get","example.txt",true);
	     xhr.send(null); 

在接收到响应之前还可以调用 abort() 方法来取消异步请求，如下所示

	xhr.absort();

## HTTP头部信息
每个 HTTP 请求和响应都会带有响应的头部信息，有的对开发人员有用，有的也没有什么用，XHR 对象也提供了操作这两种头部（即请求头部和响应头部）信息的方法。
默认情况下，在发送 XHR 请求的同事，还会发送下列头部信息。



- Accept: 浏览器能够处理的内容类型。
- Accept-Charset: 浏览器能够显示的字符集。
- Accept-Encoding: 浏览器能够处理的压缩编码。
- Accept-Language: 浏览器当前设置的语言。
- Connection: 浏览器与服务器之间连接的类型。
- Cookie: 当前页面设置的语言。
- Host: 发出请求的页面所在的域。
- Referer: 发出请求的页面的URI。
- User-Agent: 浏览器的用户代理字符串。

使用 setRequestHeader() 方法可以设置自定义的请求头部信息。这个方法接受两个参数：头部字段的名称和头部字段的值。要成功发送请求头部信息，必须在调用 open() 方法之后且调用 send() 方法之前调用 setRequestHeader(),如下面的例子所示。

	 var xhr = createXHR();
	     xhr.onreadystatechange = function(){
	         if(xhr.readyState == 4){
	             if((xhr.status >= 200 && xhr.status < 300) || xhr.status = 304){
	                 alert(xhr.responseText);
	             }else{
	                 alert("Request was unsuccessful: " + xhr.status);
	             }
	         }
	     };
	     xhr.open("get","request.php",true);
	     xhr.setRequestHeader("MyHeader","MyValue");
	     xhr.send(null); 

服务器在接收到这种自定义的头部信息之后，可移植性响应的后续操作。建议使用自定义的头部字段名称，不要使用浏览器正常发送的字段名称。

调用 XHR 对象的 getResponseHeader() 方法并传入头部字段名称，可以取得相应的响应头部信息。而调用 getAllResponseHeaders() 方法可以取得一个包含所有头部信息的长字符串。看下面的例子：
	
	var myHeader=xhr.getResponseHeader("MyHeader");
	var allHeader=xhr.getAllResponseHeaders();


# 请求

## GET请求

GET 是最常见的请求类型，最常用于向服务器查询某些信息。必要时，可以讲查询字符串参数追加到 URL 的末尾，以便将信息发送给服务器。对 XHR 而言，位于传入 open() 方法的 URL 末尾的查询字符串必须经过正确的编码才行。
使用 GET 请求经常会发生的一个错误，及时查询字符串的格式有问题。查询字符串中每个参数的名称和值必须使用 encodeURIComponent() 进行编码，然后才能放到 URL 的末尾；而且所有名-值对都必须由和号(&)分隔，例子如下：


	xhr.open("get","request.php?name1=value1&name2=value2",true);
下面这个函数可以辅助向现有 URL 的末尾添加查询字符串参数：


	function addURLParam(url,name,value){
	    url += (url.indexOf("?") == -1? "?":"&");
	    url += encodeURIComponent(name) + "=" + encodeURIComponent(value);
	}

使用：

	var url="request.php";
 
添加参数

	url = addURLParam(url,"name","Nocholas");
	url = addRULParam(rul,"book","Professional JavaScript");
 
初始化请求

	xhr.open("get",url,false);

## POST请求

POST 请求通常用于向服务器发送应该被保存的数据。POST 请求应该吧数据作为请求的主体提交，而 GET 请求传统上不是这样。
默认情况下，服务器对 POST 请求和提交 Web 表单的请求并不会一视同仁。因此，服务器端必须有程序来读取发送过来的原始数据，并从中解析出有用的部分。不过，我们可以使用 XHR 来模仿表单提交：首先将 Content-Type 头部信息设置为 application/x-www-from-urlencoded,也就是表单提交时的内容类型，其次是以适当的格式创建一个字符串。
如下所示：


	 function submitData(){
	         var xhr = createXHR();
	         xhr.onreadystatechange = function(){
	             if(xhr.readyState == 4){
	                 if((xhr.status >= 200 && xhr.status < 300) || xhr.status = 304){
	                     alert(xhr.responseText);
	                 }else{
	                     alert("Request was unsuccessful: " + xhr.status);
	                 }
	             }
	         };
	         xhr.open("post","example.php",true);
	         xhr.setRequestHeader("Content-Type","application/x-www-from-urlencoded");
	         var form=document.getElementById("user-info");
	         xhr.send(serialize(form));
	     } 

这个函数可以将 ID 为 “user-info” 的表单中的数据序列化之后发送给服务器。

## 跨域资源

通过 XHR 实现 Ajax 通信的一个主要限制，来源于跨域安全策略。
CORS(Cross-Origin Resource Sharing，跨域资源共享)背后的基本思想，及时使用自定义的HTTP头部让浏览器与服务器进行沟通，从而决定请求或响应是应该成功，还是应该失败。
比如一个简单地使用 GET 或 POST 发送的请求，它没有自定义的头部，而主题内容是 text/plain。在发送该请求时，需要给它附加一个额外的 Origin 头部，其中包括请求页面的源信息（协议、域名、端口），以便服务器根据这个头部信息来决定是否给予响应，下面是 Oringin 头部的一个示例：

	Origin: http://www.ibm.com/

如果服务器认为这个请求可以接受，就在 Access-Control-Allow-Origin 头部中回发相同的源信息（如果是公共资源，可以回发 “*”）。例如：

	Access-Control-Allow-Origin: http://www.ibm.com/

如果没有这个头部，或者有这个头部但源信息不匹配，浏览器就会驳回请求。正常情况下，浏览器会处理请求。注意，请求和响应都不包含 cookie 信息。

##　IE 对 CORS 的实现
微软在 IE8 中引入了 XDR (XDomainRequest) 类型。这个对象与 XHR 类似，但能实现安全可靠的跨域通信。XDR 对象的安全机制部分实现了 W3C 的 CORS 规范。以下是 XDR 与 XHR 的一些不同之处。

cookie 不会随请求发送，也不会随响应返回。
只能设置请求头部信息的 Content-Type 字段。
不能访问响应头部信息。
只支持GET和POST请求。
所有的 XDR 请求都是异步执行的，不能用它来创建同步请求。请求返回之后，会触发 load 事件，响应的数据也会保存在 responseText 属性中。

在接收到响应后，你只能访问响应的原始文本；没有办法确定响应的状态代码。而且，只要响应有效就会触发 load 事件，如果失败（包括响应中缺少 Access-Control-Allow-Origin头部），就会触发 error 事件。遗憾的是，除了错误本身之外，没有其他信息可用，因此唯一能够确定的就只有请求未成功了。要检测错误，可以像下面这样指定一个 onerror 事件处理程序。

	
	var xdr=new XDomainRequest();
	xdr.onload=function(){
	    alert(xdr.responseText);
	};
	xdr.onerror=function(){
	    alert("An erro occurred.");
	};
	xdr.open("get","http://www.somewhere-else.com/page");
	xdr.send(null);

为支持 POST 请求，XDR 对象提供了 contentType 属性，用来表示发送数据的格式，如下所示：


	var xdr=new XDomainRequest();
	xdr.onload=function(){
	    alert(xdr.responseText);
	};
	xdr.onerror=function(){
	    alert("An erro occurred.");
	};
	xdr.open("post","http://www.somewhere-else.com/page");
	xdr.contentType="application/x-www-form-urlencoded";
	xdr.send("name1=value1&name2=value2");

这个属性通过 XDR 对象影响头部信息的唯一方式。

## 其他浏览器对 CORS 的实现

Forefox3.5+,Sarari4+,Chrome,iOS 版 Sarari 和 Android 平台中的 WebKit 都通过 XMLHttpRequest 对象实现了对 CORS 的原生支持。在尝试打开不同来源的资源时，无需额外编写代码就可以出发这个行为。要请求位于另一个域中的资源，使用标准的 XHR 对象并在 open() 方法中传入绝对 URL 即可，例如：

	
	 var xhr=createXHR();
	     xhr.onreadystatechange=function(){
	         if(xhr.readyState == 4){
	             if((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304){
	                 alert(xhr.responseText);
	             }else{
	                 alert("Request was unsuccessful: " + xhr.status);
	             }
	         }
	     };
	     xhr.open("get","http://www.somewhere-else.com/page/",true);
	     xhr.send(null); 

与 IE 中的 XDR 对象不同，通过跨域 XHR 对象可以访问 status 和 statusText 属性，而且还支持同步请求，跨域 XHR 对象也有一些限制，但为了安全，这些限制是必需的，一下就是这些限制。

不能使用 setRequestHeader() 设置自定义头部。
不能发送和接收 cookie。
调用 getAllResponseHeaders() 方法总会返回空字符串。
对于本地资源，最好使用相对 RUL,在访问远程资源时再使用绝对 URL。

## Preflighted Requests

CORS 通过一种叫做 Preflighted Requests 的透明服务器验证机制支持开发人员使用自定义的头部，GET 或 POST 之外的方法，以及不同类型的主体内容，在使用下列高级选项来发送请求时，就会像服务器发送一个 Preflight 请求。这种请求使用 OPTIONS 方法，发送下列头部。

Origin: 与简单的请求相同。
Access-Control-Request-Method: 请求自身使用的方法。
Access-Control-Request-Headers: (可选) 自定义的头部信息，多个头部以逗号分隔。
以下是一个带有自定义头部的 NCZ 的使用 POST 方法发送的请求。


	Origin: http://www.nczonline.net
	Access-Control-Request-Method: POST
	Access-Control-Request-Headers: NCZ

发送这个请求后，服务器可以决定是否允许这种类型的请求。服务器通过在响应中发送如下头部与浏览器进行沟通。

	
	Access-Control-Allow-Origin: 与简单的请求相同。
	Access-Control-Allow-Method: 允许的方法，多个方法以逗号分隔。
	Access-Control-Allow-Headers: 允许的头部，多个头部以逗号分隔。
	Access-Control-Max-Age: 应该将这个 Preflight 请求缓存多长时间（以秒表示）。

例如：

	
	Access-Control-Allow-Origin: http://www.nczonline.net
	Access-Control-Allow-Method: GET,POST
	Access-Control-Allow-Headers: NCZ
	Access-Control-Max-Age: 1728000

Preflight 请求结束后，结果将按照响应中指定的事件缓存起来。而为此付出的代价只是第一次发送这种请求时会多一次 HTTP 请求。

支持 Preflight 请求的浏览器包括 Firefox3.5+， Sarari4+ 和 Chrome。IE10 及更早版本都不支持。

带凭据的请求（Requests with Credential）

默认情况下，跨域请求不提供凭据（Cookie、HTTP 认证及客户端 SSL 证明等）。通过将 withCredentials 属性设置为 true，可以指定某个请求应该发送凭据。如果服务器接收带凭据的请求，会用下面的 HTTP 头部来响应。


	Access-Control-Allow-Credentials: true

如果发送的是带凭据的请求，单服务器的响应中没有包含这个头部，那么浏览器就不会把响应交给 JavaScript （于是，responseText 中将是空字符串，status 的值为0，而且会调用 onerror() 事件处理程序）。

支持 withCredentials 属性的浏览器有 Firefox3.5+，Sarari4+ 和 Chrome。IE10 及更早版本都不支持。


## 跨浏览器的 CORS (兼容性 CORS的写法)

	function createCORSRequest(method,url){
	    var xhr=new XMLHttpRequest();
	    if("withCredentials" in xhr){
	        xhr.open(method,url,true);
	    }else if(typeof XDomainRequest != "undefined"){
	        xhr = new XDomainRequest();
	        xhr.open(method,url);
	    }else {
	        xhr = null;
	    }
	    return xhr;
	}
	 
	var request = createCORSRequest("get","http://www.somewhere-else.com/page/");
	if(request){
	    request.onload = function(){
	        //对request。responseText进行处理
	    };
	    request.send();
	}

##　其他跨域技术

## JSONP

JSONP 原理：JSONP 是通过动态script元素来使用的，使用时可以作为 src 属性指定一个跨域 URL。 这里的script元素有能力不受限制地从其他域加载资源。因为 JSONP 是有效的 JavaScript 代码，所以在请求完成后，即在 JSONP 响应加载到页面中以后，就会立即执行。



## ajax和jsonp



1. ajax和jsonp这两种技术在调用方式上“看起来”很像，目的也一样，都是请求一个url，然后把服务器返回的数据进行处理，因此jquery和ext等框架都把jsonp作为ajax的一种形式进行了封装；
1. 但ajax和jsonp其实本质上是不同的东西。ajax的核心是通过XmlHttpRequest获取非本页内容，而jsonp的核心则是动态添加<script>标签来调用服务器提供的js脚本。
1. 所以说，其实ajax与jsonp的区别不在于是否跨域，ajax通过服务端代理一样可以实现跨域，jsonp本身也不排斥同域的数据的获取。
1. 还有就是，jsonp是一种方式或者说非强制性协议，如同ajax一样，它也不一定非要用json格式来传递数据，如果你愿意，字符串都行，只不过这样不利于用jsonp提供公开服务。

### 手写ajax

	function ajax(){  
	    var xmlhttp;  
	    if(window.XMLHttpRequest){  
	        xmlhttp = new XMLHttpRequest();  
	    }else{  
	        // code for IE6, IE5  
	        xmlhttp = ActionXObject("Microsoft.XMLHTTP");  
	    }  
	      
	    //判定执行状态  
	    xmlhttp.onreadystatechange = function(){  
	        /* 
	        readyState 
	            0: 请求未初始化 
	            1: 服务器连接已建立 
	            2: 请求已接收 
	            3: 请求处理中 
	            4: 请求已完成，且响应已就绪 
	        status 
	            200:请求成功 
	            404:未找到 
	            500:服务器内部错误 
	        */  
	        if (xmlhttp.readyState==4 && xmlhttp.status==200){  
	            document.getElementById("myDiv").innerHTML=xmlhttp.responseText;//获得字符串形式的响应数据，如果返回的是XML需要单独解析  
	            //responseXML       获得 XML 形式的响应数据  
	            var xmlDoc = xmlhttp.responseXML;  
	            var txt = "";  
	            var num = xmlDoc.getElementsByName("value");//获取节点name=value的值  
	            for(var i=0;i<num.length;i++){  
	                txt = txt+num[i].childNodes[0].nodeValue+"<br />";  
	            }  
	            document.getElementById("myDiv2").innerHTML = txt;  
	        }  
	    }  
	      
	    //@param 最后一个参数表示是否是异步提交，为了避免使用缓存我们加上一个时间戳  
	    xmlhttp.open("Get","url"+  
	        (function(){  
	            var date = new Date();  
	            return date.getSeconds();     
	        })  
	    ,true);  
	      
	    //设置头信息  
	    xmlhttp.setRequestHeader("Content-type","application/x-www-form-urlencoded");  
	      
	    //将信息发送到服务器  
	    xmlhttp.send();   
	      
	}  


### 手写ajax
	
    function createXHR(){
	    if(typeof XMLHttpRequest != "undefined"){ // 非IE6浏览器
	        return new XMLHttpRequest();
	    }else if(typeof ActiveXObject != "undefined"){   // IE6浏览器
	        var version = [
	                    "MSXML2.XMLHttp.6.0",
	                    "MSXML2.XMLHttp.3.0",
	                    "MSXML2.XMLHttp",
	        ];
	        for(var i = 0; i < version.length; i++){
	            try{
	                return new ActiveXObject(version[i]);
	            }catch(e){
	                //跳过
	            }
	        }
	    }else{
	        throw new Error("您的系统或浏览器不支持XHR对象！");
	    }
	}
	// 转义字符
	function params(data){
	    var arr = [];
	    for(var i in data){
	        arr.push(encodeURIComponent(i) + "=" + encodeURIComponent(data[i]));
	    }
	    return arr.join("&");
	}
	// 封装ajax
	function ga_ajax(obj){
	    var xhr = createXHR();
	    obj.url = obj.url + "?rand=" + Math.random(); // 清除缓存
	    obj.data = params(obj.data);      // 转义字符串
	    if(obj.method === "get"){      // 判断使用的是否是get方式发送
	        obj.url += obj.url.indexOf("?") == "-1" ? "?" + obj.data : "&" + obj.data;
	    }
	    // 异步
	    if(obj.async === true){
	        // 异步的时候需要触发onreadystatechange事件
	        xhr.onreadystatechange = function(){
	            // 执行完成
	            if(xhr.readyState == 4){
	                callBack();
	            }
	        }
	    }
	    xhr.open(obj.method,obj.url,obj.async);  // false是同步 true是异步 // "demo.php?rand="+Math.random()+"&name=ga&ga",
	    if(obj.method === "post"){
	        xhr.setRequestHeader("Content-Type","application/x-www-form-urlencoded");
	        xhr.send(obj.data);
	    }else{
	        xhr.send(null);
	    }
	    // xhr.abort(); // 取消异步请求
	    // 同步
	    if(obj.async === false){
	        callBack();
	    }
	    // 返回数据
	    function callBack(){
	        // 判断是否返回正确
	        if(xhr.status == 200){
	            obj.success(xhr.responseText);
	        }else{
	            obj.Error("获取数据失败，错误代号为："+xhr.status+"错误信息为："+xhr.statusText);
	        }
	    }
	}