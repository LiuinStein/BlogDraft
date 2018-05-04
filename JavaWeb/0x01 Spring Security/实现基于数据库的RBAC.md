## 0x00 应用场景

现考虑如下需求：

* 目标是一个**Restful**服务端程序
* 权限管理使用**RBAC访问控制模型**
* 管理员可以对角色**动态授权或剥夺权限**，权限的修改要**即时生效**
* 密码在数据库里以**强度为11的BCrypt算法**加密后存储
* API站点有单独的子域名，所以要支持**跨域访问**
* 使用包装在Json里面的用户名密码登录

## 0x01 数据库设计

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

## 0x02 Spring Security配置

### 0x00 所需依赖（Maven）

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

### 0x01 web.xml 配置拦截器及监听器

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

### 0x02 Application Context 应用上下文配置

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

#### 0x00 http标签

```xml
<http auto-config="false" create-session="always" entry-point-ref="restAuthenticationEntryPoint">
    
        <session-management session-fixation-protection="migrateSession">
            <concurrency-control max-sessions="1" session-registry-ref="sessionRegistry"/>
        </session-management>

        <remember-me services-ref="rememberMeServices"/>

        <custom-filter ref="securityFilter" before="FILTER_SECURITY_INTERCEPTOR"/>
        <custom-filter ref="myAuthenticationFilter" position="FORM_LOGIN_FILTER"/>
        <custom-filter ref="myLogoutFilter" position="LOGOUT_FILTER"/>

        <csrf disabled="true"/>

        <cors configuration-source-ref="corsSource"/>

    </http>
```

##### 0x00 auto-config

`auto-config`用来设置自动配置，可以简化一些配置工作，并且Spring Security会自动加上一些安全机制，但是对于我们目前的应用来说，我们需要自定义`FORM_LOGIN_FILTER`过滤器，所以就**必需禁用**`auto-config`，即设置`auto-config="false"`，原因如下：

> 摘自Spring Security官方文档
>
> **Avoiding filter position conflicts**
>
> If you are inserting a custom filter which may occupy the same position as one of the standard filters created by the namespace then it's important that you don't include the namespace versions by mistake. **Avoid using the `auto-config` attribute and remove any elements which create filters whose functionality you want to replace**.Note that you can't replace filters which are created by the use of the `<http>` element itself - `SecurityContextPersistenceFilter`, `ExceptionTranslationFilter`or `FilterSecurityInterceptor`.
>
> 其实具体的原因你看我加粗的那些话就可以了

##### 0x01 create-session

`create-session`用来设置session的模式，在这里有4种模式

* `always`: 总是创建session
* `ifRequired`: 在需要的时候创建session，在Spring Security中会默认为每一个登录的用户创建一个session
* `stateless`: 这个主要用于RESTful API中，这个配置的意思是Spring Security对登录成功的用户不会创建和使用任何的Session了，而且**你的application也不会允许新建session**，而且Spring Security会跳过所有的filter chain: `HttpSessionSecurityContextRepository, SessionManagementFilter, RequestCacheFilter`. 
* `never`: 框架永远也不创建session了，**但是，但是，如果你的application创建了一个session，那么Spring Security还是会使用它的**

