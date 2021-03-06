## AJAX

**（async JavaScript and xml ）异步的JS和xml**

```
首先理解：
	XHTML：可扩展超文本标记语言（eXtensible HyperText Markup Language），表现方式与超文本标记语言（HTML）类似，不过语法上更加严格
	HTML：超文本标记语言（HyperText Markup Language），是一。
	种标识性的语言。它包括一系列标签．通过这些标签可以将网络上的文档格式统一，使分散的Internet资源连接为一个逻辑整体。
```

<!--more-->

##### 1.在ajax中的异步不是我们理解的同步异步编程，而是泛指"局部刷新"，但是我们在以后的AJAX请求中尽可能使用异步获取数据（因为异步数据获取不会阻塞下面的代码执行）

​		XML是一种文件格式（我们可以把HTML理解为XML的一种）：可扩展的标记语言，它的作用是用自己扩展的一些语义标签来存储一些数据和内容，这样存储的好处是清晰的展示出数据的结构

​		很久以前，AJAX刚新起的时候，客户端从服务器端获取数据，服务器为了清晰的表达数据结构，都是返回XML格式的内容，当下，我们获取的数据一般都是JSON格式的内容，JSON相对于XML来说，也能清晰表达数据结构，而且访问里面数据的时候操作起来比XML更简便（但是现在项目中，服务器返回给客户端的数据不单纯是数据，而是数据和需要展示的结构拼接好的结果（类似于我们自己做的字符串拼接），换句话说，是服务器端把数据和结构拼接好返回给我们，此时返回的数据格式一般都是XML格式的字符串）

**全局刷新**

​		**原理：**页面是服务器端渲染的（前后端不分离项目），把数据和结构都在服务器端处理好，客户端只负责展示。

​		**好处：**

​		1.有利于SEO优化（服务器渲染好内容到客户端呈现，在页面源代码中可以看到被绑定的内容，有利于引擎的收录），但是客户端做字符串拼接，呈现出来的内容在页面源代码中是没有的，不利于SEO

##### 2.只要服务器端并发（抗压能力）给力，页面加载速度会比客户端渲染更快

​		很多的大网站首屏都是基于服务器端渲染的，客户端获取XML数据后直接呈现，增加页面第一次打开的速度，而剩下屏中的内容都是基于AJAX获取数据，在客户端进行数据拼接渲染的.....

​		不使用AJAX：首先向服务器端发送一个请求（通过浏览器地址栏问号传参发送给服务器），服务器获取请求后，验证是否存在，不管存不存在，都要给用户一个提示（原始展示的页面内容需要改变）服务器端把带提示的内容重新进行拼接，然后返回给客户端，客户端重新渲染最新的内容（只能页面整体刷新）

​		2.AJAX操作

```javascript
//创建AJAX实例：IE6中是不兼容的，使用的是new ActiveXObject来实现的
let xhr=new XMLHTTPRequest();
//打开请求：发送之前的一些配置项
/*1.HTTP METHOD 请求方式
 *	GET/DELETE/HEAD/OPTIONS/TRACE/CONNECT/POST/PUT
 *2.URL 向服务器端发送请求的API（Application Programming Interface）接口地址
 *3.ASYNC 设置AJAX请求的同步异步，默认是异步（写true也是异步），false是同步，项目中都是异步，防止阻塞后续代码执行
 *4.USER-NAME/USER-PASS 用户名密码，一般不用
*/
xhr.open([HTTP METHOD],[URL],[ASYNC],[USER-NAME],[USER-PASS]);
//事件监听：一般监听的都是ready-state-change 事件（ajax状态改变事件），基于这个事件可以获取服务器返回的响应头响应主体内容
xhr.onreadystatechange=()=>{
    if(xhr.readyState===4 && xhr.status===200){
        xhr.responseText;
    }
};
//发送AJAX请求：从这一步开始，当前AJAX任务开始，如果AJAX是同步的，后续代码不会执行，要等到AJAX状态成功后再执行，反之异步不会
xhr.send([请求主体内容]);	
```

##### 3.关于HTTP请求方式的一点学习

所有的请求都可以给服务器端传递内容，也都可以从服务器端获取内容

**GET**:从服务器端获取数据（给的少拿的多）

**POST**：向服务器端推送数据（给的多拿的少）

**DELETE**：删除服务器端的某些内容（一般是删除一些文件）

**PUT**：向服务器上存放一些内容（一般也是存放文件）

**HEAD**：只想获取服务器返回的响应头信息，不要响应主体中的内容

