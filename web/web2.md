# Web-基础知识篇

## 基础名词

一些名词：

1. 浏览器：Web Browser

2. Web服务器：Web Server

3. WWW（万维网）: （英语：World Wide Web）亦作**WWW**、**Web**，是一个透过互联网访问的，由许多互相链接的超文本组成的系统[[1\]](https://zh.wikipedia.org/wiki/万维网)

4. HTML：超文本标记语言

   - 进入`www.baidu.com`按下F12所看到的场景

5. URL：统一资源定位符

   - `<scheme>://<user>:<password>@<host>:<port>/<path>;<params>?<query>#<flag>`
   - 方案://用户名:密码@主机号:端口/服务器上资源的路径;参数?查询#片段
   - `ftp://joe:joepasswd@ftp.prep.edu/pub/name`
   - 在ctf中url中通常会暗藏玄机，接下来同学们可以在冲浪的时候多看一些有趣的url，试着分析它的具体含义~

6. 路径：

   - 绝对路径：完整的地址 如`www.example.com/home/home.php`

   - 相对路径：指目标相对于当前文件的路径 

     ```
     ./ 指文件本身所在的目录（可省略）
     ../ 指文件所在的父级目录（可以叠加）
     / 指文件所在的根目录，无论哪个层级都可以使用/单斜杠直接访问根目录
     
     例如，目录结构为:
     /
     /home /tmp
     /home/home.php
     
     当前目录下的home.php ./home.php
     当前目录为home，向上一层则为 ../ => /
     ```

7. IP：网际互连协议，IP地址为网际协议地址，它为互联网上的每一个网络和每一台主机分配一个逻辑地址，以此来屏蔽物理地址的差异。

   - 常见的内网IP：

     ```
     10.0.0.0~10.255.255.255（A类）
     172.16.0.0~172.31.255.255（B类）
     192.168.0.0~192.168.255.255（C类）
     ```

   - 回环地址：127.0.0.1
   - 查询本机的IP地址，Windows下使用`ipconfig`，Linux下使用`ifconfig`
   - ping测试：打开电脑的终端ping一个喜欢的地址看看效果吧~

8. 域名：一串用点分隔的名字组成的，Internet上某一台计算机或计算机组的名称。

   - 为了补足IP地址不方便记忆并且不能显示地址组织的名称和性质的缺点
   - 即域名更好记，例如`www.baidu.com`一看就知道是百度家的

9. 编码：例如base64编码，可以解决一些传输方面的问题，经常被用于URL、Cookie等地方。

   - base64是由`A-Z、a-z、0-9、+、/`组成，`=`通常出现在末尾作为填充字符
   - 可以尝试去解码：`ZmxhZ3tiYXNlNjRfaXNfb2shfQ==`

## HTTP协议

- 一个熟悉的url：`http://www.example.com:80/path/to/index.html?key1=value1&key2=value2#Somewhere`

- 请求方法：GET/POST/PUT/HEAD/DELETE等等

- 常见的状态码：

  - 1xx 信息提示，表示请求已经被成功接收
- 2xx 成功 例：200 OK
  - 3xx 重定向 例：302(重定向) 
- 4xx 客户端错误 例：404(请求资源不存在) 403(服务器拒绝服务) 
  - 5xx 服务端错误 例：500(服务器内部错误)


协议之所以称之为协议，是因为其是由已经协商好的，也可以称为一种规范，用这样规范的形式作交互，即

一个简易的图片说明前后端的关系：![image-20210903101806759](http://image-teach.test.upcdn.net/basic/http.png)

打开熟悉的Burp，利用Burp截包可以看到报文的内容

![image-20210903101913575](http://image-teach.test.upcdn.net/basic/image-20210903101913575.png)

请求报文**Request**的格式：

```
Method URI Version
Key1: Value1
Key2: Value2
...
KeyN: ValueN

<RequestBody>
```

响应报文**Response**的格式

```
Version Status StatusText
Key1: Value1
Key2: Value2
....
KeyN: ValueN

<ResponseBody>
```

## 前端语言

> HTML 负责整体的框架以及内容部分，HTML5可以负责一部分的数据验证
> CSS 负责美化网页，部分特效
> JS 负责数据验证、数据交互 特效

### HTML

超文本标记语言，即赋予文本更丰富的含义

基本框架：

```html
<!DOCTYPE html>
<html>
<head>
	<title>	</title>
</head>
<body>

</body>
</html>
```

我们可以看到这里都是双标签，即需要前后两个标签来包住内容，同时也有单标签，可以单个使用

现在可以打开电脑的编辑器，例如记事本，输入

```html
<!DOCTYPE html>
<html>
<head>
	<title>I love Web</title>
</head>
<body>
	<h1>Web is funny~</h1>
	<div>
		name:
		<input type="text" style="name">
	</div>
	<br>
	<div>
    	age：<input type="number" name="age" min="0" max="100"  step="1"/>
	</div>
	<br>
	<div>出生日期
    	<input type="datetime-local" name="date" />
	</div>
</body>
</html>
```

保存后将其后缀改为`html`，例如命名为`test.html`，后用浏览器打开即可看到内容。

对于html的语法不需要死记硬背，有需要的时候到网上查阅即可。

### CSS

层叠样式表，用于定义网页的样式，配合html打造好看的页面。

```html
<html>
<head>
	<title>Test</title>
	<style>
		.button{
			color: red;
		}

	</style>
</head>
<body>
	<input type="submit" class="button">
</body>
</html>
```

这里可以任意改一下color的位置，或是随便添加一些其他的属性，感受一下意思就行。

### Javascript

引用外部的js文件

```
<script src="test.js" type="text/javascript" charset="utf-8">
```

为按钮加入点击事件

```
<button onclick="alert('Click!')">Click Here!</button>
```

弹框

```
<script>alert(1);</sctipt>
```

可以在浏览器的控制台进行简单实验

```
console.log(123)
alert(123)
```

## 后端语言

目标：

- 常见后端语言：php、python、java、nodejs

- 以及一些常见的框架

入门的题目通常用php写，会涉及一些php的特性，在熟悉之后也要去收集一些

## 数据库

常见的数据库软件：

- 关系型：MySQL、MariaDB、SQL Server
- 非关系型：PostgreSQL、MongoDB

结构：

数据库 - 表 - 列 - 字段 

一些基本的语句

```sql
show databaes; # 显示所有数据库
show tables; # 显示所有表
select * from users where name='noname'; #查询users表下name为noname的记录
```

## 几道例题

那么讲述了最简单的这些内容，请打开bugku平台完成一些简单题目吧，先思考过后再查看题解

```
1.滑稽
2.GET
3.POST
4.你必须让他停下
5.源代码
6.你从哪里来
```

