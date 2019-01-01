# 一,thymeleaf-简单表达式

1.变量表达式 

2.选择或星号表达式 

3.文字国际化表达式 

4.URL表达式 

## 1,变量表达式

Thymeleaf模板引擎在进行模板渲染时，还会附带一个Context存放进行模板渲染的变量，在模板中定义的表达式本质上就是从Context中获取对应的变量的值

```html
<p>Today is: <span th:text="${day}">2 November 2016</span>.</p>
```

假设day的值为2016年11月2日，那么渲染结果为：<p>Today is: 2016年11月2日.</p>。
注意 : 渲染后,模板中span值2 November 2016将被覆盖

**th:value**

可以将一个值放入到 input 标签的 value 中

```
<input type="text" th:value="zhangzq" >
```



## 2,选择(星号)表达式

可以简单理解为内层是对外层对象的引用

```html
<div th:object="${session.user}">
    <p>Name: <span th:text="*{firstName}">Sebastian</span>.</p>
    <p>Surname: <span th:text="*{lastName}">Pepper</span>.</p>
    <p>Nationality: <span th:text="*{nationality}">Saturn</span>.</p>
</div
```

等同于以下方式:

```html
<div>
  <p>Name: <span th:text="${session.user.firstName}">Sebastian</span>.</p>
  <p>Surname: <span th:text="${session.user.lastName}">Pepper</span>.</p>
  <p>Nationality: <span th:text="${session.user.nationality}">Saturn</span>.</p
</div>
```

也可以混用,如下:

```html
<div th:object="${session.user}">
  <p>Name: <span th:text="*{firstName}">Sebastian</span>.</p>
  <p>Surname: <span th:text="${session.user.lastName}">Pepper</span>.</p>
  <p>Nationality: <span th:text="*{nationality}">Saturn</span>.</p>
</div>
```


如何没有与th:object结合使用，*{}与${}效果一样，因为其范围自动扩展到context。


## 3,URL表达式

URL表达式指的是把一个有用的上下文或会话信息添加到URL，这个过程经常被叫做URL重写。 
Thymeleaf对于URL的处理是通过语法@{…}来处理

```html
<!— 绝对路径 —>
<!-- Will produce 'http://localhost:8080/gtvg/order/details?orderId=3' (plus rewriting) -->
<a href="details.html" th:href="@{http://localhost:8080/gtvg/order/details(orderId=${o.id})}">view</a>

<!— 相对路径 带参数—>
<!-- Will produce '/gtvg/order/details?orderId=3' (plus rewriting) -->
<a href="details.html" th:href="@{/order/details(orderId=${o.id})}">view</a>

<!-- Will produce '/gtvg/order/3/details' (plus rewriting) -->
<a href="details.html" th:href="@{/order/{orderId}/details(orderId=${o.id})}">view</a>
1.一般传参
<a th:href="@{/freemarker(username=pkusoft,pwd=123456)}">相对路径</a><br/>
2.rest风格传参
<a th:href="@{/freemarker/{pwd}/(username=pkusoft,pwd=123456)}">rest风格</a><br/>

B.相对路径
1.相对于当前项目的根 相对于项目的上下文的相对路径 
<a th:href="@{/freemarker}">相对路径</a><br/>
2. 相对于服务器路径的根
<a th:href="@{~/hello/freemarker}">相对于服务器的根</a>

```



Thymeleaf支持相对路径和绝对路径
(orderId=${o.id})表示将括号内的内容作为URL参数处理
@{...}表达式中可以通过{orderId}访问Context中的orderId变量
@{/order}是Context相关的相对路径，在渲染时会自动添加上当前Web应用的Context名字，假设context名字为app，那么结果应该是/app/order


## 4,文字国际化表达式

文字国际化表达式允许我们从一个外部文件获取区域文字信息(.properties) 
使用Key-Value方式，还可以提供一组参数(可选).

.properties

```proper
{main.title}

{message.entrycreated(${entryId})}
```



模板引用:

```html
<table>
    <th th:text="#{header.address.city}">...</th>
    <th th:text="#{header.address.country}">...</th>
</table>
```



# 二.thymeleaf-字面值

　　1.文本文字：’one text’, ‘Another one!’,… 
　　2.文字数量：0, 34, 3.0, 12.3,… 
　　3.布尔型常量：true, false 
　　4.空的文字：null 
　　5.文字标记：one, sometext, main,… 
　　

# 三：thymeleaf-文本处理



## 1.字符串拼接：+

