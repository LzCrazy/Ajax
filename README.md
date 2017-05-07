## Ajax(Asynchronous javascript +xML)
它的核心是技术是XMLHttpRequest(xhr),为向服务器发送请求和解析服务器响应提供了流畅的接口。
能够以异步方式从服务器获取更多的新数据，然后再通过DOM将新数据插入到页面中。

## XMLHttpRequest对象
IE5是第一款引入XHR对象的浏览器，是通过MSXML库中的一个ActiveX对象实现的。IE中有三个不同的版本对象（MSXML2.XMLHttp,MSXML2.XMLHttp3.0,MSXML2.XMLHttp6.0）

```
    //使用于IE7之前的版本和高版本浏览器（兼容性）
    function createXHR(){
       if(typeof XMLHttpRequest != "undefined){
            return new XMLHttpRequest();
        }else if(typeof arguments.calee.activeXString != "string"){
            var versions=["MSXML2.XMLHttp",
           " MSXML2.XMLHttp.3.0","MSXML2.XMLHttp.6.0"],
           i,len;
           
           for(i = 0,len=versions.length;i<len;i++){
                try{
                    new ActiveXObject(versions[i]);
                    argument.call.activeXString=versions[i];
                    break;
                }catch(ex){
                }
           }
        }
        return new ActiveXObject(arguments.callee.activeXString);
    }
```

##xhr的用法
在使用该对象时，要调用的第一个方法就是open(),接收三个参数(1.请求类型（"get","post"）2.URL 3.是否异步发送请求)。

注意：1.URL相当于执行代码的当前页面；2调用open()方法并不会发送真正的请求，只是启动一个请求以备发送。
只能向同一个域中使用相同端口号和协议的URL发送请求，如果URL与启动请求的页面有任何差别，都会引发安全错误

send()方法接收一个参数，作为请求主体发送的数据。

当请求是同步时，代码会等到服务器响应之后再执行。在收到响应后，响应数据会自动填充XHR对象的属性，属性如下
1.responseText:作为响应主体被返回的文本。

2.responseXML:如果响应的内容类型是"text/xml"或者"application/xml",这个属性中将保存包含着响应的XML DOM文档

3.响应的HTTP状态

4.HTTP状态的说明

检测XHR对象的的属性readystate，
取值有（0：为初始化，1：启动，已经调用open方法，2。发送，已经调用send方法，3：接收，收到部分响应，4，完成，接收到所有数据，可以再客户端上使用）

######FromDate
该对象为序列化表单以及表单格式相同的数据（用于通过XHR传输）提供了便利。
```
    var data=new FormData();
    data.apend("name","LzCrazy");
```
通过FromData构造函数中国年传入表单元素，也可以用表单元素的数据预先向其中填入键值对


## http头部信息
XHR对象提供了两种头部操作（请求头部和响应头部），发送的头部信息
1.Accept:浏览器能够处理的内容类型
2.Accept-Charset：浏览器能够显示的字符集
3.Accept-Encoding：浏览器能够处理的压缩编码
4.Accept-Language：浏览器当前设置的语言
5.Connection：浏览器与服务器之间连接的类型
6.host：发出请求的页面所在的域
7.Cookie：当前页面设置的任何Cookie
8.Referer:发出请求的页面的ULR
9.User-Agent：浏览器的用户代理字符串


## 跨域资源共享
通过XHR实现Ajax通讯的一个主要限制，来源域跨域安全策略。默认情况下，XHR对象只能访问与包含它的页面位于同一域中的资源。
CORS（Cross-Origin Resource Sharing），使用自定义HTTP头部让浏览器与服务器之间沟通；在发送请求时，需要给它附加一个额外的origin头部，
其中包含了源信息（协议，域名，端口号）
````
    Origin：http://www.github.com
````
如果服务器认为这个请求可以接收，就在Access-Control-Allow-Origin头部中回发相同的原信息
```
    Access-Control-Allow-Origin：http://www.github.com
```
如果没有这个头部，或者有这个头部但原信息不匹配，浏览器就会驳回请求

浏览器实现跨域
除了IE以外，都是通过XMLHttpRequest对象实现对CORS的原生支持。再尝试打开不同的资源时，无需额外编写代码就可以触发这个行为
```
    var xhr=createXHR();
    xhr.onreadystatechange=function(){
        if(xhr.readystate==4){
            if((xhr.status >= 200 && xhr.status<300)|| xhr.status == 304){
                console.log(xhr.responseText);
            }else{
                console.log("request succ"+xhr.status);
            }
        }
    }
    xhr.open("get","https://www.github.com/LzCrazy",true);
    xhr.send(null);
```
跨域中的限制。
* 不能使用setRequestheader()设置自定义头部
* 不能发送和接收cookie
* 调用getAllResponseheaders()方法总回返回空字符串

无论同源请求还是跨域请求都是使用相同的接口，因此对于本地资源，最好使用相对URL，再访问远程资源时使用绝对URL，能够消除歧义，避免出现限制头部或者本地cookie信息等问题



