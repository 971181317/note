## web回顾

* 软件架构
  1. c/s
  2. b/s
  
* 资源分类
  1. 静态资源：所有用户访问后，得到的结果是一样的，称为静态资源，静态资源可以直接被浏览器解析

     * 如：html、css、JavaScript
  2. 动态资源：每个用户访问相同资源后，得到的结果可能不一样。称为动态资源，动态资源被访问后，需要先转换为静态资源，再返回给浏览器

     * 如：servlet/jsp，php，asp

* 网络通信三要素

  1. ip：电子设备在网络中的唯一标识
  2. 端口号：应用程序在计算机的唯一标识。0-65536
  3. 传输协议：规定了数据传输的规则

## web服务器软件

* 服务器：安装了服务器软件的计算机
* 服务器软件：接受用户的请求，处理请求，做出响应
* web服务器软件：接受用户的请求，处理请求，做出响应

  在web服务器中，可以部署web项目，让用户通过浏览器来访问这些项目，有时称为web容器
  
* 常见java相关的web服务器软件

  1. webLogic：oracle公司，大型javaee服务器，支持所有的javaee规范，收费
  2. webSphere：IBM公司，大型javaee服务器，支持所有的javaee规范，收费
  3. JBOSS：JBOSS公司，大型javaee服务器，支持所有的javaee规范，收费
  4. Tomcat：Apache基金组织，中小型javaee服务器，仅仅支持少量的javaee规范servlet/jsp。开源、免费

* javaEE：java语言在企业级开发中使用的级数规范的总和，一共规定了了13项大的规范
* Tomcat：web服务器软件

  1. 下载：https://tomcat.apache.org/
  2. 安装：解压压缩包，目录建议没有中文
  3. 卸载：删除目录
  4. 启动

     * bin/start.bat，运行文件
     * 访问：浏览器输入http://localhost:8080/访问
     * 一般会将tomcat的默认端口号修改为80。80端口号是http协议的默认端口号
       * 好处：在访问时不用输入端口号
  5. 关闭：

     1. 正常关闭：bin/shut.bat或者ctrl+c
     2. 强制关闭：点击启动窗口
  6. 配置
  
     * 部署项目的方式

       1. 直接项目放到webapps目录项即可

          简化部署：将项目打包成一个war包，再将war包放置到webapps目录下，war会自动解压缩

       2. 配置conf/server.xml：在\<Host>标签体中配置

          `<Contex docBase="存放路径" path="虚拟路径"/>`

       3. 在conf\Catalina\localhost创建任意名称的`xml`文件,在文件中编写`<Contex docBase="存放路径"/>`
     
          * 虚拟目录名称：xml文件名称
     
     * 静态项目和动态项目：
     
       * 目录结构

## Servlet：server applet

* 概念：运行在服务器端的小程序

  servlet就是一个接口，定义了Java类被浏览器访问到（tomcat识别）的规则
  将来我们自定义一个类，实现servlet接口，复写方法
  
* 快速入门
  
  1. 创建JavaEE项目
  2. 定义一个类，实现Servlet接口
  3. 实现接口中的抽象方法
  4. 配置Servlet：在web.xml

```xml
<!--配置Servlet-->
<servlet>
    <servlet-name>demo1</servlet-name>
    <servlet-class>servlet.ServletDemo1</servlet-class>
</servlet>

<servlet-mapping>
    <servlet-name>demo1</servlet-name>
    <url-pattern>/demo1</url-pattern>
</servlet-mapping>
```

* 执行原理

  1. 当服务器接收到客户端浏览器的请求后，会解析URL路径，获取访问的Servlet的资源路径
  2. 查找web.xml。是否有对应的\<url-pattern>标签体内容
  3. 如果有，则找到对应的\<servlet-class>全类名
  4. tomcat会将字节码文件加载进内存，并且创建其对象
  5. 调用其方法