```html
<span th:text="'Welcome to our application, ' + ${user.name} + '!'">
```



## 2.文字替换：|The name is ${name}|

```html
<span th:text="|Welcome to our application, ${user.name}!|">
```


相比以上两种方式都可以实现字符串合并,但是,|…|中只能包含变量表达式${…}，不能包含其他常量、条件表达式等。



# 四.表达基本对象

　　1.#ctx:上下文对象

　　2.#vars:上下文变量

　　3.#locale:上下文语言环境

　　4.#httpServletRequest:(只有在Web上下文)HttpServletRequest对象

　　5.#httpSession:(只有在Web上下文)HttpSession对象。

例如:

```html
<span th:text="${#locale.country}">US</span>.

th:text="${#calendars.format(today,'dd MMMM yyyy')}"
```



# 五,表达式预处理



表达式预处理，它被定义在_之间：

```html
#{selection.__${sel.code}__}
```

${sel.code}将先被执行，结果(假如是AAA)将被看做表达式的一部分被执行
结果#{为selection.AAA}。


# 六,thymeleaf运算符



在表达式中可以使用各类算术运算符，例如+, -, *, /, %

```html
th:with="isEven=(${prodStat.count} % 2 == 0)"
```


逻辑运算符>, <, <=,>=，==,!=都可以使用 
需要注意的是使用 > ,<, >=, <=时需要用它的HTML转义符(> gt; < lt; >= ge; gte; <= le; lte; == eq; != ne; neq;)

```html
th:if="${prodStat.count} &gt; 1"
th:text="'Execution mode is ' + ( (${execMode} == 'dev')? 'Development' : 'Production')"
```


布尔运算符 and,or



# 七,thymeleaf循环



数据集合必须是可以遍历的，使用th:each标签：

```html
<body>
  <h1>Product list</h1>
  <table>
    <tr>
      <th>NAME</th>
      <th>PRICE</th>
      <th>IN STOCK</th>
    </tr>
    <tr th:each="prod : ${prods}">
      <td th:text="${prod.name}">Onions</td>
      <td th:text="${prod.price}">2.41</td>
      <td th:text="${prod.inStock}? #{true} : #{false}">yes</td>
    </tr>
  </table>
  <p>
    <a href="../home.html" th:href="@{/}">Return to home</a>
  </p>
</body>
```


被循环渲染的元素<tr>中加入th:each标签
th:each="prod : ${prods}"对集合变量prods进行遍历，对象prod在循环体中可通过表达式访问

## 1.th:each

```html
<table class="table" >
    <tr th:each=" name : ${arr} " ><td th:text="${name}" ></td></tr>
</table>

```

后台代码：

@RequestMapping("/thymeleaf")
public String freemarker(Model model){

```java
List<String> arr = new ArrayList<>();
arr.add("licm");
arr.add("zhangzq");
arr.add("springboot");
 
model.addAttribute("msg","zhangzq");
model.addAttribute("sex","男");
model.addAttribute("id","1");
model.addAttribute("arr",arr);
return "thy/thymeleaf";
```
}

## 2.th:each  状态变量

状态变量属性
1,index:当前迭代器的索引 从 0 开始

2,count:当前迭代对象的计数 从 1 开始

3,size:被迭代对象的长度

4,even/odd:布尔值，当前循环是否是偶数/奇数 从 0 开始

5,first:布尔值，当前循环的是否是第一条，如果是返回 true 否则返回 false

6,last:布尔值，当前循环的是否是最后一条，如果是则返回 true 否则返回 false

```html
<table class="table" >
    <tr>
        <th>name</th>
        <th>Index</th>
        <th>Count</th>
        <th>Size</th>
        <th>Even</th>
        <th>Odd</th>
        <th>First</th>
        <th>lase</th>
    </tr>
    <tr th:each=" name , var : ${arr} " >
        <td th:text="${name}" ></td>
        <td th:text="${var.index}"></td>
        <td th:text="${var.count}"></td>
        <td th:text="${var.size}"></td>
        <td th:text="${var.even}"></td>
        <td th:text="${var.odd}"></td>
        <td th:text="${var.first}"></td>
        <td th:text="${var.last}"></td>
    </tr>
</table>
```

## 3.th:each 迭代 Map

```html
<table class="table" >
    <tr>
        <th>键值对</th>
        <th>值</th>
    </tr>
    <tr th:each="maps : ${map}">
        <td th:text="${maps}"></td>
        <td th:each="entry:${maps}"  th:text="${entry.value }" ></td>
    </tr>
</table>
```



