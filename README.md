# Ajax笔记
## author：yangdawei
##简介
Asynchronous JavaScript and XML（异步的 JavaScript 和 XML）
这意味着可以在不重新加载整个网页的情况下，对网页的某部分进行更新。

浏览器与服务器之间，采用HTTP协议通信。用户在浏览器地址栏键入一个网址，或者通过网页表单向服务器提交内容，这时浏览器就会向服务器发出HTTP请求。

1999年，微软公司发布IE浏览器5.0版，第一次引入新功能：允许JavaScript脚本向服务器发起HTTP请求。这个功能当时并没有引起注意，直到2004年Gmail发布和2005年Google Map发布，才引起广泛重视。2005年2月，AJAX这个词第一次正式提出，指围绕这个功能进行开发的一整套做法。从此，AJAX成为脚本发起HTTP通信的代名词，W3C也在2006年发布了它的国际标准。

### Ajax 与 XHLHttpRequest
XHLHttpRequest是Ajax的核心，AJAX通过原生的XMLHttpRequest对象发出HTTP请求，得到服务器返回的数据后，再进行处理。
XMLHttpRequest 可以做的事：
1. 在不重新加载页面的情况下更新网页
2. 在页面已加载后从服务器请求数据
3. 在页面已加载后从服务器接收数据
4. 在后台向服务器发送数据

AJAX包括以下几个步骤：
1. 创建AJAX对象
2. 发出HTTP请求
3. 接收服务器传回的数据
4. 更新网页数据

所有现代的浏览器都支持 XMLHttpRequest 对象
`
AJAX只能向同源网址（协议、域名、端口都相同）发出HTTP请求，如果发出跨源请求，就会报错
`
### 同步和异步 阻塞与非阻塞
在node.js中同步和异步是站在http请求的角度来看待的，而阻塞和非阻塞是站在内核跟主进程交换的角度来看待的，阻塞和非阻塞是同步和异步的一个前提，阻塞是同步的，而非阻塞是异步的。
同步：当请求发送给服务器时，要等待服务器的响应，其它事务要等待前面的同步方法完成才能进行下去。
异步：当请求发送给服务器时，不需要等待服务器响应，而进行下面的请求发送，当服务器响应时在回调函数中执行响应的操作。
同步方法有返回值，所以声明一个变量来接受结果；而异步方法没有返回值，是直接把结果传递给回调函数运算。
异步方法的最后一个参数都是一个回调函数。 传给回调函数的参数取决于具体方法，但回调函数的第一个参数都会保留给异常。 
如果操作成功完成，则第一个参数会是null。
所有异步方法一定要晚于同步方法。
异步的方法不能保证执行顺序，正确的方法把嵌套起来。

### XMLHttpRequest对象
创建XMLHttpRequest对象:
```
现代浏览器：
var xml = new XMLHttpRequest()

IE6, IE5 浏览器执行代码
var xml = new ActiveXObject("Microsoft.XMLHTTP");
```
创建连接：
```
xml.open('GET','http://www.baidu.com',true)
```
open() 方法创建一个链接，三个参数，第一个是HTTP请求方法，第二个是URL，第三个是是否异步，true 异步 false 同步。
发送请求：
```
xml.send(null)
```
send方法用于实际发出HTTP请求。如果不带参数，就表示HTTP请求只包含头信息，也就是只有一个URL，典型例子就是GET请求；如果带有参数，就表示除了头信息，还带有包含具体数据的信息体，典型例子就是POST请求。

设置请求头
```
xml.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded')
```
获取响应头
```
xml.getResponseHeader()
```
方法返回HTTP头信息指定字段的值，如果还没有收到服务器回应或者指定字段不存在，则该属性为null。

获得所有响应头
```
xml.getAllResponseHeaders()
```
方法返回服务器发来的所有HTTP头信息。格式为字符串，每个头信息之间使用CRLF分隔，如果没有受到服务器回应，该属性返回null。

监听链接状态事件：
```
xml.onreadystatechange = ()=>{}
```
链接状态：
```
xml.readyState = 4 请求完成 AJAX状态
xml.state = 200 请求成功 Http请求状态
```
readyState状态码：
```
0	UNSENT (未打开)	open()方法还未被调用.
1	OPENED  (未发送)	send()方法还未被调用.
2	HEADERS_RECEIVED (已获取响应头) send()方法已经被调用, 响应头和响应状态已经返回.
3	LOADING (正在下载响应体) 响应体下载中; responseText中已经获取了部分数据.
4	DONE (请求完成)	整个请求过程已经完毕.
```
在通信过程中，每当发生状态变化的时候，readyState属性的值就会发生改变。这个值每一次变化，都会触发readyStateChange事件

请求数据返回类型：
```
xml.responseType="text/html"
```
返回数据：
```
var data = xml.responseText;
data = JSON.parse(data);
```
返回从服务器接收到的字符串，该属性为只读。如果本次请求没有成功或者数据不完整，该属性就会等于null。

### XMLHttpRequest2
```
新版本的XMLHttpRequest对象，针对老版本的缺点，做出了大幅改进
* 可以设置HTTP请求的时限。
* 可以使用FormData对象管理表单数据。
* 可以上传文件。
* 可以请求不同域名下的数据（跨域请求）。
* 可以获取服务器端的二进制数据。
* 可以获得数据传输的进度信息。
```
### AJAX完整实例
```
 // 新建XMLHttpRequest对象
var request = new XMLHttpRequest();
// 状态发生变化时，函数被回调
request.onreadystatechange = function () { 
	// 成功完成
    if (request.readyState === 4) { 
        // 判断响应结果:
        if (request.status === 200) {
            // 成功，通过responseText拿到响应的文本:
           console.log(request.responseText);
        } else {
            // 失败，根据响应码判断失败原因:
            console.log(request.status);
        }
    } else {
        // HTTP请求还在继续...
    }
}

// 发送请求:
request.open('GET', 'http:www.baidu.com',true);
request.send(null);
```
`
未来新的请求方式Fetch
Fetch API提供了一个获取资源的接口(包括跨网络)。任何使用过 XMLHttpRequest 的人都会熟悉它，但是新的API提供了更强大和更灵活的功能集。
`

