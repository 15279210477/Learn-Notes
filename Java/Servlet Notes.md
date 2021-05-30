# Servlet Notes

## 一、Servlet 基础

#### 1.1、注解

**@WebServlet**：注解servlet类

- name：servlet名称（可选）

- value：配置url路径，可多个

- urlPatterns：配置url路径，和value作用一样，不能同时使用

- loadOnStartup：配置servlet的创建时机，数字越小优先级越高

#### 1.2、转发

> 转发的作用在服务器端，将请求发送给服务器上的其他资源，以共同完成一次请求的处理。

###### 页面跳转

> 在调用业务逻辑的Servlet中，编写以下代码：
> 
> ```java
> request.getRequestDispatcher("/目标URL-pattern").forward(req,resp);
> ```

 注意：使用forward跳转时，是发生在服务器内部跳转，地址栏不发生变化，属于同一次请求

###### 数据传递

> forward表示一次请求，是在服务器内部跳转，可以共享同一次request作用域中的数据。
> 
> - request作用域：拥有存储数据的空间，作用范围是一次请求有效（一次请求可以多次转发），可在一次请求的任意位置进行获取，可传递任何数据类型。
> 
> - 存数据：以键值对的形式存储在request作用域中，key为String，value为Object类型。
>   
>   ```java
>   request.setAttribute(key,value);
>   ```
> 
> - 取数据：
>   
>   ```java
>   request.getAttribute(key);
>   ```

#### 1.3、重定向

> 重定向作用在客户端，客户端将请求发送给服务器端后，服务器响应给客户端一个新的请求地址，客户端重新发送新请求。

###### 页面跳转

> 在调用业务逻辑的Servlet中，编写以下代码
> 
> ```java
> response.sendRedirect("目标URI");
> ```

**URL**：统一资源标识符，用来表示服务器中定位一个资源，资源在web项目中的路径。

###### 数据传递

> sendRedirect跳转时，地址栏改变，代表客户端重新发送请求，属于两次请求。
> 
> - response没有作用域，两次request请求中的数据无法共享
> 
> - 传递数据：通过URI的拼接进行数据传递，且只能传字符串类型。

 **总结**：

> 当两个Servlet需要传递数据时，选择forward转发，不建议使用sendRedirect进行传递。

------

## 二、Servlet 生命周期

#### 实例化

> 当用户第一次访问Servlet时，由容器调用Servlet的构造器创建具体的Servlet对象。也可以在容器启动之后立刻创建实例。使用如下代码可以设置Servlet是否在服务器启动时就创建。
> 
> ```java
> <load-on-startup>1</load-on-startup>
> ```
> 
> **注意**：只执行一次

#### 初始化

> 在初始化阶段，init()方法会被调用。这个方法在javax.servlet.Servlet接口中定义。其中，方法以一个ServletConfig类型的对象作为参数。
> 
> **注意**：init方法只被执行一次

#### 服务

> 当客户端有一个请求时，容器就会将请求ServletRequest与响应ServletResponse对象转给Servlet，以参数的形式传给service方法。
> 
> **注意**：此方法会执行多次

#### 销毁

> 当Servlet容器停止或者重启都会引起销毁Servlet对象并调用destroy方法。
> 
> **注意**：destroy方法执行一次

------

## 三、Servlet 特性

#### 线程安全问题，如何保证线程安全

> - synchronized
>   
>   - 将存在线程安全问题的代码放到同步代码块中
> - 实现SingleThreadModel接口
> 
>     - 实现接口后，每个线程都会创建servlet实例，这样每个客户端请求就不存在共享资源的问题，但是servlet响应客户端请求的效率太低，所以已经淘汰。
> - 尽可能使用局部变量

---------

## 四、状态管理

#### 4.1、Cookie

###### 什么是Cookie

> - Cookie是在浏览器访问Web服务器的某个资源时，由web服务器在HTTP响应消息头中附带传送给浏览器的一小段数据。
> 
> - 一旦Web浏览器保存了某个Cookie，那么它在以后每次访问该web服务器时，都应在HTTP请求头中将这个Cookie回传给Web服务器。
> 
> - 一个Cookie主要由标识该信息的名称（name）和值（value）组成。

