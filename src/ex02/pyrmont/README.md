一、javax.servlet.Servlet接口
1，Servlet编程是通过javax.servlet和javax.servlet.http这两个包的类和接口来实现的。其中一个至关重要的就是javax.servlet.Servlet接口。所有的servlet必须实现或者继承实现该接口的类。

2，Servlet接口有五个方法，其用法如下：
public void init(ServletConfig config) throws ServletException;
public void service(ServletRequest request, ServletResponse response) throws ServletException, java.io.IOException;
public void destroy();
public ServletConfig getServletConfig();
public java.lang.String getServletInfo();

3，在Servlet的五个方法中，init，service和destroy是servlet的生命周期方法。

4，在servlet类已经初始化之后，init方法将会被servlet容器所调用。servlet容器只调用一次init方法，以此表明servlet已经被加载进服务中。

5，init方法必须在servlet可以接受任何请求之前成功运行完毕。一个servlet程序员可以通过覆盖这个方法来写那些仅仅只要运行一次的初始化代码，例如加载数据库驱动，值初始化等等。在其他情况下，这个方法通常是留空的。

6，servlet容器为servlet请求调用servlet的service方法。

7，servlet容器传递一个javax.servlet.ServletRequest对象和javax.servlet.ServletResponse对象。ServletRequest对象包括客户端的HTTP请求信息，而ServletResponse对象封装servlet的响应。

8，在servlet的生命周期中，service方法将会给调用多次。

9，当从服务中移除一个servlet实例的时候，servlet容器调用destroy方法。这通常发生在servlet容器正在被关闭或者servlet容器需要一些空闲内存的时候。

10，仅仅在所有servlet线程(注：详情见本章附录)的service方法已经退出或者超时淘汰的时候，destroy方法才被调用。在servlet容器已经调用完destroy方法之后，在同一个servlet里边将不会再调用service方法。

11，destroy方法提供了一个机会来清理任何已经被占用的资源，例如内存，文件句柄和线程，并确保任何持久化状态和servlet的内存当前状态是同步的。

二、servlet容器处理servlet的http请求
1，总的来说，一个全功能的servlet容器会为servlet的每个HTTP请求做下面一些工作：
当第一次调用servlet的时候，加载该servlet类并调用servlet的init方法(仅仅一次)。
对每次请求，构造一个javax.servlet.ServletRequest实例和一个javax.servlet.ServletResponse实例。
调用servlet的service方法，同时传递ServletRequest和ServletResponse对象。
当servlet类被关闭的时候，调用servlet的destroy方法并卸载servlet类。

三、程序知识点
1，java.io.PrintWriter构造方法：
        java.io.PrintWriter writer = new java.io.PrintWriter(java.io.OutputStream output,boolean flag);
PrintWriter类的构造方法的第二个参数是一个布尔值表明是否允许自动刷新。传递true作为第二个参数将会使任何println方法的调用都会刷新输出(output)。不过，print方法不会刷新输出。

2，要加载servlet，你可以使用java.net.URLClassLoader类，它是java.lang.ClassLoader类的一个直接子类。一旦你拥有一个URLClassLoader实例，你使用它的loadClass方法去加载一个servlet类。这个类有三个构造方法，其中最简单的是：
        public URLClassLoader(URL[] urls);

3，这里urls是一个java.net.URL的对象数组，这些对象指向了加载类时候查找的位置。任何以/结尾的URL都假设是一个目录。

