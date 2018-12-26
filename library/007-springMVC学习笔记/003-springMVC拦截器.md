# 1       拦截器

## 1.1     拦截器开发流程

### 1.1.1  创建拦截器

#### 1.1.1.1 一号拦截器

```java
//自定义拦截器
public class MyInterceptor1 implements HandlerInterceptor {

    //在Controller方法执行后被执行
   //处理异常、记录日志
    @Override
    public void afterCompletion(HttpServletRequest arg0, HttpServletResponse arg1, Object arg2, Exception arg3)throws Exception {
       System.out.println("MyInterceptor1.afterCompletion.....");
    }

    //在Controller方法执行后，返回ModelAndView之前被执行
    //设置或者清理页面共用参数等等
    @Override
    public void postHandle(HttpServletRequest arg0, HttpServletResponse arg1, Object arg2, ModelAndView arg3) throws Exception {
       System.out.println("MyInterceptor1.postHandle.....");
    }

 

    //在Controller方法执行前被执行
   //登录拦截、权限认证等等
    @Override
    public boolean preHandle(HttpServletRequest arg0, HttpServletResponse arg1, Object arg2) throws Exception {

       System.out.println("MyInterceptor1.preHandle.....");

       //返回true放行，false拦截
       return true;

    }

}
```





#### 1.1.1.2 二号拦截器，复制一号，修改一下系统输出消息

 

### 1.1.2  配置拦截器

```xml
<!-- 拦截器定义 -->
<mvc:interceptors>
    <!-- 定义一个拦截器 -->
    <mvc:interceptor>
        <!-- path配置</**>拦截所有请求，包括二级以上目录，</*>拦截所有请求，不包括二级以上目录 -->
        <mvc:mapping path="/**"/>
        <bean class="com.itheima.springmvc.interceptor.MyInterceptor1" />
    </mvc:interceptor>

    <!-- 定义一个拦截器 -->
    <mvc:interceptor>
        <!-- path配置</**>拦截所有请求，包括二级以上目录，</*>拦截所有请求，不包括二级以上目录 -->
        <mvc:mapping path="/**"/>
        <bean class="com.itheima.springmvc.interceptor.MyInterceptor2" />
    </mvc:interceptor>
</mvc:interceptors>
```



 

### 1.1.3  测试流程，可参考教案

![img](file:///C:\Users\ADMINI~1\AppData\Local\Temp\msohtmlclip1\01\clip_image002.jpg)

 

## 1.2     拦截器案例应用，登录拦截器

### 1.2.1  思路

1、有一个登录页面，需要写一个controller访问页面

2、登录页面有一提交表单的动作。需要在controller中处理。

a)         判断用户名密码是否正确

b)         如果正确 想session中写入用户信息

c)         返回登录成功，或者跳转到商品列表

3、拦截器。

a)         拦截用户请求，判断用户是否登录

b)         如果用户已经登录。放行

c)         如果用户未登录，跳转到登录页面。

 

### 1.2.2  login.jsp

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>用户登录</title>
</head>
<body>
<form action="${pageContext.request.contextPath }/user/login.action">
用户名：<input type="text" name="username" /><br>
密码：<input type="password" name="password" /><br>
<input type="submit">
</form>
</body>
</html>

```



### 1.2.3  UserController

```java
/**
 * 登录拦截器
 * @author Steven
 *
 */
@Controller
@RequestMapping("user")
public class UserController {
	
	/**
	 * 跳转登录页面
	 * @return
	 */
	@RequestMapping("toLogin")
	public String toLogin(){
		return "login";
	}
	
	/**
	 * 用户登录
	 * @return
	 */
	@RequestMapping("login")
	public String login(String username,String password,HttpSession session){
		
		String result = "login";
		
		//断定用户是否允许登录
		if(username != null){
			session.setAttribute("username", username);
			//登录成功，跳转商品列表
			result = "redirect:/itemList.action";
		}
		return result;
	}
}

```

 

### 1.2.4  LoginInterceptor拦截器编码

*这里省略类声名和其它两个方法。*

 ```java
//处理执行前被执行
	//登录拦截、权限验证
	@Override
	public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object arg2) throws Exception {
		
		Object attribute = request.getSession().getAttribute("username");
		if(attribute != null){
			//放行，执行处理器
			return true;
		}else{
			response.sendRedirect(request.getContextPath() + "/user/tologin.action");
			//放行，执行处理器
			return false;
		}
	}

 ```



### 1.2.5  拦截器配置

```xml
<!-- 登录拦截器 -->
<mvc:interceptor>
    <!-- path配置</**>拦截所有请求，包括二级以上目录，</*>拦截所有请求，不包括二级以上目录 -->
    <mvc:mapping path="/**"/>
    <!-- 配置不拦截请求的地址 -->
    <!-- <mvc:exclude-mapping path="/user/toLogin.action"/>
   <mvc:exclude-mapping path="/user/login.action"/> -->
    <mvc:exclude-mapping path="/user/*"/>
    <bean class="com.itheima.springmvc.interceptor.LoginInterceptor" />
</mvc:interceptor>
```