* Servlet中的生命周期：

  1. 被创建：执行`init`方法，只执行一次

     * Servlet什么时候创建

       默认情况下，第一次访问时，被创建
       
     * Servlet的init方法只执行一次，说明Servlet在内存中只存在一个对象，Servlet是单例的
       多个用户访问时可能存在线程安全问题，解决：尽量不要在Servlet中定义成员变量，及时定义了成员变量，也不要修改值
       
```xml
<servlet>
    <servlet-name>demo2</servlet-name>
    <servlet-class>servlet.ServletDemo2</servlet-class>
    <!--指定Servlet的创建时间：<load-on-startup>
    1. 第一次访问时被创建，值为负数
    2. 服务器启动时被创建，值为0或正整数
    -->
    <load-on-startup>5</load-on-startup>
</servlet>
```


  2. 提供服务：执行`service`方法，执行多次
  3. 被销毁：执行`destroy`方法，执行一次

     * Servlet被销毁时执行。服务器关闭时，Servlet被销毁
     * 只有服务器正常关闭时，才会执行destroy
     * destroy方法在Servlet销毁之前执行

* Servlet3.0
  * 支持注解配置，可以不使用`Web.xml`
  * 步骤：
    1. 创建项目，选择Servlet的版本3.0以上
    2. 定义Servlet类，实现Servlet接口
    3. 复写方法
    4. 在类上@WebServlet注解配置：`@WebServlet(urlPatterns = "资源路径")`

* 体系结构
  
  * `Servlet`接口
  * `GenericServlet`抽象类：将`Servlet`接口中其他方法做了默认空实现，只将`service()`方法作为抽象，将来定义`Servlet`类时，可以继承`GenericServlet`。实现`service()`即可
  * `HttpServlet`抽象类：对Http协议一种封装，简化操作
    1. 定义类继承`HttpServlet`
    2. 复写`doGet()`/`dePost()`方法

* Servlet 相关配置

  1. `urlpartten`：`Servlet`访问路径
     1. 一个`Servlet`可以定义多个访问路径`@WebServlet({"/d4", "/dd4", "/ddd4"})`
     2. 路径定义规则：`/xxx`，` /xxx/xxx`，`*.xxx`任意.xxx都可以访问

## IDEA与tomcat的相关配置

1. IDEA会为每一个tomcat部署的项目建立一份配置文件
2. 工作空间项目 和 tomcat部署的项目

   * tomcat真正访问的是“tomcat部署的web项目”，“tomcat部署的web项目”对应着“工作空间项目”的web目录项的所有资源
   * WEB-INF下的资源不能直接被浏览器直接访问
   * 断点调试

## HTTP
1. 概念：Hyper Text Transfer Protol超文本传输协议

   * 传输协议：定义了客户端和服务器通信时，发送数据的格式
   * 特点：
     
     1. 基于TCP/IP的高级协议
     2. 默认端口号是80
     3. 基于请求/响应模型的：一次请求对应一次响应
     4. 无状态协议：每次请求之间相互独立，不能交互数据

   * 历史版本
     1. 1.0：每一次请求都会新建连接
     2. 1.1：复用连接

2. 请求消息数据格式

   1. 请求行
      `请求方式 请求url 请求协议/版本`
      `GET /login.html HTTP/1.1`
      HTTP协议有7种请求方式，常用的有两种
      
      GET：参数在请求行中，在url之后，请求的url有长度限制，不太安全
      POST：参数在请求体中，请求的url没有长度限制，相对安全
   2. 请求头
      `请求头名称：请求头值`
      常用请求头
      1. User-Agent：浏览器版本信息
      2. Referer：告诉服务器从哪里来。作用：防盗链，统计 
   3. 请求空行
      空行，就是用于分割POST的请求头和请求体的
   4. 请求体（正文）
      封装POST请求消息的请求参数