# 八,thymeleaf条件求值

## 1,If/Unless

Thymeleaf中使用th:if和th:unless属性进行条件判断

设置标签只有在th:if中条件成立时才显示:

```html
<a th:href="@{/login}" th:unless=${session.user != null}>Login</a>
```

th:unless与th:if相反，表达式条件不成立时显示内容。

## 2,Switch

多路选择Switch结构,默认属性default,用*表示

```html
<div th:switch="${user.role}">
  <p th:case="'admin'">User is an administrator</p>
  <p th:case="#{roles.manager}">User is a manager</p>
  <p th:case="*">User is some other thing</p>
</div>
```



## 3.三元运算符

三元运算控制class属性选择ml

```ht
<tr th:class="${row.even}? 'even' : 'odd'">
```



三元运算嵌套

```html
<tr th:class="${row.even}? (${row.first}? 'first' : 'even') : 'odd'">
```


还可以省略else部分，当表达式结果为false，返回null，否则返回’alt’

```html
<tr th:class="${row.even}? 'alt'">
    ...
</tr>
```


4.If-then: (if) ? (then) ,省略了else部分，如果条件不成立，返回null

如果第一个表达式的计算结果为null，则取第二个表达式的结果

```html
<div th:object="${session.user}">
    <p>Age: <span th:text="*{age}?: '(no age specified)'">27</span>.</p>
</div>
```


等效于：

```html
<p>Age: <span th:text="*{age != null}? *{age} : '(no age specified)'">27</span>.</p>
```


条件表达式嵌套：

```ht
<p>Name: <span th:text="*{firstName} ?: (*{admin} ? 'Admin' : #{default.username})">Sebastian</span>.</p>
```



# 九,Thymeleaf-Utilities

Thymeleaf提供了套Utility对象,内置于Context中,可通过#直接访问：

```properties
- #dates: java.util的实用方法。对象:日期格式、组件提取等.
- #calendars: 类似于#日期,但对于java.util。日历对象
- #numbers: 格式化数字对象的实用方法。
- #strings:字符串对象的实用方法:包含startsWith,将/附加等。
- #objects: 实用方法的对象。
- #bools: 布尔评价的实用方法。
- #arrays: 数组的实用方法。
- #lists: list集合。
- #sets:set集合。
- #maps: map集合。
- #aggregates: 实用程序方法用于创建聚集在数组或集合.
- #messages: 实用程序方法获取外部信息内部变量表达式,以同样的方式,因为他们将获得使用# {…}语法
- #ids: 实用程序方法来处理可能重复的id属性(例如,由于迭代)。
```

## 1.字符串操作

Thymeleaf 内置对象 注意语法：

 1，调用内置对象一定要用# 

2，大部分的内置对象都以 s 结尾 strings、numbers、dates 

```properties
${#strings.isEmpty(key)}===>判断字符串是否为空，如果为空返回 true，否则返回 false

${#strings.contains(msg,'T')}===>判断字符串是否包含指定的子串，如果包含返回 true，否则返回 false

${#strings.startsWith(msg,'a')} ===> 判断当前字符串是否以子串开头，如果是返回 true，否则返回 false

${#strings.endsWith(msg,'a')}  ===>  判断当前字符串是否以子串结尾，如果是返回 true，否则返回 false

${#strings.length(msg)}   ===>  返回字符串的长度

${#strings.indexOf(msg,'h')}   ===>    查找子串的位置，并返回该子串的下标，如果没找到则返回-1

${#strings.substring(msg,13)} ,{#strings.substring(msg,13,15)}    ===>    截取子串，用户与 jdkString 类下 SubString 方法相同

${#strings.toUpperCase(msg)}, ${#strings.toLowerCase(msg)}    ===>   字符串转大小写。

```



## 2.日期格式化处理

```properties
${#dates.format(key)}    ===>   格式化日期，默认的以浏览器默认语言为格式化标准

${#dates.format(key,'yyy/MM/dd')}    ===>   按照自定义的格式做日期转换

${#dates.year(key)} ${#dates.month(key)} ${#dates.day(key)} year ===> 取年 Month：取月 Day：取日
```

## 3.域对象操作

1.HttpServletRequest

```html
<p>Request:<span th:text="${#httpServletRequest.getAttribute('name')}"></span><br/></p>
```


2.HttpSession

```html
<p>Session:<span th:text="${session.sess}"></span><br/></p>
```


3.ServletContext

```html
<p>Application:<span th:text="${application.app}"></span></p>
```