> 有关本部分的其他参考资料：
>
> [Control the Session with Spring Security](http://www.baeldung.com/spring-security-session)

##### 0x02 entry-point-ref

`entry-point-ref` 用来设置进入点，为了实现Restful，且未经授权的用户无法访问资源，并返回401`Unauthorized`错误，我们需要手工配置一个`entry-point`

首先创建一个类名为`MyAuthenticationEntryPoint`使其实现`AuthenticationEntryPoint`接口，有如下代码：

```java
public class MyAuthenticationEntryPoint implements AuthenticationEntryPoint {
    @Override
    public void commence(HttpServletRequest request, HttpServletResponse response, AuthenticationException e) throws IOException, ServletException {
        response.sendError(HttpServletResponse.SC_UNAUTHORIZED, "Unauthorized");
    }
}
```

仅需要在commence方法里面设置返回一个401未授权即可，对于一个REST API我们还可以在commence方法中为response body设置Json或xml的详细信息返回串

> 此处可参考：
>
> [Spring Security Basic Authentication](http://www.baeldung.com/spring-security-basic-authentication)
>
> 第4节，**Further Configuration –** **The Entry Point**

创建完成之后，我们需要将其注册成一个bean，然后放在`entry-point`里面

##### 0x03 session-management

`session-management`标签用来进行session的进一步管理

其中`session-fixation-protection`用来设置抵御session fixation攻击的具体实现模式，在这里我们可以有4种不同的属性设置

* `none` 什么都不做，放任被攻击的可能性不管
* `newSession` 创建一个全新的session，仅将Spring Security相关的属性复制过去
* `migrateSession` 创建一个全新的session将所有已存在的session属性全部拷贝过去，这是Servlet 3.0及之前版本默认的防护措施
* `changeSessionId` 不创建一个全新的session，使用Servlet容器提供的保护措施
  `HttpServletRequest#changeSessionId()`这个选项仅在Servlet 3.1 (Java EE 7) 之后的版本可以使用，之前的版本如果设置了这个选项将会抛出异常，这是Servlet 3.1默认的防护措施

> 有关session fixation攻击可参考OWASP文章：
>
> [session fixation - OWASP](https://www.owasp.org/index.php/Session_fixation)
>
> 具体攻击细节可参考其Examples章节
>
> 有关Spring Security对session fixation攻击防御细节，可参考其官方文档
>
> [Session Fixation Attack Protection](https://docs.spring.io/spring-security/site/docs/5.0.x/reference/htmlsingle/#ns-session-fixation)

`concurrency-control`标签用来设置session的并发数量，其中

* `max-sessions`用来设置最大并发session数，如果将其值设置为1，那么仅允许同时仅有一个客户端来访问这个session，如果设置为-1则去除限制，允许无限多个session

* `session-registry-ref`用来自定义我们的session registry，因为此处我们将session存储在了Redis中，所以此处需自定义session registry，它的值指向一个bean

  ```xml
  <beans:bean id="sessionRegistry" class="org.springframework.session.security.SpringSessionBackedSessionRegistry">
          <beans:constructor-arg ref="sessionRepository"/>
      </beans:bean>
  ```

> 更多标签和属性信息可参考官方文档
>
> [<concurrency-control> Attributes](https://docs.spring.io/spring-security/site/docs/5.0.x/reference/htmlsingle/#nsa-concurrency-control-attributes)

##### 0x04 custom-filter

这个用来自定义filter，它默认有一套filter chain

> 详细的Spring Security Filter Chain信息可参考：
>
> [14.3. Filter Ordering](https://docs.spring.io/spring-security/site/docs/5.0.x/reference/html5/#filter-ordering)

在上述的参考文献中即为一个请求可能走过的filter列表，从上往下依次走过

对于`custom-filter`标签，他提供了：

* `ref=` 用来指向filter的bean
* `before=` 在给定的filter之前插入
* `position=` 替换掉给定的filter
* `after=` 在给定的filter之后插入

你自己写的filter必需要实现接口`javax.servlet.Filter`

后面的`before`，`position`，`after`的值是一个filter class的代号（alias）

> 相关代号可以参考表格：
>
> [6.3.6. Adding in Your Own Filters](https://docs.spring.io/spring-security/site/docs/5.0.x/reference/html5/#ns-custom-filters)
>
> 表格在此节的Table 2. Standard Filter Aliases and Ordering

在这里不将整个的表格都拿过来了，说一下这个表格怎么看以及注意事项

| Alias         | Filter Class   | Namespace Element or Attribute |
| ------------- | -------------- | ------------------------------ |
| LOGOUT_FILTER | `LogoutFilter` | ` http/logout `                |

上面这个表格是在上述参考文献的表格里摘出来的一小部分

从左边看起

* 第一列是这个filter的代号，也就是你应当放到`before`，`position`或`after`里面的值。
* 第二列是这个代号所代指的具体的类，也就是Spring Security框架所实现这个filter的具体的类名
* 第三列是这个filter与哪个XML文件配置标签作用相同

> 以下摘自Spring Security文档6.3.6小节
>
> Avoiding filter position conflicts
>
> If you are inserting a custom filter which may occupy the same position as one of the standard filters created by the namespace then it’s important that you don’t include the namespace versions by mistake. Remove any elements which create filters whose functionality you want to replace. 
>
> 大意就是，如果使用了XML标签就不能添加相关的`custom-filter`，如果添加了相关的`custom-filter`那就不能使用第三列所指的XML标签，否则他就会抛出异常
>
> 举个例子：
>
> ```xml
> <custom-filter ref="myLogoutFilter" position="LOGOUT_FILTER"/>
> <logout logout-url="/v1/user/session">
> ```
>
> 如果上面的配置出现在同一个配置文件里，Spring Security就会抛出异常

#### 0x01 配置自定义Security filter