```
POST /demo3 HTTP/1.1
Host: localhost
Connection: keep-alive
Content-Length: 27
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
Origin: http://localhost
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/86.0.4240.75 Safari/537.36 Edg/86.0.622.38
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: http://localhost/login.html
Accept-Encoding: gzip, deflate, br
Accept-Language: zh-CN,zh;q=0.9,en;q=0.8,en-GB;q=0.7,en-US;q=0.6
Cookie: Idea-55747dda=712f28e4-eb37-44f3-9fe8-8aa299a57e07; JSESSIONID=D1A42BEEB1D46CC4E4FB829CE5C336FE

username=%E5%BC%A0%E4%B8%89
```
3. 响应消息数据格式

   1. 响应行
      1. 组成：协议/版本 响应状态码 状态码描述 
      2. 响应状态码：服务器告诉客户端浏览器本次请求和响应的一个状态
         * 状态码都是三位数
         * 分类
           1. 1xx：服务器就收到客户端消息，但没有接收完成，等待一段时间后，发送1xx状态码
           2. 2xx：成功。代表：200
           3. 3xx：重定向。代表：302(重定向)，304(访问缓存)
           4. 4xx：客户端错误。代表：404(请求路径没有对应资源)， 405(请求方式没有对应的doXxx方法)
           5. 5xx：服务器端错误。代表：500(服务器内部出现异常)
   2. 响应头
      1. 格式：头名称：值
      2. 常用的响应头：
         * `Content-Type`：服务器告诉客户端本次响应消息体数据格式及编码格式
         * `Content-disposition`服务器告诉客户端一什么格式打开响应体数据
           * 默认值：in-line在当当前页面内打开
           * attachment;filename=xxx：以附件形式打开响应体。文件下载
   3. 响应空行
   4. 响应体:传输的数据
   
```
HTTP/1.1 200 
Content-Type: text/html;charset=UTF-8
Content-Length: 90
Date: Sat, 24 Oct 2020 07:18:08 GMT
Keep-Alive: timeout=20
Connection: keep-alive
   
<html>
  <head>
    <title>$Title$</title>
  </head>
  <body>
$END$
</body>
</html>
```

   

## request

1. request和response对象的原理
   1. request和response对象是由服务器创建的。我们来使用
   2. request对象是来获取请求消息，response对象是来设置响应消息的、

2. request对象继承体系结构：
   ServletRequest -- 接口
   HttpServletRequest -- 接口（继承）
   org.apache.catalina.connector.RequestFacade -- 类（tomcat实现）

3. request功能：
   1. 获取请求消息
      1. 获取请求头数据

         `GET /虚拟目录/demo3?name=zhangsan HTTP/1.1`
         1. 获取请求方式：GET  `String getMethod()`
         2. 获取虚拟目录：/虚拟目录  `String getContextPath()`
         3. 获取Servlet路径：/demo3 `String getServletPath()`
         4. 获取get方式的请求参数： name=zhangsan `String getQueryString()`
         5. 获取请求的uri：
            1. `String getRequestURI`:/虚拟目录/demo3
            2. `StringBuffer getRequestURL`:http://localhost/虚拟目录/demo3
            
            * URL：统一资源名定位符
            * URI：统一资源标识符
         6. 获取协议及版本：HTTP/1.1  `getProtocol()`
         7. 获取客户机的IP地址：`String getRemoteAddr()`
         
      2. 获取请求行数据
         1. `String getHeader(String name)`通过请求头名称获取请求头的值
         2. `Enumeration<String> getHeaderNames()  `获取所有的请求头名称
         
      3. 获取请求体数据
      
         * 请求体：只有POST请求方式，才有请求体，在请求体中封装了POST请求的请求参数
         * 步骤
           1. 获取流对象
              *  `BufferedReader getReader()`：获取字符输入流，只能操作字符数据
              *  `ServletInputStream getInputStream()`：获取字节输入流，可以操作所有类型数据
           2. 再从流对象中拿去数据
      
   2. 其他功能
   
      1. 获取请求参数的通用方式：无论get还是post都可以使用
   
         1. `String getParameter(String name)`：根据参数名称获取参数值
         2. `String[] getParameterValue(String name)`：根据参数名称获取参数值数组
         3. `Enumeration<String> getParameterNames(String name)`：获取所有请求的参数名称
         4. `Map<String, String[]> getParameterMap()`获取所以参数的map集合
   
         * 中文乱码问题
   
           get：在tomcat8已经解决
   
           post：或乱码
   
      2. 请求转发：一种在服务器内部的资源跳转方式
   
         1. 步骤：
            1. 通过`request`对象获取请求转发器对象：`RequestDispatcher getRequestDispatcher(String path)`
            2. 使用`RequestDispatcher`对象来进行转发：forward(ServletRequest request, ServletResponse response) 
         2. 特点：
         1. 浏览器地址栏没有发生变化
            2. 只能转发到当前服务器内部资源
         3. 转发是一次请求
      
      3. 共享数据
      
         * 域对象：一个有作用范围的对象，可以在范围内共享数据
         * request域：代表一次请求的范围，一般用于请求转发的多个资源中共享数据
         * 方法：
           1. `void setAttribute(String name,Object obj)`:存储数据
           2. `Object getAttitude(String name)`:通过键获取值
           3. `void removeAttribute(String name)`:通过键移除键值对
      
      4. 获取ServletContext:
      
         * `ServletContext getServletContext()`

