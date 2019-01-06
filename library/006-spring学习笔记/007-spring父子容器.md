# spring父子容器



## 1、web.xml中配置spring父容器

加载了所有的关于dao、service、事务的相关配置放进spring的父容器中

```xml 
<!-- 初始化spring容器 -->
<context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>classpath:spring/applicationContext-*.xml</param-value>
</context-param>
<listener>
    <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
</listener>
```



## 2、spring的子容器springMVC配置

springmvc 本身也是一个容器，controller层对象放在springmvc容器中，

**web.xml**

```xml
<!-- springmvc的前端控制器 -->
<servlet>
    <servlet-name>taotao-manager</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <!-- contextConfigLocation不是必须的， 如果不配置contextConfigLocation， springmvc的配置文件默认在：WEB-INF/servlet的name+"-servlet.xml" -->
    <init-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:spring/springMvc.xml</param-value>
    </init-param>
    <load-on-startup>1</load-on-startup>
</servlet>
<servlet-mapping>
    <servlet-name>taotao-manager</servlet-name>
    <url-pattern>/</url-pattern>
</servlet-mapping>
```



## 3、关系

**子容器可以访问父容器的对象，父容器不能访问子容器中的对象。** 



## 4、注意

### 4.1 、service层或dao 层

原配置

```xml
<!-- 包扫描器，扫描带@Service注解的类 -->
<context:component-scan base-package="com.zxw.service"></context:component-scan>
```



如果把service的扫描配置为

```
<!-- 包扫描器，扫描带@Service注解的类 -->
<context:component-scan base-package="com.zxw"></context:component-scan>
```

就会把controller对象也扫描进来造成资源的浪费，spring用的是springmvc中的对象。



### 4.2、springMVC配置

如果把springMVC的包扫描扩大一节，那么就会把service、dao、层的对象全部扫描到springmvc容器中，这样springmvc使用service对象的时候会使用自己容器中的对象，会出现事务就不起作用啦。