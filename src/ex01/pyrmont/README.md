第一章
1，Web服务器也称为超文本传输协议(HTTP)服务器，因为它使用HTTP来跟客户端进行通信的，这通常是个web浏览器。

2，一个基于java的web服务器使用两个重要的类：java.net.Socket和java.net.ServerSocket，并通过HTTP消息进行通信。

一、超文本传输协议(HTTP)
1，HTTP是一种协议，允许web服务器和浏览器通过互联网进行发送和接受数据。

2，它是一种请求和响应协议。

3，客户端请求一个资源而服务器响应请求。

4，HTTP使用可靠的TCP连接--TCP默认使用80端口。

5，在HTTP中，始终都是客户端通过建立连接和发送一个HTTP请求从而开启一个事务。web服务器不需要联系客户端或者对客户端做一个回调连接。

6，无论是客户端或者服务器都可以提前终止连接。举例来说，当你正在使用一个web浏览器的时候，可以通过点击浏览器上的停止按钮来停止一个文件的下载进程，从而有效的关闭与web服务器的HTTP连接。

二、HTTP请求
1，一个HTTP请求包括三个组成部分：
方法，统一资源标识符(URI)，协议/版本
请求的头部
主体内容

2，下面是一个HTTP请求的例子：
POST /examples/default.jsp HTTP/1.1
Accept: text/plain; text/html
Accept-Language: en-gb
Connection: Keep-Alive
Host: localhost
User-Agent: Mozilla/4.0 (compatible; MSIE 4.01; Windows 98)
Content-Length: 33
Content-Type: application/x-www-form-urlencoded
Accept-Encoding: gzip, deflate

lastName=Franks&firstName=Michael

3，方法，统一资源标识符(URI)，协议/版本出现在请求的第一行。
        POST /examples/default.jsp HTTP/1.1

4，这里POST是请求方法，/examples/default.jsp是URI，而HTTP/1.1是协议/版本部分。

5，每个HTTP请求可以使用HTTP标准里边提到的多种方法之一。HTTP 1.1支持7种类型的请求：GET, POST, HEAD, OPTIONS, PUT, DELETE和TRACE。

6，GET和POST在互联网应用里边最普遍使用的。

7，URI（统一资源标识符）完全指明了一个互联网资源。URI通常是相对服务器的根目录解释的。因此，始终以斜线"/"开头。

8，URL（统一资源定位符)其实是一种URI。

9，协议版本代表了正在使用的HTTP协议的版本。

10，请求的头部包含了关于客户端环境和请求的主体内容的有用信息。例如它可能包括浏览器设置的语言，主体内容的长度等等。

11，每个头部通过一个回车换行符(CRLF)来分隔的。

12，对于HTTP请求格式来说，头部和主体内容之间有一个回车换行符(CRLF)是相当重要的。CRLF告诉HTTP服务器主体内容是在什么地方开始的。

三、HTTP响应
1，类似于HTTP请求，一个HTTP响应也包括三个组成部分：
状态行
响应的头部
主体内容

2，下面是一个HTTP响应的例子：
HTTP/1.1 200 OK
Server: Microsoft-IIS/4.0
Date: Mon, 5 Jan 2004 13:13:33 GMT
Content-Type: text/html
Last-Modified: Mon, 5 Jan 2004 13:13:12 GMT
Content-Length: 112

<html>
    <head>
        <title>HTTP Response Example</title>
    </head>
    <body> Welcome to Brainy Software </body>
</html>

3，响应头部的第一行类似于请求头部的第一行。第一行告诉你该协议使用HTTP 1.1，请求成功(200=成功)，表示一切都运行良好。

4，响应头部和请求头部类似，也包括很多有用的信息。

5，响应的主体内容是响应本身的HTML内容。

6，头部和主体内容通过回车换行(CRLF)分隔开来。

四、Socket类
1，套接字是网络连接的一个端点。

2，套接字使得一个应用可以从网络中读取和写入数据。

3，两个不同计算机上的两个应用可以通过套接字连接发送和接受字节流。

4，在Java里边，套接字指的是java.net.Socket类。

5，要创建一个套接字，你可以使用Socket类众多构造方法中的一个。其中一个接收主机名称和端口号：
        public Socket (java.lang.String host, int port)

6，在这里主机是指远程机器名称或者IP地址，端口是指远程应用的端口号。例如，要连接yahoo.com的80端口，你需要构造以下的Socket对象：
        new Socket ("yahoo.com", 80);

7，一旦你成功创建了一个Socket类的实例，你可以使用它来发送和接受字节流。要发送字节流，你首先必须调用Socket类的getOutputStream方法来获取一个java.io.OutputStream对象。

8，要发送文本到一个远程应用，你经常要从返回的OutputStream对象中构造一个java.io.PrintWriter对象。

9，要从连接的另一端接受字节流，你可以调用Socket类的getInputStream方法用来返回一个java.io.InputStream对象。

10，为了从web服务器获取适当的响应，需要发送一个遵守HTTP协议的HTTP请求。


五、ServerSocket类
1，Socket类代表一个客户端套接字，即任何时候你想连接到一个远程服务器应用的时候你构造的套接字。

2，假如实施一个服务器应用，例如一个HTTP服务器或者FTP服务器，就需要一种不同的做法。这是因为服务器必须随时待命，因为它不知道一个客户端应用什么时候会尝试去连接它。

3，为了让你应用能随时待命，你需要使用java.net.ServerSocket类。这是服务器套接字的实现。

4，ServerSocket和Socket不同，服务器套接字的角色是等待来自客户端的连接请求。

5，一旦服务器套接字获得一个连接请求，它创建一个Socket实例来与客户端进行通信。

6，要创建一个服务器套接字，需要使用ServerSocket类提供的四个构造方法中的一个。需要指定IP地址和服务器套接字将要进行监听的端口号。

7，通常，IP地址将会是127.0.0.1，也就是说，服务器套接字将会监听本地机器。服务器套接字正在监听的IP地址被称为是绑定地址。

8，服务器套接字的另一个重要的属性是backlog，这是服务器套接字开始拒绝传入的请求之前，传入的连接请求的最大队列长度。

9，其中一个ServerSocket类的构造方法如下所示：
        public ServerSocket(int port, int backLog, InetAddress bindingAddress);

10，对于这个构造方法，绑定地址必须是java.net.InetAddress的一个实例。一种构造InetAddress对象的简单的方法是调用它的静态方法getByName，传入一个包含主机名称的字符串，就像下面的代码一样：
        InetAddress.getByName("127.0.0.1");

11，一旦你有一个ServerSocket实例，你可以让它在绑定地址和服务器套接字正在监听的端口上等待传入的连接请求。你可以通过调用ServerSocket类的accept方法做到这点。这个方法只会在有连接请求时才会返回，并且返回值是一个Socket类的实例。