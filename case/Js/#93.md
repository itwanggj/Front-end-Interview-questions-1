 # 动态加载JS文件，并根据JS文件的加载状态来执行自己的回调函数

动态加载JS文件，并根据JS文件的加载状态来执行自己的回调函数，

在很多场景下，我们需要在动态加载JS文件的时候，根据加载的状态来进行后续的操作，需要在JS加载成功后，执行另一方法，这个方法是依托在加载的JS文件上的（调用了这JS里面的方法等等），此时就需要将这个方法作为JS加载后的回调函数进行执行

	//javascript
	function loadJs(loadUrl,callMyFun,argObj){
            var loadScript=document.createElement('script');
            loadScript.setAttribute("type","text/javascript");
            loadScript.setAttribute('src',loadUrl);
            console.log(loadUrl)
            document.getElementsByTagName("head")[0].appendChild(loadScript);
            //判断服务器
            if(navigator.userAgent.indexOf("IE") >=0){
                //IE下的事件
                loadScript.onreadystatechange=function(){
                    if(loadScript && (loadScript.readyState == "loaded" || loadScript.readyState == "complete")){
                        //表示加载成功
                        loadScript.onreadystatechange=null;
                        callMyFun()//执行回调
                    }
                }
            }
            else{
                loadScript.onload=function(){
                    loadScript.onload=null;
                    callMyFun();
                }
            }
            console.log(argObj);
        }
        function loadJsBtn(){
            //如需传参
            var argObj={};
            loadJs("js/jqueryww.js",callMyFun,argObj);
        }
        function callMyFun(){
            alert("执行我的回调啊")
        }


	//HTML
	<button onclick="loadJsBtn()">加载JS文件</button>
	