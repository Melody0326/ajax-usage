# ajax-usage
关于一些前端和后端ajax的用法

## AJAX

AJAX是Asynchronous Javascript and XML 异步的javascript和XML

是一种在不重新加载页面的情况下能和服务器交换数据并更新部分页面内容的技术，可以不需要任何浏览器插件而且绝大多数浏览器支持，同时打破了以往之用表单传输数据的方式，实现了按需发送数据。

AJAX的核心是XMLHttpRequerst对象，简称XHR，提供了向服务器发送请求和解析服务器响应提供了流畅的接口。IE7+、Firefox、Opera、Chrome 和Safari 都支持原生的XHR对象。


### 关于AJAX前端实现

#### 原生的XHR对象

首先需要先创建一个XHR对象，所有现代浏览器均支持XMLHttpRequest对象。

1.利用构造函数创建新的对象，实例化XMLHttpRequest

var xhr = new XMLHttpRequest();

//至于IE7之前早期版本,使用的是ActiveXObject对象，根据W3C手册,可以这样创建一个XHR对象。

var xhr = new ActiveXObject("Microsoft.XMLHTTP");


2.连接服务器

使用XHR对象,调用的第一个方法是open()，该方法有三个参数：method要发送的请求的类型，通常为GET或者POST；url指的是文件在服务器上的位置；async表示请求是否异步处理，true为异步false为同步。

例如:

xhr.open("get","a.php",true);

但是open()方法不会真正的发送请求而只是启动了一个请求准备发送。


3.发送请求

真正发送请求到服务器需要使用send()方法

send()方法只接受一个参数：要求发送的数据，如果是不发送数据，参数设置为null而且必须设置，因为对有些浏览器这个参数是必需的。

例如:

xhr.open("get","example.php",true);

xhr.send(null);

对于GET请求还可以向URL添加信息

xhr.open("get","a.php?name=ma",true);

xhr.send(null);


如果是POST请求

POST请求通常是用于向服务器发送应该被保存或者是处理的数据，而且相对于GET来说可以包含很多数据且格式不限。

但是如果使用POST请求，服务器中必须有程序来读取发送过来的数据，然后解析处理，所以我们需要添加HTTP头，然后再send()中添加想发送的数据。

例如:

xhr.open("post","a.php",true);

xhr.setRequestHeader("Content-Type","application/x-www-form-urlencoded");

xhr.send("name=ma&age=21");


在提供服务器请求的过程中，有两种方式，分别是：GET和POST。

GET： 一般用于信息获取，用URL传递参数，对发送信息数量有限制，一般2000个字符

POST：一般用于修改服务器上的资源，对所发送的信息没有限制

GET： 是通过地址栏来传值

POST：是通过提交表单来传值

在以下情况中，请使用 POST 请求：

无法使用缓存文件（更新服务器上的文件或数据库）

向服务器发送大量数据（POST 没有数据量限制）

发送包含未知字符的用户输入时，POST比 GET更稳定也更可靠


4.接收响应

当请求被发送到服务器后，我们需要执行一些响应任务，在处理响应的时候主要是针对XHR的几个重要属性进行检测:

1.readyState 表示当前XHR对象状态，取值0-4,我们只对 readyState = 4 的阶段感兴趣，此时表示数据已经准备就绪而且用户可以使用；

2.status 表示响应HTTP的状态

HTTP      状态码                  说明

200   OK                    服务器成功返回

304   304 Not Modified      表示请求资源没有被修改可以直接使用缓存数据

400   Bad Request           语法错误导致服务器无法识别

404   Not found             URL不存在

500   Internal Server Error 服务器遇到意外错误无法完成请求

503   ServiceUnavailable    服务器过载导致无法完成请求

3.responseText 服务器返回的文本，作为响应主体被返回的文本

在接受到响应后，第一步检查status属性，以确定响应成功返回，如果发送请求是要求异步发送，检测readyState属性，确定当前活动阶段。

readyState属性的值每次改变都会触发onreadystatechange事件，利用这个事件来检测每次变化后的readyState的值。


如果请求为异步，则按照以下方式编写onreadystatechange函数