**OPTIONS**：一般使用它向服务器发送一个探测性请求，如果服务器端返回了信息，说明当前客户端和服务器端建立了连接，我们可以继续执行其它请求了（trace是干这件事的，但是axios这个AJAX类库在基于cross domain进行跨域请求的时候，就是先发送OPTIONS请求探测尝试，如果能连通服务器，才会继续发送其它的请求）

##### 4.GET VS POST

【传递给服务器信息的方式不一样】

​		GET是基于URL地址“问号传参”的方式把信息传递给服务器，POST是基于“请求主体”把信息传递给服务器

```javascript
[GET]
	xhr.open('GET','/tem/list?xx=xxx&xx=xxx')
[POST]
	xhr.send('xx=xxx&xx=xxx')
```

​		GET一般应用于拿（给服务器的会少一点），而POST给服务器的很多，如果POST是基于问号传参方式来搞会出现一些问题：URL拼接会很长，浏览器对于URL的长度有最大限度（谷歌8kb，火狐7kb，IE12kb.....），超过的部分浏览器就把它截掉了=>所以GET请求可以基于URL传参，而POST都是使用请求主体传递（请求主体理论上是没有限制的，真实项目中我们自己会做大小限制，防止上传过大信息导致请求迟迟不完成）

【GET不安全，POST相对安全】

​		因为GET是基于“问号传参”把信息传给服务器的，容易被黑客进行URL劫持，POST是基于请求主体传递的，相对来说不好劫持；所以登录，注册等涉及安全性的交互操作，我们都应该用post请求；

【GET会产生不可控制的缓存，POST不会】

​		不可控：不是想要就要，不想要就不要的，这是浏览器自主记忆的缓存，我们无法基于JS控制，真实项目中我们都会把这个缓存干掉；

​		GET请求产生缓存是因为连续多次向相同的地址（并且传递的信息参数也是相同的）发送请求，浏览器会把之前获取的数据从缓存中拿到返回，导致无法获取最新的数据（而POST不会）

​	解决方案：

```javascript
xhr.open('GET',`/temp/list?lx=1000&_=${Math.random()}`);
//保证每一次请求的地址不完全一样，在每一次请求的末尾追加一个随机数即可（使用_作为属性名就是不想和其它的属性名冲突）
```

##### 5.AJAX状态

0 =>unsent  		刚开始创建xhr，还没有发送

1 =>opened		已经执行了open的操作了

2 =>headers_received 	已经发送AJAX请求（AJAX任务开始），响应头信息已经被客户端接收

3 =>loading		响应主体内容正在返回

4 =>done			响应主体内容已经被客户端接收

**6.HTTP网络状态码status**

根据状态码能够清楚的反映出当前交互的结果以及原因

​	**200** OK 成功（只能证明服务器成功返回信息了，但是信息不一定是你业务需要的）

​	**301** Moved Permanently 永久转移（永久重定向）

​		=>域名更改，访问原始域名重定向到新的域名

​	**302** Move temporarily 临时转移（临时重定向）

​		=>网站现在是基于HTTPS协议运作的，如果访问的是HTTP协议，会基于307重定向到HTTPS协议上

​		=>302一般用作服务器负载均衡，当一台服务器达到最大并发数的时候，会把后续访问的用户临时转移到其他的服务器机组上处理

​		=>真实项目中会把所有的图片放到单独的服务器上“图片处理服务器”，这样减少主服务器的压力，当用户向主服务器访问图片的时候，主服务器都把它转移到图片服务器上处理

​	**304** Not Modified 设置缓存

​		=>对于不经常更新的资源文件，例如：CSS/JS/HTML/iMG等，服务器会结合客户端设置304缓存，第一次加载过这些资源就缓存到客户端了，下次在获取的时候，是从缓存中获取;如果资源更新了，服务器端会通过最后修改时间来强制让客户端从服务器重新拉取；基于Ctrl+F5强制刷新页面，304做的缓存就没有用了

​	**400** Bad request 		请求参数错误

​	**401** Unauthorized		无权限访问

​	**404** Not Found		找不到资源（地址不存在）

​	**413** request Entity Too Large	和服务器交互的内容资源超过服务器最大限制

​	**500** Internal Server Error 未知的服务器错误

​	**503** Service Unavailable 服务器超负荷

**7.关于xhr的属性和方法**

​	xhr.response 响应主体内容

​	xhr.responseText 响应主体的内容是字符串（JSON或者XML格式的字符串都可以）

​	xhr.responseXML 响应的主体内容是xml文档

​	xhr.status 返回的HTTP状态码

​	xhr.statusText 状态码的描述

​	xhr.timeout 设置请求超时的时间