###### 创建Cookie

```java
Cookie ck = new Cookie("code", code);
ck.setPath("/webs");    // 设置Cookie的路径
ck.setMaxAge(-1);    // 内存存储，取值有三种：>0有效期，单位秒；=0浏览器关闭；<0内存存储，默认-1
response.addCookie(ck);
```

**注意**：有效路径：当前访问资源的上一级目录，不带主机名

###### 获取、修改Cookie

```java
Cookie[] cks = request.getCookies(); // 获取Cookie数组
```

**注意**：只需要保证Cookie的名称和路径一致即可修改，如果改变Cookie的name和有效路径会新建cookie。而仅改变cookie值 ，有效期会覆盖原有cookie。

###### Cookie优点和缺点

> **优点**：
> 
> 1. 可配置到期规则。
> 
> 2. 简单性：Cookie是一种基于文本的轻量结构，包含简单的键值对。
> 
> 3. 数据持久性：Cookie默认在过期之前是可以一直存在客户端浏览器上。

> **缺点**：
> 
> 1. 大小受到限制：大多数浏览器对Cookie的大小有4k、8k的限制。
> 
> 2. 用户配置为禁用：有些用户禁用了浏览器或客户端设备接收Cookie的能力，因此限制了这一功能。
> 
> 3. 潜在的安全风险：Cookie可能会被篡改。会对安全性造成潜在风险或者导致依赖于Cookie的应用程序失败。

#### 4.2、Session对象

###### Session的概述

> Session用于记录用户的状态。Session是指在一段时间内，单个客户端与web服务器的一连串相关的交互过程。

###### Session的原理

> - 服务器会为每一次的回话分配一个Session对象
> 
> - 同一个浏览器发送的多次请求，同属于一次会话
> 
> - 首次使用Session时，服务器会自动创建Session，并创建Cookie存储SessionId发送回客户端
> 
> **注意**：Session是由服务器端创建的。

###### Session的使用

```java
HttpSession session = request.getSession();    // 获取Session
session.setAttribute("key", value);    // 保存Session数据
session.getAttribute("key");    // 获取session数据
session.removeAttribute("key");    // 移除数据
```

###### Session的生命周期

> - 开始：第一次使用到Session的请求产生，则创建Session
> 
> - 结束：
>   
>   - 浏览器关闭，则失效
>   
>   - Session超时，则失效
>     
>     - session.setMaxInactiveInterval(seconds);    // 设置最大有效时间（单位：秒）
>   
>   - 手工销毁，则失效
>     
>     - session.invalidate();    // 登陆退出、注销

###### 浏览器禁用Cookie使用Session的解决方案

**URL重写**：

```java
response.encodeRedirectURL(String url)生成重写的URL。
```

#### 4.3、ServletContext对象

###### 概述

> - 全局对象，也拥有作用域，对应一个Tomcat中的Web应用。 
> 
> - 当web服务器启动时，会为每一个Web应用程序创建一个共享的存储区域（ServletContext）。
> 
> - ServletContext在Web服务器启动时创建，服务器关闭时销毁。
>   
>   **注意**：一个应用对应一个ServletContext，只要容器不关闭，就一直存在。

###### 获取ServletContext对象

> - GenericServlet提供了getServletContext()方法。（推荐）
> 
> - HttpServletRequest提供了getServletContext()方法。（推荐）
> 
> - HttpSession提供了getServletContext()方法。

###### ServletContext作用

```java
servletContext.getRealPath("/");    // 获取当前项目在服务器发布的真实路径
servletContext.getContextPath("/");    // 获取当前项目上下文路径
```

-----------

## 五、过滤器

#### 5.1、概念：

> 过滤器（Filter）是处于客户端与服务器端目标资源之间的一道过滤技术。
> 
> 执行地位在Servlet之前，客户端发送请求会先经过Filter。

------------

## 六、监听器

#### 6.1、概念

> web监听器是servlet中一种特殊的类，能帮助开发者监听web中的特定事件，比如ServletContext，HttpSession，ServletRequest的创建和销毁，变量的创建、销毁和修改等。可在某些动作前后增加处理， 实现监控。