xhr.onreadystatechange = function(){

	if(xhr.readyState == 4 && xhr.status == 200){
	
		alert(xhr.responseText);
		
	}else{
	
		alert("Request is unsuccessful !");
		
	}
	
	xhr.open("get","a.php",true);
	
	xhr.send(null);
	
}


如果请求要求是同步,则可以不必编写onreadystatechange函数，把处理数据代码放在send()后面即可

xhr.open("get","a.php",false);

xhr.send(null);

alert(xhr.responseText);


#### jQuery提供了更多的快捷操作,不用去处理XHR对象，使AJAX变得极其简单

jQuery中最常用的是$.get(), $.post(), $.ajax()方法，另外还有load()，$.getScript()和$.getJson()

$.get()和$.post()方法

二者参数和用法完全相同,以$.get()为例

$.get()支持四个参数：

url请求的页面的URL地址;

data可选,发送到服务器的数据,要求以键值对的方式{name:value,name:value}；

callback可选,载入成功的回调函数,两个参数:data返回的内容;textStatus请求状态；

type可选,服务器端返回内容的格式,通常为text,html,xml,json,script和_default


例如:

$('[type = "button"]).click(function(){

	$.get("a.php",
	
	          {name:"ma",age:21},
	          
	          function(data){
	          
			alert(data);
			
		},"text");
		
})

上述实现了点击button按钮后想a.php发送get请求,并且发送数据name=ma,age=21


因为在PHP中,POST和GET发送的数据都会通过$_REQUEST[]获取，因此$.post()和$.get()可以根据需求进行切换。

因为$.post()和$.get()只可以完成简单的AJAX请求，jQuery还提供了$.ajax()方法，该方法不仅能实现前两者的功能，还提供了更丰富的回调函数，提示给用户更多信息。


$.ajax()的用法类似,但是提供了更多的参数
url

type：post或者get

timeout：设置超时时间

data

dataType:返回的数据类型

complete:请求完成后的回调函数,请求失败或者成功都执行

success:请求成功完成后的回调函数

error:请求失败是被调用的函数

global:默认true,表示是否触发全局AJAX事件

例如:
$('[type = "button"]').click(function(){

	$.ajax({
	
		type: "get",
		
		url:"a.php"
		
		dataType:"json",
		
		success: function(data){
		
			alert(data);
			
		}
		
	})
	
})

上述代码和$.get("a.php",function(data){alert(data)},"json")具有一样的功能

### 整个ajax异步可以总结为：

创建XMLHttpRequest对象，即创建一个异步调用对象

创建一个新的HTTP请求，并指定该HTTP请求的方法、URL及是否异步

设置响应HTTP请求状态变化的函数

发送HTTP请求

获取异步调用返回的数据

使用JavaScript和DOM实现局部刷新


### 关于AJAX后端实现

这里以node为例来介绍

例：
##### 前端部分

var oButton = document.getElementById('myButton');

var sName = document.getElementById('isName').value;

oButton.onclick = function() {

    ajax({
    
        method : 'post',
        
        url : 'isuser',
        
        data : sName,
        
        success : function () {
        
            console.log('useable name');
            
        },
        
        async : false
        
    });
    
}

##### node端部分

//用户注册时判断用户名是否已存在

app.post('/isuser', function(req, res) {

  var username = req.body.username;
  
  User.isUser(username, function(status){  //查询数据库，牵扯知识点多，不详述 
  
    if(status == 'OK'){
    
      res.send(200);          
      
    }else{
    
      res.send(404);
      
    }
    
  });
  
  
### Ajax优缺点

Ajax带来的好处：

1、通过异步模式，实现动态不刷新，提升了用户体验 

2、优化了浏览器和服务器之间的传输，减少不必要的数据往返，减少了带宽占用 

3、Ajax在客户端运行，承担了一部分本来由服务器承担的工作，减少了大用户量下的服务器负载

Ajax的缺点：

1、Ajax不支持浏览器back按钮

2、安全问题,Ajax暴露了与服务器交互的细节 

3、对搜索引擎的支持比较弱

4、破坏了程序的异常机制

5、不容易调试