## JavaBean

标准的Java；类 

1. 类必须被`public`修饰
2. 提供空参的构造器
3. 成员变量必须使用`private`修饰
4. 提供公共的setter/getter方法

功能：封装数据

### BeaUtils工具类：简化数据封装

1. 方法
   1. `setProperty()`：设置属性
   2. `getProperty()`：获取属性
   3. `populate(Object obj, Map map)`：封装JavaBean，使用map

## Response

* 功能：设置相应消息
  1. 设置响应行
     1. 格式：`HTTP/1.1 200 ok`
     2. 设置状态码：`void setStatus(int sc)  `
     
  2. 设置响应头
  
     `void setHeader(String name, String value)  `
  
  3. 设置响应体
  
     使用步骤
     
     1. 获取输出流
        * 字节输出流`ServletOutputStream getOutputStream()`
        * 字符输出流`PrintWriter getWriter()`
     2. 使用输出流，将数据输出到客户端浏览器
  
* 重定向

  * 重定向特点

    1. 地址栏发生改变
    2. 重定向可以访问其他网站
    3. 重定向是两次请求

  * 转发的特点

    1. 地址栏路径不变
    2. 只能访问当前路径下的程序
    3. 转发是一次请求

  * 路径写法

    1. 路径分类

       1. 相对路径：通过相对路径不可以确定唯一资源    `./xxx`

          规则：确定访问当前资源与目标资源之间的相对位置关系 

          * `./`当前目录
          * `../`后退一级目录

       2. 绝对路径：通过绝对路径可以访问唯一资源    `/xxx/xxxx`

          * 规则：判断给谁使用

            * 给客户端浏览器使用：需要加虚拟目录

              建议虚拟目录动态获取：`request.getContextPath()`

            * 给服务器使用：不需要加虚拟目录

* 字符流的乱码问题

  `response.setContentType("text/html;charset=utf-8");`

```java
//访问demo1，会自动跳转的demo2
/*//1.设置状态码为302
response.setStatus(302);
//2.设置响应头location
response.setHeader("location", "/responseDemo2");*/
//简单的重定向方法
response.sendRedirect("/responseDemo2");
```

## ServletContext对象

* 概念：整个web应用，可以和程序的容器（服务器）通信
* 获取：
  1. 通过request获取  `request.getServletContext()`
  2. 通过httpServlet获取 `this.getServletContext()`

* 功能

  1. 获取MIME类型：

     * MIME类型：在互联网通信过程中定义的一种文件类型
     * 类型：大类型/小类型  `text/html`  `image/jpeg`
     * 获取：`String getMimeType(String file)`

  2. 域对象：共享数据

     1. `setAttribute(String name, Object value)`
     2. `getAttribute(String name)`
     3. `remoteAttribute(String name)`

     * ServletContext对象范围：所有用户所有请求的数据

  3. 获取文件的真实（服务器）路径

     * 方法：`String getRealPath(String path)`