4，在一个servlet容器里边，一个类加载器可以找到servlet的地方被称为资源库(repository）。

5，URL类提供了一系列的构造方法，所以有很多中构造一个URL对象的方式。使用Tomcat中的另一个类的相同的构造方法。这个构造方法如下所示：
        public URL(URL context, java.lang.String spec, URLStreamHandler hander) throws MalformedURLException；
你可以使用这个构造方法，并为第二个参数传递一个说明，为第一个和第三个参数都传递null。不过，这里有另外一个接受三个参数的构造方法：
        public URL(java.lang.String protocol, java.lang.String host, java.lang.String file) throws MalformedURLException；
因此，假如你使用下面的代码时，编译器将不会知道你指的是那个构造方法：
        new URL(null, aString, null);
你可以通过告诉编译器第三个参数的类型来避开这个问题，例如：
        URLStreamHandler streamHandler = null;
        new URL(null, aString, streamHandler);
你可以使用下面的代码在组成一个包含资源库(servlet类可以被找到的地方)的字符串，并作为第二个参数，
        String repository = (new URL("file", null, classPath.getCanonicalPath() + File.separator)).toString() ;
把所有的片段组合在一起，这就是用来构造适当的URLClassLoader实例的process方法中的一部分：
        URL[] urls = new URL[1];
        URLStreamHandler streamHandler = null;
        File classPath = new File(Constants.WEB_ROOT);
        String repository = (new URL("file", null, classPath.getCanonicalPath() + File.separator)).toString() ;
        urls[0] = new URL(null, repository, streamHandler);
        loader = new URLClassLoader(urls);

6，当有了一个类加载器，你可以使用loadClass方法加载一个servlet：
        Class myClass = loader.loadClass(servletName);




附录：
servlet多线程
一，servlet容器如何同时处理多个请求。
        servlet采用多线程来处理多个请求同时访问，Servelet容器维护了一个线程池来服务请求。
        线程池实际上是等待执行代码的一组线程叫做工作者线程(Worker Thread)，Servlet容器使用一个调度线程来管理工作者线程(Dispatcher Thread)。
        当容器收到一个访问Servlet的请求，调度者线程从线程池中选出一个工作者线程，将请求传递给该线程，然后由该线程来执行Servlet的service方法。
        当这个线程正在执行的时候，容器收到另外一个请求，调度者线程将从池中选出另外一个工作者线程来服务新的请求，容器并不关心这个请求是否访问的是同一个Servlet还是另外一个Servlet。
        当容器同时收到对同一Servlet的多个请求，那这个Servlet的service方法将在多线程中并发的执行。
二，Servlet容器默认采用单实例多线程的方式来处理请求
        这样减少产生Servlet实例的开销，提升了对请求的响应时间。对于Tomcat可以在server.xml中通过<Connector>元素设置线程池中线程的数目。
        就实现来说：调度者线程类所担负的责任如其名字，该类的责任是调度线程，只需要利用自己的属性完成自己的责任。所以该类是承担了责任的，并且该类的责任又集中到唯一的单体对象中。
        而其他对象又依赖于该特定对象所承担的责任，我们就需要得到该特定对象。那该类就是一个单例模式的实现了。
三，如何开发线程安全的Servlet
1，变量的线程安全：这里的变量指字段和共享数据(如表单参数值)。
a，将 参数变量 本地化。多线程并不共享局部变量.所以我们要尽可能的在servlet中使用局部变量。
        例如：
        String user = "";
        user = request.getParameter("user");
b，使用同步块Synchronized，防止可能异步调用的代码块。这意味着线程需要排队处理。
        在使用同板块的时候要尽可能的缩小同步代码的范围，不要直接在sevice方法和响应方法上使用同步，这样会严重影响性能。
2,属性的线程安全：ServletContext，HttpSession，ServletRequest对象中属性
ServletContext：（线程是不安全的）
        ServletContext是可以多线程同时读/写属性的，线程是不安全的。要对属性的读写进行同步处理或者进行深度Clone()。
        所以在Servlet上下文中尽可能少量保存会被修改（写）的数据，可以采取其他方式在多个Servlet中共享，比方我们可以使用单例模式来处理共享数据。
HttpSession：（线程是不安全的）
        HttpSession对象在用户会话期间存在，只能在处理属于同一个Session的请求的线程中被访问，因此Session对象的属性访问理论上是线程安全的。
        当用户打开多个同属于一个进程的浏览器窗口，在这些窗口的访问属于同一个Session，会出现多次请求，需要多个工作线程来处理请求，可能造成同时多线程读写属性。
        这时我们需要对属性的读写进行同步处理：使用同步块Synchronized和使用读/写器来解决。
ServletRequest：（线程是安全的）
        对于每一个请求，由一个工作线程来执行，都会创建有一个新的ServletRequest对象，所以ServletRequest对象只能在一个线程中被访问。ServletRequest是线程安全的。
        注意：ServletRequest对象在service方法的范围内是有效的，不要试图在service方法结束后仍然保存请求对象的引用。
3，使用同步的集合类：
        使用Vector代替ArrayList，使用Hashtable代替HashMap。
4，不要在Servlet中创建自己的线程来完成某个功能。
        Servlet本身就是多线程的，在Servlet中再创建线程，将导致执行情况复杂化，出现多线程安全问题。
5，在多个servlet中对外部对象(比方文件)进行修改操作一定要加锁，做到互斥的访问。
四，SingleThreadModel接口
        javax.servlet.SingleThreadModel接口是一个标识接口，如果一个Servlet实现了这个接口，那Servlet容器将保证在一个时刻仅有一个线程可以在给定的servlet实例的service方法中执行。将其他所有请求进行排队。
        服务器可以使用多个实例来处理请求，代替单个实例的请求排队带来的效益问题。服务器创建一个Servlet类的多个Servlet实例组成的实例池，对于每个请求分配Servlet实例进行响应处理，之后放回到实例池中等待下次请求。这样就解决了并发访问的问题。
        此时,局部变量(字段)也是安全的，但对于全局变量和共享数据是不安全的，需要进行同步处理。而对于这样多实例的情况SingleThreadModel接口并不能解决并发访问问题。
        SingleThreadModel接口在servlet规范中已经被废弃了。