# 十、demo入门

## 1.引入依赖

maven中直接引入

```pom
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-thymeleaf</artifactId>
    </dependency>1234
```

可以查看依赖关系,发现spring-boot-starter-thymeleaf下面已经包括了spring-boot-starter-web,所以可以把spring-boot-starter-web的依赖去掉.

## 2.配置视图解析器

spring-boot很多配置都有默认配置,比如默认页面映射路径为 
classpath:/templates/*.html 
同样静态文件路径为 
classpath:/static/

在application.properties中可以配置thymeleaf模板解析器属性.就像使用springMVC的JSP解析器配置一样

```properties
#thymeleaf start
spring.thymeleaf.mode=HTML5
spring.thymeleaf.encoding=UTF-8
spring.thymeleaf.content-type=text/html
#开发时关闭缓存,不然没法看到实时页面
spring.thymeleaf.cache=false
#thymeleaf end1234567
```

具体可以配置的参数可以查看 
`org.springframework.boot.autoconfigure.thymeleaf.ThymeleafProperties`这个类,上面的配置实际上就是注入到该类中的属性值.

## 3.编写DEMO

1.控制器

```Java
    @Controller
    public class HelloController {

        private Logger logger = LoggerFactory.getLogger(HelloController.class);
        /**
         * 测试hello
         * @return
         */
        @RequestMapping(value = "/hello",method = RequestMethod.GET)
        public String hello(Model model) {
            model.addAttribute("name", "Dear");
            return "hello";
        }

    }123456789101112131415
```

2.view(注释为IDEA生成的索引,便于IDEA补全)

```HTML
<!DOCTYPE HTML>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <title>hello</title>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
</head>
<body>
<!--/*@thymesVar id="name" type="java.lang.String"*/-->
<p th:text="'Hello！, ' + ${name} + '!'" >3333</p>
</body>
</html>
```


# 十一、${}、*{}、#{}、@{}、#maps

## 1、**${}**

变量表达式（美元表达式，哈哈），用于访问容器上下文环境中的变量，功能同jstl中${}。

例如：

```java
protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {

...

//Create Servlet context
WebContext ctx = new WebContext(req, resp, this.getServletContext(), req.getLocale());
ctx.setVariable("helloword","hello thymeleaf,wellcome!");
//Executing template engine
templateEngine.process("home", ctx, resp.getWriter());

}
```

模板页面访问变量

```
<p><span th:text="${helloword}"></span></p>
```

 

## 2、***{}**

选择表达式（星号表达式）。选择表达式与变量表达式有一个重要的区别：选择表达式计算的是选定的对象，而不是整个环境变量映射。也就是：只要是没有选择的对象，选择表达式与变量表达式的语法是完全一样的。那什么是选择的对象呢？是一个：th:object对象属性绑定的对象。

例如：

```html
<div th: object=" ${session.user}" >
	<p>Name: <span th: text=" *{firstName}" >Sebastian</span>. </p>
	<p>Surname: <span th: text=" *{lastName}" >Pepper</span>. </p>
	<p>Nationality: <span th: text=" *{nationality}" >Saturn</span>. </p>
</div>
```

上例中，选择表达式选择的是th:object对象属性绑定的session. user对象中的属性。

 

## 3、**#{}**

消息表达式（井号表达式，资源表达式）。通常与th:text属性一起使用，指明声明了th:text的标签的文本是#{}中的key所对应的value，而标签内的文本将不会显示。

例如：

新建/WEB-INF/templates/home.html，段落

```HTML
<p th: text=" #{home.welcome}" >This text will not be show! </p>
```

新建/WEB-INF/templates/home.properties，home.welcome：

```HTML
home.welcome=this messages is from home.properties!
```



测试结果可以看出，消息表达式通常用于显示页面静态文本，将静态文本维护在properties文件中也方面维护，做国际化等。



## 4、**@{}**

超链接url表达式。

例如：

```html
<script th:src="@{/resources/js/jquery/jquery.json-2.4.min.js}"
```



## 5、**#maps**

工具对象表达式。常用于日期、集合、数组对象的访问。这些工具对象就像是java对象，可以访问对应java对象的方法来进行各种操作。

例如：

```
<div th:if="${#maps.size(stuReqBean.students[__${rowStat.index}__].score) != 0}">
	<label>${score.key}:</label><input type="text" th:value="${score.value}"></input>
</div>

<div th:if="${#maps.isEmpty(stuReqBean.students[__${rowStat.index}__].score)}">
	...do something...
</div>
```

 