## 文件下载

* 需求
  1. 页面显示超链接
  2. 点击超链接后弹出下载提示框
  3. 完成图片文件下载
* 分析：
  1. 超链接指向的资源如果能够被浏览器解析，则在浏览器中展示，如果不能解析，则弹出下载提示框。不满足需求
  2. 任何资源都必须弹出下载提示框
  3. 使用响应头设置资源的打开方式：
     * content-disposition:attachment;filename=xxx

* 步骤：
  1. 定义页面，编辑超链接href属性，指向Servlet，传递资源名称filename
  2. 定义Servlet
  3. 获取文件名称
  4. 使用字节输入流加载文件进内存
  5. 指定response的响应头： content-disposition:attachment;filename=xxx
  6. 将数据写出到response输出流

* 问题：

  * 中文文件问题
  * 解决思路：
    1. 获取客户端使用的浏览器版本信息
    2. 根据不同的版本信息，设置filename的编码方式不同

## 会话内容

1. 会话：一次会话中包含多次请求和响应
   * 一次会话：浏览器第一次给服务器资源发送请求，会话建立，直到有一方断开连接
2. 功能：在一次会话的范围内的多次请求间共享数据
3. 方式：
   1. 客户端会话技术：Cookie
   2. 服务器端会话技术：Session

## Cookie

1. 概念：客户端会话技术。将数据保存在客户端

2. 快速入门：

   * 使用步骤
     1. 创建Cookie对象，绑定数据
        * `new Cookie(String name, String value)`
     2. 发送Cookie对象
        * `response.addCookie(Cookie cookie)`
     3. 获取Cookie，拿到数据
        * `Cookie[] request.getCookies()`

3. 实现原理：基于响应头set-cookie和请求头cookie实现

4. cookie细节

   1.  一次可以发送多个cookie

   2. cookie保存时间

      1. 默认情况下，当浏览器关闭后，Cookie数据被销毁

      2. 设置cookie持久化存储：`setMaxAge(int seconds)`

         1. 正数：将cookie数据写入到硬盘中。持久化储存。cookie的存活时间
         2. 负数：默认值
         3. 0：删除cookie信息

      3. 存储中文

         * tomcat8之前，不能存储中文，解决方法：讲中文数据转码---一般采用URL编码
         * tomcat8之后，可以存储中文。特殊字符还是不支持，建议使用URL编码

      4. 获取范围

         * 在同一个tomcat服务器中，默认情况下不能共享

           * `setPath()`:设置cookie的获取范围。默认情况下，设置当前的虚拟目录，如果要共享，则可将path设置为"/"

         * 在不同tomcat服务器间cookie共享

           * `setDomain(String path)` ：如果设置一级域名，那么多个服务器之间cookie可以共享

             例：setDomain(".baidu.com")，那么tieba.baidu.com和news.baidu.com可以共享 

      5. 特点和作用

         1. cookie存储数据在客户端浏览器
         2. 浏览器对单个cookie的大小有限制（4kb)，以及对同一个域名下的总cookie数量有限制（20个）

         * 作用
           1. cookie一般用于存储少量的不太敏感的数据
           2. 在不登录的情况下，完成服务器对客户端的身份识别

## Session

1. 概念：服务器端会话技术，在一次会话的多次请求间共享数据，将数据保存在服务器端的对象中。HttpSession
2. 快速入门

   1. 获取HttpSession对象：
      * HttpSession session = request.getSession();
   2. 使用HttpSession对象
      * `Object getAttribute(String name) ``
      * ``void setAttribute(String name, Object value)`
      * `void removeAttribute(String name) ` 
3. 原理
   * Session的实现是依赖于Cookie的
