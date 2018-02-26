### 0x00 web.xml加载过程

当我们启动一个WEB项目容器(tomcat)，首先会去读取web.xml配置文件里的配置，当这一步骤没有出错并且完成之后，项目才能正常的被启动起来。

启动WEB项目的时候，容器首先会去读取web.xml配置文件中的两个节点：

`<listener>`和`<context-param>`，例如：

```xml
<context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>classpath:spring/applicationContext*.xml</param-value>
</context-param>

<listener>
    <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
</listener>
```

此时容器将创建一个`ServletContext`，这个web项目所有的部分将会**共享**这个上下文，并且容器将`<context-param>`中的参数名和参数值转化为键值对存入这个`ServletContext`

然后容器通过实例化`<listener>`结点中的`<listener-class>`中的类名来创建监听，启动这个web应用时容器调用这个listener中的`contextInitialized`方法：

```java
public void contextInitialized(ServletContextEvent event) {
    this.initWebApplicationContext(event.getServletContext());
} // 摘自 org.springframework.web.context.ContextLoaderListener
```

其中`event.getServletContext()`用来获取`ServletContext`，然后即可以使用

```java
servletContext.getInitParameter("context-param中的param-name");
```

来获取在web.xml中`<context-param> </context-param>`下的参数值

同样在容器销毁时也会调用listener中的`contextDestroyed`方法

```java
public void contextDestroyed(ServletContextEvent event) {
    this.closeWebApplicationContext(event.getServletContext());
    ContextCleanupListener.cleanupAttributes(event.getServletContext());
} // 摘自 org.springframework.web.context.ContextLoaderListener
```

接着容器回去读取`filter`标签，例如如下一个完整的`filter`

```xml
<filter>
    <filter-name>forceUtf8Encoding</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    <init-param>
        <param-name>encoding</param-name>
        <param-value>UTF-8</param-value>
    </init-param>
    <init-param>
        <param-name>forceEncoding</param-name>
        <param-value>true</param-value>
    </init-param>
</filter>
<filter-mapping>
    <filter-name>forceUtf8Encoding</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

容器会实例化`filter-class`所指定的类，同样其中标签`init-param`指定了用于传递给类构造方法的参数，比如在上述配置中，指定了`encoding`和`forceEncoding`两个参数名，容器即使用如下构造方法对`CharacterEncodingFilter`进行构造

```java
public CharacterEncodingFilter(String encoding, boolean forceEncoding) {
    this(encoding, forceEncoding, forceEncoding);
} // 摘自 org.springframework.web.filter.CharacterEncodingFilter
```

此时web项目还没有完全启动起来

如果系统中有Servlet，则Servlet是在第一次发起请求的时候被实例化的，而且**一般不会被容器销毁**，它可以**服务于多个用户**的请求。

总的来说：web.xml的加载顺序是: `<context-param>`-> `<listener>` -> `<filter>` -> `<servlet>`。其中，如果web.xml中出现了相同的元素，则**按照在配置文件中出现的先后顺序**来加载。

### 0x01 标签

#### 0x00 web-app

```xml
<web-app xmlns="http://java.sun.com/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://java.sun.com/xml/ns/javaee
          http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd"
         version="3.0">
  
</web-app>
```

这个是根元素，对我们的项目来说并没有什么特殊的意义

#### 0x01 display-name

```xml
<display-name>Archetype Created Web Application</display-name>
```

这个用于指定web-app项目的名称，上例为使用maven创建的web-app的默认名称

#### 0x02 distributable

```xml
<distributable>
```

这个是一个空标签，指定站点是否可以进行分布式处理

#### 0x03 context-param

```xml
<context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>classpath:spring/applicationContext*.xml</param-value>
</context-param>
```

这个已经在文章开头web.xml加载过程里面详细地说过了，主要用作Servlet上下文初始化参数，参数名在整个Web应用中**必须是唯一**的，在web应用的整个生命周期中上下文初始化参数**都存在**，任意的Servlet和jsp中都可以随时随地访问它。

#### 0x04 session-config

```xml
<session-config>  
    <session-timeout>30</session-timeout>  
    <cookie-config>  
        <name>JSESSIONID</name>  
        <domain>example.org</domain>  
        <path>/shop</path>  
        <http-only>true</http-only>  
        <secure>false</secure>  
        <max-age>1800</max-age>  
    </cookie-config>  
    <tracking-mode>COOKIE</tracking-mode>  
    <tracking-mode>URL</tracking-mode>  
    <tracking-mode>SSL</tracking-mode>  
</session-config>  
```

这个用来配置session，例如上例中的`session-timeout`即用来配置session的过期时间

`<tracking-mode>`用于表示容器应该使用哪种技术追踪会话ID，它的合法值有：

* URL：容器将只在URL中内嵌会话ID，这种方式非常不安全。
* COOKIE：容器将使用cookie追踪会话ID，该技术非常安全。
* SSL：容器将使用SSL会话ID作为HTTP会话ID，该方法是最安全的方式，但要求使用的**所有请求都必须是HTTPS请求**。

`session-timeout`的单位为分钟，`max-age`的单位为秒

#### 0x05 filter

filter对用户请求进行**预处理**，接着将请求交给Servlet进行处理并生成响应，最后filter再对服务器响应进行**后处理**。

在这儿留个悬念，具体细节，先不做叙述

#### 0x06 listener

listener用来监听客户端的请求和服务端的操作

在这儿留个悬念，具体细节，先不做叙述

#### 0x07 servlet

```xml
<servlet>
    <servlet-name>SpringMVC</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <load-on-startup>1</load-on-startup>
    <init-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:spring/springmvc.xml</param-value>
    </init-param>
</servlet>
<servlet-mapping>
    <servlet-name>SpringMVC</servlet-name>
    <url-pattern>/</url-pattern>
</servlet-mapping>
```

servlet就是服务端小程序，在上例中仅说明一个标签`load-on-startup`

默认情况下servlet都是在客户端第一次访问这个请求的时候创建

load-on-startup标签**标记容器是否在启动的时候就加载**这个servlet

* 当值为0或者大于0时，表示容器在启动时就加载并初始化这个servlet。
* 当值小于0或者没有指定时，则表示容器在该servlet被请求时，才会去加载

正数的值越小，该servlet的优先级就越高，应用启动时就优先加载，当值相同的时候，容器就会自己选择优先加载。

#### 0x08 error-page

```xml
<error-page>
    <error-code>403</error-code>
    <location>/WEB-INF/jsp/error/403.jsp</location>
</error-page>
```

这个用来指定错误的返回界面，`error-code`用来指定错误代码，`location`为错误界面的位置

#### 0x09 welcome-file-list 

```xml
<welcome-file-list>
    <welcome-file>loginAction_login</welcome-file>
</welcome-file-list>
```

用来指定首页面

### 0x02 其它

#### 0x00 classpath与classpath*区别

同名资源存在时，classpath**只从第一个符合**条件的classpath中加载资源，而**classpath\*会从所有的classpath中加载符合条件的资源**。classpath\*需要遍历所有的classpath，效率肯定比不上classpath，因此在项目设计的初期就尽量规划好资源文件所在的路径，**避免使用classpath\*来加载**。

