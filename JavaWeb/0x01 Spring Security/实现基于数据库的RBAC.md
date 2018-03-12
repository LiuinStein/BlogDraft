### 0x00 应用场景

现考虑如下需求：

* 目标是一个**Restful**服务端程序
* 权限管理使用**RBAC访问控制模型**
* 管理员可以对角色**动态授权或剥夺权限**
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