4. 细节
   1. 当客户端关闭后，服务器不关闭，两次获取的Session是否为同一个
      * 默认不是同一个
      * 如果需要相同，则可以创建Cookie，Cookie，键为JSESSIONID，设置最大存活时间，让cookie持久化储存
   2. 客户端不关闭，服务器关闭后，两次获取的session
      * 不是同一个，但要确保数据不丢失
        * session的钝化：在服务器正常关闭之前。将session对象序列化到硬盘上
        * session的活化：在服务器启动后，将session文件转化为内存中的session对象即可
   3. Session什么时候被销毁
      1. 服务器关闭
      2. session的对象调用invalidate()方法
      3. session默认的失效时间：30min
         * 选择性修改
5. session的特点
   1. session用于存储一次会话的多次请求的数据，存在服务器端
   2. session可以存储任意类型，任意大小的数据

## Session和Cookie的区别

1. session存储数据在服务器端，cookie在客户端
2. session没有大小限制，cookie有限制
3. session数据安全，cookie相对于不安全

## JSP

1. 概念：Java Server Pages：java服务器端页面	

2. 原理：本质是就是一个Servlet

3. JSP的脚本：JSP定义java代码的方式

   1. `<% code %>`：定义的java代码，在service方法中。service可以定义什么，该脚本中就可以定义什么
   2. `<!% code %>`：定义的java代码。在jsp转换后的java类的成员位置。
   3. `<!= code %>`：定义的java。会输出到页面上。输出定义什么，该脚本中就可以定义什么

4. 指令

   * 作用：用于配置jsp页面，导入资源文件
   * 格式：`<%@ 指令名称 属性名1=属性值1 属性名2=属性值2 ... %>`
   * 分类：
     1. page：配置JSP页面的
        * contentType：等同于response.setContentType()
          1. 设置响应体的mime类型以及字符集
          2. 设置当前jsp页面的编码（只能是高级的IDE才能生效，如果使用低级工具，则需要设置pageEncoding属性设置当前页面的字符集）
        * import：导包
        * errorPage：当前页面发生异常后，会自动跳转到指定的错误页面
        * isErrorPage：标识当前也是是否是错误页面。
          * true：是，可以使用内置对象exception
          * false：否。默认值。不可以使用内置对象exception
     2. include：页面包含的。导入页面的资源文件
        * <%@include file="top.jsp"%>
     3. taglib：导入资源
        * `<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>`
        * prefix：前缀，自定义的

5. 注释

   1. html注释：`<!-- -->`:只能注释html代码片段
   2. jsp注释：`<%-- --%>`：可以注释所有

6. 内置对象

   在jsp页面中不需要获取和创建，可以直接使用的对象。一共有9个内置对象

   |   变量名    |      真实类型       |
   | :---------: | :-----------------: |
   | pageContext |     PageContext     |
   |   request   | HttpServletRequest  |
   |   session   |     HttpSession     |
   | application |   ServletContext    |
   |  response   | HttpServletResponse |
   |    page     |       Object        |
   |     out     |      JspWriter      |
   |   config    |    ServletConfig    |
|  exception  |      Throwable      |
   

   
* pageContext：当前页面共享数据，还可以获取其他八个内置对象
  
* request：一次请求访问的多个资源(转发)
  
* session：一次会话的多个请求间
  
* application：所有用户间共享数据
  
* response：响应对象
  
* page：当前页面(Servlet)的对象  this
  
* out：字符输出流对象。可以将数据输出到页面上。和`request.getWriter()`类似
  
     `request.getWriter()`和out的区别：在tomcat服务器真正给客户端作出响应之前，会先找response缓冲区数据，再找out缓冲区数据。`request.getWriter()`数据输出永远在`out.write()`之前
     
* config：Servlet的配置对象
  
   * exception：异常对象

## MVC开发模式

1. M：Model，模型。JavaBean
   * 完成具体的业务操作，如：查询数据库，封装对象
2. V：View，视图。JSP
   * 展示数据
3. C：Controller，控制器。Servlet
   * 获取用户的输入
   * 调用模型
   * 将数据交给视图进行展示

