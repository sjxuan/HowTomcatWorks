# HowTomcatWorks
本项目用于学习howtomcatworks这本书的内容，并且跟随书中的项目示例同步学习！

servlet容器是一个复杂的系统。不过，一个servlet容器要为一个servlet的请求提供服务，基本上有三件事要做：

1，创建一个request对象并填充那些有可能被所引用的servlet使用的信息，如参数、头部、cookies、查询字符串、URI等等。一个request对象是javax.servlet.ServletRequest接口的一个实例。

2，创建一个response对象，所引用的servlet使用它来给客户端发送响应。一个response对象javax.servlet.ServletResponse接口的一个实例。

3，调用servlet的service方法，并传入request和response对象。在这里servlet会从request对象取值，给response写值。