​	xhr.abort() 强制中断AJAX请求

​	xhr.getResponseHeader([key]) 获取KEY对应的响应头信息，例如：xhr.getResponseHeader(‘data’) 获取响应头中的服务器时间

​	xhr.open()	打开URL请求

​	xhr.send() 	发送AJAX请求

​	xhr.setRequestHeader()	设置请求头

### 练习题

```javascript
let xhr= new XMLHttpRequest();
xhr.open('GET','/temp/list',true);//异步操作
xhr.onreadystatechange=()=>{    //=>监听的是AJAX状态‘改变’事件：设置监听之前有一个状态，当后续的状态和设置之前的状态不相同，才会触发这个事件
	if(xhr.readyState===2){
		console.log(1);
    }
    if(xhr.readyState==44){
        console.log(2);
    }
};
xhr.send(); //发送AJAX请求，这个执行才证明AJAX任务开始
console.log(3);
//输出3,1,2
```

===基于服务器时间做倒计时===

JS中也可以获取时间：“new Data()”获取的客户端时间，这个时间客户端可以任意修改，所以在项目中，如果时间作为重要的参考标准，需要使用服务器时间



### JQ中的AJAX方法

```javascript
$.ajax({
    url:'',
    method:'',
    data:,
    dataType:'json',
    async:true,
    cache:true,
    success:()=>{
    
	},
    error:()=>{
        
    }
})
```

**url**：请求的API接口地址

**method**：请求的方式

**data**：传输给服务器的信息可以放到data中

​	如果是GET请求基于问号传递过去

​	如果是POST请求是基于请求主体传递过去的

​	DATA的值可以是对象也可以是字符串（一般常用对象）

​		如果是对象类型，JQ会把对象转换为xxx=xxx&xxx=xxx的模式（x-www-form-urlencoded）

**dataType**：预设置获取结果的数据格式text、json、jsonp、html、script....（服务器返回给客户端的响应主体中的内容一般都是字符串【json格式居多】），而设置dataType=‘json’，JQ会内部把获取的字符串转换为json格式的对象=>‘他不会影响服务器的返回结果，只是把返回的结果进行了二次处理’

**async**：设置同步或者异步（true是异步，false是同步）

**cache**：设置GET请求下是否建立缓存（默认true建立缓存，false不建立缓存），当我们设置的是false，并且当前是get请求，JQ会在末尾追加随机数

**success**：回调函数，当ajax请求成功执行

**error**：请求失败后执行的回调函数



### promise

promise是ES6中新增加的内置类：目的是为了管理异步操作的

```javascript
new Promise((resolve,reject)=>{
    //resolve,reject:是任意执行的，但是大家都约定成功执行resolve，失败执行reject
    //Excutor函数（执行函数）中可以不管控异步操作（但是不管控异步操作没啥意义）
    resolve();
}).then(result=>{
    //resolve执行的时候会触发第一个回调函数执行
    console.log(1);
    return 100;//会把这个值传递给下一个then中的方法，如果返回的是一个新的promise实例，则等到promise处理完成，把处理完成的结果传递给下一个then
},reason=>{
    //reject执行的时候会触发第二个回调函数执行
    console.log(2);
}).then(result=>{//需要保证执行then方法返回的依旧是promise实例这样才可以实现链式调用
    //上一个then中管控的两个方法只要任何一个执行不报错，我们都会执行这个then中的第一个方法，如果执行报错，会执行此then中的第二个方法
}).catch(reson=>{
    //catch就相当于then(null,reason=>{})
});
//等待所有的promise都成功执行then，反之只要有一个失败就执行catch
Promise.all([primose1,...]).then();

```

- new Promise()创建类的一个实例，每一个实例都可以管理一个异步操作

	- 必须传递一个回调函数进去（回调函数中管理你的异步操作），不传递会报错

	-  回调函数中有两个参数

		- resolve：异步操作成功做的事情（代指成功后的事件队列=>成功后要做的所有事情都存放到成功这个事件队列中）

		- reject：异步操作失败做的事情（代指失败后的事情队列）

	- new Promise的时候立即把回调函数执行了（Promise是同步的）

- 基于Promise.prototype.then方法（还有catch、finally两个方法）向成功队列和失败队列中依次加入需要处理的事情

```javascript
let promise1 = new Promise((resolve,reject)=>{
   $.ajax({
       url: 'json/data.json',
       success:(result)=>{
           resolve(result);
       },
       error:(msg)=>{
           reject(msg);
       }
   }); 
});
promise1.then(
	result=>{
	
    },
    msg=>{
        
    }
);

```



​		