* 优缺点
  1. 优点
     1. 耦合性低，方便维护，可以利于分工协作
     2. 重用性高
  2. 缺点
     1. 使得项目架构变得复杂，对开发人员要求高

## EL表达式

1. 概念：Expression Language 表达式语言
2. 作用：替换和简化jsp页面中java代码的编写
3. 语法：`${表达式}`
4. 注意
   * jsp默认支持el表达式的。如果要忽略el表达式
     1. 设置jsp中page指令中：isELIgnored="true" 忽略当前jsp页面中所有的el表达式
     2. \${表达式} ：忽略当前这个el表达式
5. 使用
   1. 运算
      1. 算数运算符： + - * /(div) %(mod)
      2. 比较运算符： > < >= <= == !=
      3. 逻辑运算符： &&(and) ||(or) !(not)
      4. 空运算符： empty
         * 功能：用于判断字符串、集合、数组对象是否为null或者长度是否为0
         * ${empty list}:判断字符串、集合、数组对象是否为null或者长度为0
         * ${not empty str}:表示判断字符串、集合、数组对象是否不为null 并且 长度>0
   2. 获取值
      1. el表达式只能从域对象中获取值
      2. 语法：
         1. ${域名称.键名}：从指定域中获取指定键的值
            * 域名称：
              1. pageScope		--> pageContext
              2. requestScope 	--> request
              3. sessionScope 	--> session
              4. applicationScope --> application（ServletContext）
         2. ${键名}：表示依次从最小的域中查找是否有该键对应的值，直到找到为止。
         3. 获取对象、List集合、Map集合的值
            1. 对象：${域名称.键名.属性名}（本质上会去调用对象的getter方法）
            2. List集合：${域名称.键名[索引]}
            3. Map集合：
               * ${域名称.键名.key名称}
               * ${域名称.键名["key名称"]}
         4. 隐式对象：
            * el表达式中有11个隐式对象
            * pageContext
              * 获取jsp其他八个内置对象   
              * `${pageContext.request.contextPath}`：动态获取

## JSTL

1. 概念：JavaServer Pages Tag Library  JSP标准标签库，是由Apache组织提供的开源的免费的jsp标签
2. 作用：用于简化和替换jsp页面上的java代码
3. 使用步骤
   1. 导入jstl相关jar包
   2. 引入标签库：taglib指令：  `<%@ taglib %>`
   3. 使用标签
4. 常用的JSTL标签
   1. if:相当于java代码的if语句
      1. 属性
         * test 必须属性，接受boolean表达式
           * 如果表达式为true，则显示if标签体内容，如果为false，则不显示标签体内容
           * 一般情况下，test属性值会结合el表达式一起使用
      2. 注意：
         * c:if标签没有else情况，想要else情况，则可以在定义一个c:if标签
   2. choose:相当于java代码的switch语句
      1. 使用choose标签声明（相当于switch声明）
      2. 使用when标签做判断（相当于case）
      3. 使用otherwise标签做其他情况的声明（相当于default）
   3. foreach:相当于java代码的for语句
      1. 完成重复操作
         * 属性：
           * begin：开始值
           * end：结束值
           * var：临时变量
           * step：步长
           * varStatus：循环状态对象
             * index：容器中元素的索引，从0开始
             * count：循环次数，从1开始
      2. 遍历容器 
         * 属性
           * item：容器对象
           * var：容器中元素的临时变量
           * varStatus：循环状态对象
             * index：容器中元素的索引，从0开始
             * count：循环次数，从1开始

## Filter过滤器

当访问服务器的资源时，过滤器可以将请求拦截下来，完成一些特殊的功能

* 过滤器的作用  

  一般用于完成通用的操作。如：登录验证、统一编码处理、敏感字符过滤...

