# 1       springmvc框架

## 1.1      什么是springmvc

springmvc是spring框架的一个模块，springmvc和spring无需通过中间整合层进行整合。

springmvc是一个基于mvc的web框架。

 

 

![img](file:///C:\Users\ADMINI~1\AppData\Local\Temp\msohtmlclip1\01\clip_image002.jpg)

 

 

## 1.2      mvc在b/s系统 下的应用

 

mvc是一个设计模式，mvc在b/s系统 下的应用：

 

![img](file:///C:\Users\ADMINI~1\AppData\Local\Temp\msohtmlclip1\01\clip_image003.gif)

 

## 1.3      springmvc框架

![img](file:///C:\Users\ADMINI~1\AppData\Local\Temp\msohtmlclip1\01\clip_image004.gif)

 

 

第一步：发起请求到前端控制器(DispatcherServlet)

第二步：前端控制器请求HandlerMapping查找 Handler

​         可以根据xml配置、注解进行查找

第三步：处理器映射器HandlerMapping向前端控制器返回Handler

第四步：前端控制器调用处理器适配器去执行Handler

第五步：处理器适配器去执行Handler

第六步：Handler执行完成给适配器返回ModelAndView

第七步：处理器适配器向前端控制器返回ModelAndView

​         ModelAndView是springmvc框架的一个底层对象，包括 Model和view

第八步：前端控制器请求视图解析器去进行视图解析

​         根据逻辑视图名解析成真正的视图(jsp)

第九步：视图解析器向前端控制器返回View

第十步：前端控制器进行视图渲染

​         视图渲染将模型数据(在ModelAndView对象中)填充到request域

第十一步：前端控制器向用户响应结果 

 

 

组件：

1、前端控制器DispatcherServlet（不需要程序员开发）

作用接收请求，响应结果，相当于转发器，中央处理器。

有了DispatcherServlet减少了其它组件之间的耦合度。

 

2、处理器映射器HandlerMapping(不需要程序员开发)

作用：根据请求的url查找Handler

 

 

3、处理器适配器HandlerAdapter

作用：按照特定规则（HandlerAdapter要求的规则）去执行Handler

 

4、处理器Handler(需要程序员开发)

注意：编写Handler时按照HandlerAdapter的要求去做，这样适配器才可以去正确执行Handler

 

5、视图解析器View resolver(不需要程序员开发)

作用：进行视图解析，根据逻辑视图名解析成真正的视图（view）

 

6、视图View(需要程序员开发jsp)

View是一个接口，实现类支持不同的View类型（jsp、freemarker、pdf...）

 

## 1.4  为什么学SpringMVC?

 

| **对比项目** | **SrpingMVC**                                | **Struts2**                                      | **优势**                                                     |
| ------------ | -------------------------------------------- | ------------------------------------------------ | ------------------------------------------------------------ |
| 国内市场情况 | 有大量用户，一般新项目启动都会选用springmvc  | 有部分老用户，老项目组，由于习惯了，一直在使用。 | 国内情况，springmvc的使用率已经超过Struts2                   |
| 框架入口     | 基于servlet                                  | 基于filter                                       | 本质上没太大优势之分，只是配置方式不一样                     |
| 框架设计思想 | 控制器基于方法级别的拦截，处理器设计为单实例 | 控制器基于类级别的拦截，   处理器设计为多实例    | 由于设计本身原因，造成了Struts2，通常来讲只能设计为多实例模式，相比于springmvc设计为单实例模式，Struts2会消耗更多的服务器内存。 |
| 参数传递     | 参数通过方法入参传递                         | 参数通过类的成员变量传递                         | Struts2通过成员变量传递参数，导致了参数线程不安全，有可能引发并发的问题。 |
| 与spring整合 | 与spring同一家公司，可以与spring无缝整合     | 需要整合包                                       | Springmvc可以更轻松与spring整合                              |

## 1.5  springmvc和struts2的区别 

 

1、springmvc基于方法开发的，struts2基于类开发的。

 

springmvc将url和controller方法映射。映射成功后springmvc生成一个Handler对象，对象中只包括了一个method。

方法执行结束，形参数据销毁。

springmvc的controller开发类似service开发。

 

2、springmvc可以进行单例开发，并且建议使用单例开发，struts2通过类的成员变量接收参数，无法使用单例，只能使用多例。

 

3、经过实际测试，struts2速度慢，在于使用struts标签，如果使用struts建议使用jstl。

 

# 2       入门程序

## 2.1  创建Dynamic web项目

## 2.2  导入springmvc的jar包

## 2.3  编写TestController类

```java
@Controller

public class HelloController {


    @RequestMapping("hello")
    public ModelAndView hello(){
       System.out.println("hello springmvc....");
       //创建ModelAndView对象
       ModelAndView mav = new ModelAndView();
       //设置模型数据
       mav.addObject("msg", "hello springmvc...");
       //设置视图名字
       mav.setViewName("/WEB-INF/jsp/hello.jsp");
       return mav;
    }
}
```



## 2.4  创建hello.jsp页面

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
${msg}
</body>
</html>
```



## 2.5  创建与配置springmvc.xml核心配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>

<beans xmlns="http://www.springframework.org/schema/beans"

    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:p="http://www.springframework.org/schema/p"

    xmlns:context="http://www.springframework.org/schema/context"

    xmlns:mvc="http://www.springframework.org/schema/mvc"

    xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.0.xsd

        http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc-4.0.xsd

        http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.0.xsd">

    

    <!-- 配置@Controller处理器，包扫描器 -->
    <context:component-scan base-package="com.zxw.controller" />

</beans>

```



## 2.6  在web.xml中配置前端控制器

```xml
<!--核心控制器的配置  -->
  <servlet>
  	<servlet-name>springmvc</servlet-name>
  	<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
  	
  	<!-- 加载springmvc核心配置文件 
		contextConfigLocation : 配置springmvc加载的配置文件（配置处理器，映射器）
		如果不配置contextConfigLocation，默认加载的是WEB-INF/servlet名称-servlet.xml
	-->
  	<init-param>
  		<param-name>contextConfigLocation</param-name>
  		<param-value>classpath:springmvc.xml</param-value>
  	</init-param>
  </servlet>
  
<!--
  第一种*.do，访问以.do结尾的由DispatcherServlet进行解析
  第二种：/ 所有的访问地址都由DispatcherServlet进行解析，对于静态文件的解析需要配置不让					DispatcherServlet解析
  第三种：/* 拦截使有的请求包括jsp
-->
  <servlet-mapping>
  	<servlet-name>springmvc</servlet-name>
  	<url-pattern>*.do</url-pattern>
  </servlet-mapping>
```



## 2.7  启动项目通过浏览器测试

http://localhost:8080/SpringMVC01/hello.do



# 3  SpringMVC架构



## 3.1  处理器映射器与处理器适配器



### 3.1.1 处理器映射器

从spring3.1版本开始，废除了DefaultAnnotationHandlerMapping的使用，推荐使用RequestMappingHandlerMapping完成注解式处理器映射。

 ```xml
<!-- 配置处理器映射器 -->
<bean class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerMapping"/>
 ```



### 3.1.2 处理器适配器

从spring3.1版本开始，废除了AnnotationMethodHandlerAdapter的使用，推荐使用RequestMappingHandlerAdapter完成注解式处理器适配。

```xml
<!-- 处理器适配器 -->

<bean class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter"/>

```



### 3.1.3 小结

映射器与适配器必需配套使用，如果映射器使用了推荐的RequestMappingHandlerMapping，适配器也必需使用推荐的RequestMappingHandlerAdapter。



### 3.1.4 注解驱动

```xml
<!-- 注解驱动配置，代替映射器与适配器的单独配置，同时支持json响应(推荐使用) -->

<mvc:annotation-driven />

```



## 3.2  视图解析器

```xml
<!-- 配置视图解析器 -->
<bean class=*"org.springframework.web.servlet.view.InternalResourceViewResolver"*>

       <!-- 配置视图响应的前缀 -->
       <property name="prefix" value="/WEB-INF/jsp/" />
    
       <!-- 配置视图响应的后缀 -->
       <property name="suffix" value=".jsp"/>
</bean>
```

 

## 3.3     总结-springMVC架构

![img](file:///C:\Users\ADMINI~1\AppData\Local\Temp\msohtmlclip1\01\clip_image008.jpg)

 



# 4     springmvc和mybatis整合



springmvc+mybaits的系统架构：

![img](file:///C:\Users\ADMINI~1\AppData\Local\Temp\msohtmlclip1\01\clip_image055.gif)



第一步：整合dao层

​         mybatis和spring整合，通过spring管理mapper接口。

​         使用mapper的扫描器自动扫描mapper接口在spring中进行注册。

第二步：整合service层

​         通过spring管理 service接口。

​         使用配置方式将service接口配置在spring配置文件中。

​         实现事务控制。 

第三步：整合springmvc

​         由于springmvc是spring的模块，不需要整合。

  思路

**Dao层：**

* SqlMapConfig.xml，空文件即可。需要文件头。
* applicationContext-dao.xml。
  * a)         数据库连接池
  * b)         SqlSessionFactory对象，需要spring和mybatis整合包下的。
  * c)         配置mapper文件扫描器。 

**Service层：**

* 1、applicationContext-service.xml包扫描器，扫描@service注解的类。
* 2、applicationContext-trans.xml配置事务。

**Controller层：**

Springmvc.xml

* 1、包扫描器，扫描@Controller注解的类。
* 2、配置注解驱动。
* 3、视图解析器 

**Web.xml**

* 配置spring容量监听器
* 配置前端控制器



  

# 5   controller方法的返回值

 

## 5.1返回ModelAndView

需要方法结束时，定义ModelAndView，将model和view分别进行设置。

 

## 5.2返回string

如果controller方法返回string，

 

1、表示返回逻辑视图名。

真正视图(jsp路径)=前缀+逻辑视图名+后缀

```java
@RequestMapping(value="item" method={RequestMethod.POST,RequestMethod.get})
public String item(Model model) throws Exceptvion{
    //调用service根据商品id查询商品信息
    Item item = itemService.finfItemById(1);
    
    //通过形参中的Model将model数据传到页面相对于modelAndView.addObject()方法
    model.addAttribute("item",item);
    return "editItem";
}
```



2、redirect重定向

商品修改提交后，重定向到商品查询列表。

redirect重定向特点：浏览器地址栏中的url会变化。修改提交的request数据无法传到重定向的地址。因为重定向后重新进行request（request无法共享）

```java
//重定向到商品查询列表
return "redirect:queryItems.action";
```



3、forward页面转发

通过forward进行页面转发，浏览器地址栏url不变，request可以共享。

```java
//页面转发
return "forward:queryItems.action"; 
```



## 5.2返回void

 

在controller方法形参上可以定义request和response，使用request或response指定响应结果：

1、使用request转向页面，如下：

```java
request.getRequestDispatcher("页面路径").forward(request, response);
```



2、也可以通过response页面重定向：

```java
response.sendRedirect("url")
```



3、也可以通过response指定响应结果，例如响应json数据如下：

```java
response.setCharacterEncoding("utf-8");

response.setContentType("application/json;charset=utf-8");

response.getWriter().write("json串");

```



 

#  6    参数绑定

 

## 6.1spring参数绑定过程

 

从客户端请求key/value数据，经过参数绑定，将key/value数据绑定到controller方法的形参上。

 

springmvc中，接收页面提交的数据是通过方法形参来接收。而不是在controller类定义成员变更接收！！！！

![img](file:///C:\Users\ADMINI~1\AppData\Local\Temp\msohtmlclip1\01\clip_image100.gif)

 

## 6.2默认支持的类型

直接在controller方法形参上定义下边类型的对象，就可以使用这些对象。在参数绑定过程中，如果遇到下边类型直接进行绑定。

#### 6.2.1 HttpServletRequest

通过request对象获取请求信息

#### 6.2.2 HttpServletResponse

通过response处理响应信息

#### 6.2.3 HttpSession

通过session对象得到session中存放的对象

#### 6.2.4 Model/ModelMap

model是一个接口，modelMap是一个接口实现 。

作用：将model数据填充到request域。

 

## 6.3简单类型



通过@RequestParam对简单类型的参数进行绑定。

如果不使用@RequestParam，要求request传入参数名称和controller方法的形参名称一致，方可绑定成功。

如果使用@RequestParam，不用限制request传入参数名称和controller方法的形参名称一致。

 通过required属性指定参数是否必须要传入，如果设置为true，没有传入参数，报下边错误：

Required Integer  parameter "id" is not present



```java
/**
	 * 演示简单参数传递
	 * 跳转修改商品信息页面
	 * @RequestParam用法：入参名字与方法名参数名不一致时使用{
	 * 	value:传入的参数名，required：是否必填,defaultValue:默认值
	 * }
	 * 
	 */
	@RequestMapping("itemEdit")
	public ModelAndView itemEdit(@RequestParam(value="id",required=true,defaultValue="1")Integer ids){
		ModelAndView mav = new ModelAndView();
		
		//查询商品信息
		Item item = itemServices.getItemById(ids);
		//设置商品数据返回页面
		mav.addObject("item", item);
		//设置视图名称
		mav.setViewName("itemEdit");
		return mav;
	}
```

 

## 6.4pojo绑定

页面中input的name和controller的pojo形参中的属性名称一致，将页面中数据绑定到pojo。



绑定包装的pojo        **要点：通过点(.)传递属性。**



 ```java
要点：表单提交的name属性必需与pojo的属性名称一致。

/**
	 * 演示传递pojo参数
	 * 更新商品信息
	 * @return
	 */
	@RequestMapping("updateItem")
	public String updateItem(Item item,Model model){
		//更新商品
		itemServices.update(item);
		//返回商品模型
		model.addAttribute("item", item);
		//返回担任提示
		model.addAttribute("msg", "修改商品成功");
		//返回修改商品页面
		return "itemEdit";
	}

 ```



## 6.5自定义参数绑定实现日期类型绑定

 

对于controller形参中pojo对象，如果属性中有日期类型，需要自定义参数绑定。

将请求日期数据串传成 日期类型，要转换的日期类型和pojo中日期属性的类型保持一致。

```java
public class Item{
    Private Integer id;
    .....
    private Date createtime;
}
```

 

所以自定义参数绑定将日期串转成java.util.Date类型。

 

需要向处理器适配器中注入自定义的参数绑定组件。

 

### 6.5.1              自定义日期类型绑定



```java
public class CustomDateConverter implement Converter<String , Date>{
    
    public Date convert(String source){
        
        //实现将日期串转换成日期类型（格式：yyyy--MM-dd HH:mm:ss)
        SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        
        try{
            retrun simpleDateFormat.parse(source);
        }catch(Parseexception e){
            e.printStackTrace();
        }
        //如果参数绑定失败返回null
        return null；
    }
}
```



### 12.5.2              配置方式

 ```xml
<!--自定义参数绑定-->

<bean id = "conversionService" class="org.springframework.format.support.FormattingConversionServiceFactoryBean"
      <property name="converters">
		<list>
			<!--日期类转换-->
            <bean class="com.zxw.util.CustomDateConverter"/>
		</list>
	</property>
</bean>
 ```



# 7        问题

 

## 7.1post乱码

 

在web.xml添加post乱码filter

 

在web.xml中加入：

```xml
<filter>
    <filter-name>CharacterEncodingFilter</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    <init-param>
        <param-name>encoding</param-name>
        <param-value>utf-8</param-value>
    </init-param>
</filter>

<filter-mapping>
    <filter-name>CharacterEncodingFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```



以上可以解决post请求乱码问题。

对于get请求中文参数出现乱码解决方法有两个：



修改tomcat配置文件添加编码与工程编码一致，如下：

 ```xml
<Connector URIEncoding="utf-8" connectionTimeout="20000" port="8080" protocol="HTTP/1.1" redirectPort="8443"/>
 ```



另外一种方法对参数进行重新编码：

```java
String userName = new String(request.getParamter("userName").getBytes("ISO8859-1"),"utf-8")
```

ISO8859-1是tomcat默认编码，需要将tomcat编码后的内容按utf-8编码

 

 

 