### 0x00 应用场景

现考虑如下需求：

* 目标是一个**Restful**服务端程序
* 权限管理使用**RBAC访问控制模型**
* 管理员可以对角色**动态授权或剥夺权限**，权限的修改要**即时生效**
* 密码在数据库里以**强度为11的BCrypt算法**加密后存储
* 使用包装在Json里面的用户名密码登录

### 0x01 数据库设计

先看E-R图：

![E-R图](http://bucket.shaoqunliu.cn/image/0150.png)

> 来自我的个人项目，其他信息可参照：
>
> [CIL实验室管理系统 - GitHub](https://github.com/LiuinStein/CILManagement-Server)
>
> [数据字典 - GitHub](https://github.com/LiuinStein/CILManagement-Server/blob/dev/docs/Design-of-Schema.md)
>
> [SQL数据库结构 - GitHub](https://github.com/LiuinStein/CILManagement-Server/blob/dev/database/structure.sql)
>
> [SQL示例数据 - GitHub](https://github.com/LiuinStein/CILManagement-Server/blob/dev/database/example-data/example-data.sql)

这个设计可以满足3NF（第三范式）的需求

### 0x02 Spring Security配置

#### 0x00 所需依赖（Maven）

```xml
<!-- Spring Security -->
<dependency>
    <groupId>org.springframework.security</groupId>
    <artifactId>spring-security-core</artifactId>
    <version>${spring.security.version}</version>
</dependency>
<dependency>
    <groupId>org.springframework.security</groupId>
    <artifactId>spring-security-config</artifactId>
    <version>${spring.security.version}</version>
</dependency>
<dependency>
    <groupId>org.springframework.security</groupId>
    <artifactId>spring-security-taglibs</artifactId>
    <version>${spring.security.version}</version>
</dependency>
<dependency>
    <groupId>org.springframework.security.oauth</groupId>
    <artifactId>spring-security-oauth2</artifactId>
    <version>${spring.security.oauth2.version}</version>
</dependency>
```

版本信息：

```xml
<spring.security.version>5.0.3.RELEASE</spring.security.version>     <spring.security.oauth2.version>2.2.1.RELEASE</spring.security.oauth2.version>
```

#### 0x01 web.xml 配置拦截器及监听器

**拦截器:**

```xml
<filter>
    <filter-name>springSecurityFilterChain</filter-name>
    <filter-class>org.springframework.web.filter.DelegatingFilterProxy</filter-class>
</filter>
<filter-mapping>
    <filter-name>springSecurityFilterChain</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

在`<url-pattern>`中我们配置了对所有请求地址实施拦截

**监听器：**

```xml
<listener>
    <listener-class>org.springframework.security.web.session.HttpSessionEventPublisher</listener-class>
</listener>
```

这个监听器的作用是用来管控session的，例如用来控制session的并发，就是当前账号可以同时在多少个设备上同时登陆，在同一个时间点，如果两个设备同时登陆，要么使前一个登陆失效要么第二次不能登陆

#### 0x02 Application Context 应用上下文配置

此处我们新建了一个文件名为`applicationContext-sec.xml`

> 全部代码可以参考我项目里的这个文件:
>
> [applicationContext-sec.xml - GitHub](https://github.com/LiuinStein/CILManagement-Server/blob/dev/src/main/resources/spring/applicationContext-sec.xml)
>
> 有关这个文件的具体描述，可以参照官方文档：
>
> [Security Namespace Configuration](https://docs.spring.io/spring-security/site/docs/3.0.x/reference/ns-config.html)
>
> 不知道为什么他这个官方文档里面给出的一些reference链接都是失效的

##### 0x00 http标签

```xml
<http auto-config="false" create-session="always" entry-point-ref="restAuthenticationEntryPoint">
    
    <custom-filter ref="securityFilter" before="FILTER_SECURITY_INTERCEPTOR"/>
    <custom-filter ref="myAuthenticationFilter" position="FORM_LOGIN_FILTER"/>

    <csrf disabled="true"/>

</http>
```

`auto-config`用来设置自动配置，可以简化一些配置工作，并且Spring Security会自动加上一些安全机制，但是对于我们目前的应用来说，我们需要自定义`FORM_LOGIN_FILTER`过滤器，所以就**必需禁用**`auto-config`，即设置`auto-config="false"`，原因如下：

> 摘自Spring Security官方文档
>
> **Avoiding filter position conflicts**
>
> If you are inserting a custom filter which may occupy the same position as one of the standard filters created by the namespace then it's important that you don't include the namespace versions by mistake. **Avoid using the `auto-config` attribute and remove any elements which create filters whose functionality you want to replace**.Note that you can't replace filters which are created by the use of the `<http>` element itself - `SecurityContextPersistenceFilter`, `ExceptionTranslationFilter`or `FilterSecurityInterceptor`.
>
> 其实具体的原因你看我加粗的那些话就可以了

`create-session`用来设置session的模式，在这里有4种模式

* `always`: 总是创建session
* `ifRequired`: 在需要的时候创建session，在Spring Security中会默认为每一个登录的用户创建一个session
* `stateless`: 这个主要用于RESTful API中，这个配置的意思是Spring Security对登录成功的用户不会创建和使用任何的Session了，而且**你的application也不会允许新建session**，而且Spring Security会跳过所有的filter chain: `HttpSessionSecurityContextRepository, SessionManagementFilter, RequestCacheFilter`. 
* `never`: 框架永远也不创建session了，**但是，但是，如果你的application创建了一个session，那么Spring Security还是会使用它的**

> 有关本部分的其他参考资料：
>
> [Control the Session with Spring Security](http://www.baeldung.com/spring-security-session)