* 快速入门

  1. 步骤：

     1. 定义一个类，实现接口Filter
     2. 复写方法
     3. 配置拦截路径
        1. web.xml
        2. 注解

  2. 过滤器细节：

     1. web.xml配置

        ```xml
        <filter>
            <filter-name>demo1</filter-name>
            <filter-class>cn.itcast.web.filter.FilterDemo1</filter-class>
        </filter>
        <filter-mapping>
            <filter-name>demo1</filter-name>
            <!-- 拦截路径 -->
            <url-pattern>/*</url-pattern>
        </filter-mapping>
        ```

     2.  过滤器执行流程

        1. 执行过滤器
        2. 执行放行后的资源
        3. 回来执行过滤器放行代码下边的代码

     3. 过滤器生命周期方法

        1. init:在服务器启动后，会创建Filter对象，然后调用init方法。只执行一次。用于加载资源
        2. doFilter:每一次请求被拦截资源时，会执行。执行多次
        3. destroy:在服务器关闭后，Filter对象被销毁。如果服务器是正常关闭，则会执行destroy方法。只执行一次。用于释放资源

     4. 过滤器配置详解

        * 拦截路径配置：

          1. 具体资源路径： /index.jsp   只有访问index.jsp资源时，过滤器才会被执行
          2. 拦截目录： /user/*	访问/user下的所有资源时，过滤器都会被执行
          3. 后缀名拦截： \*.jsp		访问所有后缀名为jsp资源时，过滤器都会被执行
       4. 拦截所有资源：/\*		访问所有资源时，过滤器都会被执行
        * 拦截方式配置：资源被访问的方式
          * 注解配置：
            * 设置dispatcherTypes属性
              1. REQUEST：默认值。浏览器直接请求资源
              2. FORWARD：转发访问资源
              3. INCLUDE：包含访问资源 
              4. ERROR：错误跳转资源
              5. ASYNC：异步访问资源
            * web.xml配置
              * 设置<dispatcher></dispatcher>标签即可
     
     5. 过滤器链(配置多个过滤器)

        * 执行顺序：如果有两个过滤器：过滤器1和过滤器2
          1. 过滤器1
          2. 过滤器2
          3. 资源执行
          4. 过滤器2
          5. 过滤器1
        * 过滤器先后顺序问题：
          1. 注解配置：按照类名的字符串比较规则比较，值小的先执行
             * 如： AFilter 和 BFilter，AFilter就先执行了。
          2. web.xml配置： <filter-mapping>谁定义在上边，谁先执行

* 增强对象的功能：


  * 设计模式：一些通用的解决固定问题的方式

	1. 装饰模式
	2. 代理模式

       * 概念：

         1. 真实对象：被代理的对象
         2. 代理对象：
         3. 代理模式：代理对象代理真实对象，达到增强真实对象功能的目的
    
            * 实现方式：
    
              1. 静态代理：有一个类文件描述代理模式
              2. 动态代理：在内存中形成代理类
    
            * 实现步骤：
    
              1. 代理对象和真实对象实现相同的接口
              2. 代理对象 = Proxy.newProxyInstance();
              3. 使用代理对象调用方法。
              4. 增强方法
    
            * 增强方式：
              1. 增强参数列表
              2. 增强返回值类型
              3. 增强方法体执行逻辑

## Listener：监听器

web三大组件之一

* 事件监听机制

  * 事件：一件事情
  * 事件源：事件发生的地方
  * 监听器：一个对象
  * 注册监听：将事件，事件源，监听器绑定在一起。当事件源上发生某个事件后，执行监听器代码

* `ServletContextListener`:监听`ServletContext`对象的创建和销毁

  * 方法

    * `void contextDestroyed(ServletContextEvent sce) `：`ServletContext`对象被销毁之前会调用该方法
    * `void contextInitialized(ServletContextEvent sce) `：`ServletContext`对象创建后会调用该方法

  * 步骤：

    1. 定义一个类，实现`ServletContextListener`接口

    2. 复写方法

    3. 配置

       1. xml

          ```xml
          <listener>
             <listenerclass>cn.itcast.web.listener.ContextLoaderListener
             </listener-class>
          </listener>
          ```

          * 指定初始化参数`<context-param>`
          
       2. 注解

          `@WebListener`

