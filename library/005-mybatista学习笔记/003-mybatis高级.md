# 0. 回顾

## 0.1mybatis框架执行过程

1、配置mybatis的配置文件，SqlMapConfig.xml（名称不固定）

2、通过配置文件，加载mybatis运行环境，创建SqlSessionFactory会话工厂

​         SqlSessionFactory在实际使用时按单例方式。

3、通过SqlSessionFactory创建SqlSession

​         SqlSession是一个面向用户接口（提供操作数据库方法），实现对象是线程不安全的，建议sqlSession应用场合在方法体内。

4、调用sqlSession的方法去操作数据。

​         如果需要提交事务，需要执行SqlSession的commit()方法。

5、释放资源，关闭SqlSession

 

## 0.2  mybatis开发dao的方法

1、原始dao 的方法

​         需要程序员编写dao接口和实现类

​         需要在dao实现类中注入一个SqlSessionFactory工厂。

 

2、mapper代理开发方法（建议使用）

​         只需要程序员编写mapper接口（就是dao接口）

​         程序员在编写mapper.xml(映射文件)和mapper.java需要遵循一个开发规范：

​         1、mapper.xml中namespace就是mapper.java的类全路径。

​         2、mapper.xml中statement的id和mapper.java中方法名一致。

​         3、mapper.xml中statement的parameterType指定输入参数的类型和mapper.java的方法输入参数类型一致。

​         4、mapper.xml中statement的resultType指定输出结果的类型和mapper.java的方法返回值类型一致。

 

## 0.3 输入输出映射

输入映射：

​         parameterType：指定输入参数类型可以简单类型、pojo、hashmap。

​         对于综合查询，建议parameterType使用包装的pojo，有利于系统 扩展。 

输出映射：

​         resultType：

​                   查询到的列名和resultType指定的pojo的属性名一致，才能映射成功。

​         reusltMap：

​                   可以通过resultMap 完成一些高级映射。

​                如果查询到的列名和映射的pojo的属性名不一致时，通过resultMap设置列名和属性名之间的对应关系（映射关系）。可以完成映射。

​                   高级映射：

​                            将关联查询的列映射到一个pojo属性中。（一对一）

​                            将关联查询的列映射到一个List<pojo>中。（一对多）



 

# 1   订单商品数据模型

 

 

## 1.1     数据模型分析思路

1、每张表记录的数据内容

​         分模块对每张表记录的内容进行熟悉，相当 于你学习系统 需求（功能）的过程。

2、每张表重要的字段设置

​         非空字段、外键字段

3、数据库级别表与表之间的关系

​         外键关系

4、表与表之间的业务关系

​         在分析表与表之间的业务关系时一定要建立 在某个业务意义基础上去分析。

 

## 1.2     数据模型分析

用户表user：

​         记录了购买商品的用户信息

订单表：orders

​         记录了用户所创建的订单（购买商品的订单）

订单明细表：orderdetail：

​         记录了订单的详细信息即购买商品的信息

商品表：items

​         记录了商品信息

表与表之间的业务关系：

​         在分析表与表之间的业务关系时需要建立 在某个业务意义基础上去分析。



​先分析数据级别之间有关系的表之间的业务关系：         

usre和orders：

user----------->orders：一个用户可以创建多个订单，一对多

orders------------->user：一个订单只由一个用户创建，一对一



orders和orderdetail：

orders ----------------> orderdetail：一个订单可以包括 多个订单明细，因为一个订单可以购买多个商品，每个商品的购买信息在orderdetail记录，一对多关系

orderdetail--> orders：一个订单明细只能包括在一个订单中，一对一



orderdetail和itesm：

orderdetail-------------->itesms：一个订单明细只对应一个商品信息，一对一

items--------------> orderdetail:一个商品可以包括在多个订单明细 ，一对多

 

再分析数据库级别没有关系的表之间是否有业务关系：

orders和items：

orders和items之间可以通过orderdetail表建立 关系。

 

# 2       一对一查询

 

## 2.1     需求

 

查询订单信息，关联查询创建订单的用户信息

 

## 2.2     resultType

 

### 2.2.1     sql语句

 

 

确定查询的主表：订单表

确定查询的关联表：用户表

​         关联查询使用内链接？还是外链接？

​         由于orders表中有一个外键（user_id），通过外键关联查询用户表只能查询出一条记录，可以使用内链接。

 

SELECT 

  orders.*,

  USER.username,

  USER.sex,

  USER.address 

FROM

  orders,

  USER 

WHERE orders.user_id = user.id

 

### 2.2.2     创建pojo

将上边sql查询的结果映射到pojo中，pojo中必须包括所有查询列名。

原始的Orders.java不能映射全部字段，需要新创建的pojo。

创建 一个pojo继承包括查询字段较多的po类。

 ```java
public class OrdersCustom extends Orders {

	private String username;// 用户名称
	private String address;// 用户地址

    get/set。。。。
 ```



### 2.2.3     mapper.xml

```xml
<!-- 查询所有订单信息 -->
<select id="findOrdersList" resultType="com.zxw.po.OrdersCustom">
	SELECT
        orders.*,
        user.username,
        user.address
	FROM
		orders,	user
	WHERE orders.user_id = user.id 
</select>
```



### 2.2.4     mapper.java

```java
public List<OrdersCustom> findOrdersList() throws Exception;
```



### 2.2.5   测试

 ```java
Public void testfindOrdersList()throws Exception{

       //获取session
       SqlSession session = sqlSessionFactory.openSession();

       //获限mapper接口实例
       UserMapper userMapper = session.getMapper(UserMapper.class);

       //查询订单信息
       List<OrdersCustom> list = userMapper.findOrdersList();
       System.out.println(list);

       //关闭session
       session.close();

    }
 ```



### 2.2.6 总结

​         

定义专门的po类作为输出类型，其中定义了sql查询结果集所有的字段。此方法较为简单，企业中使用普遍



## 2.3     resultMap

 

### 2.3.1     sql语句

同resultType实现的sql

 

### 2.3.2     使用resultMap映射的思路

 

使用resultMap将查询结果中的订单信息映射到Orders对象中，在orders类中添加User属性，将关联查询出来的用户信息映射到orders对象中的user属性中。

 

### 2.3.3     需要Orders类中添加user属性

```java
public class Orders{
    private Integer id ;
    private Integer userId;
    private String numter;
    private Date createtime;
    
    private User user;
} 
```



### 2.3.4     mapper.xml



#### 2.3.4.1      定义resultMap

 ```xml
<!-- 
	订单查询关联用户的resultMap
  	将整个查询的结果映射到cn.zxw.po.Orders中
-->

  <resultMap type="com.zxw.po.Orders" id="userordermap">

     <!-- 配置映射的订单信息 -->
     <!-- id：指定查询列中的唯一标识，订单信息的中的唯一标识，如果有多个列组成唯一标识，配置多个id
       	 column：订单信息的唯一标识列
       	 property：订单信息的唯 一标识 列所映射到Orders中哪个属性
       -->

     <id column="id" property="id"/>
     <result column="user_id" property="userId"/>
     <result column="number" property="number"/>
     <result column="createtime" property="createtime"/>
     <result column="note" property="note"/>

    
     <!-- 配置映射的关联的用户信息 -->
     <!-- association：用于映射关联查询单个对象的信息
     	  property：要将关联查询的用户信息映射到Orders中哪个属性
      -->
     <association property="user"  javaType="com.zxw.po.User">
        <!-- id：关联查询用户的唯 一标识
       		 column：指定唯 一标识用户信息的列
      		 javaType：映射到user的哪个属性
         -->
        <id column="user_id" property="id"/>
        <result column="username" property="username"/>
        <result column="sex" property="sex"/>
        <result column="address" property="address"/>
     </association>
  </resultMap>
 ```

 

#### 2.3.4.2   statement定义



 ```java
<select id="findOrdersListResultMap" resultMap="userordermap">
	SELECT
        orders.*,
        user.username,
        user.address
	FROM
		orders,	user
	WHERE orders.user_id = user.id 
</select>
 ```



### 2.3.5     mapper.java

```java
public List<Orders> findOrdersListResultMap() throws Exception;
```



## 2.4     resultType和resultMap实现一对一查询小结

 

实现一对一查询：

resultType：使用resultType实现较为简单，如果pojo中没有包括查询出来的列名，需要增加列名对应的属性，即可完成映射。

如果没有查询结果的特殊要求建议使用resultType。

 

resultMap：需要单独定义resultMap，实现有点麻烦，如果对查询结果有特殊的要求，使用resultMap可以完成将关联查询映射pojo的属性中。

 

resultMap可以实现延迟加载，resultType无法实现延迟加载。

 

# 3       一对多查询

 

## 3.1     需求

查询订单及订单明细的信息。

 

## 3.2     sql语句

确定主查询表：订单表

确定关联查询表：订单明细表

在一对一查询基础上添加订单明细表关联即可。

 ```sql
SELECT 
      orders.*,
      USER.username,
      USER.sex,
      USER.address,
      orderdetail.id orderdetail_id,
      orderdetail.items_id,
      orderdetail.items_num,
      orderdetail.orders_id
FROM
      orders,
      USER,
      orderdetail
WHERE orders.user_id = user.id AND orderdetail.orders_id=orders.id
 ```



## 3.3     分析

使用resultType将上边的 查询结果映射到pojo中，订单信息的就是重复。

 

要求：

对orders映射不能出现重复记录。

在orders.java类中添加List<orderDetail> orderDetails属性。

最终会将订单信息映射到orders中，订单所对应的订单明细映射到orders中的orderDetails属性中。

 映射成的orders记录数为两条（orders信息不重复）

每个orders中的orderDetails属性存储了该 订单所对应的订单明细。

 

## 3.4     在orders中添加list订单明细属性

 

```java
public class Orders{
    private Integer id ;
    private Integer userId;
    private String numter;
    private Date createtime;
    
    private User user;
    
    private List<Orderdetail> Orderdetais
} 
```



 

## 3.5     mapper.xml

 

```xml
<!-- 查询所有订单信息 -->
<select id="OrdersAndOrderDetailResultMap" resultMap="com.zxw.po.OrdersCustom">
	SELECT 
      orders.*,
      USER.username,
      USER.sex,
      USER.address,
      orderdetail.id orderdetail_id,
      orderdetail.items_id,
      orderdetail.items_num,
      orderdetail.orders_id
    FROM
        orders,
        USER,
        orderdetail
    WHERE orders.user_id = user.id AND orderdetail.orders_id=orders.id
</select>
```



## 3.6     resultMap定义

```java
<!-- 
    订单及订单明细的resultMap
    使用extends继承，不用在中配置订单信息和用户信息的映射
-->
<resultMap type="com.zxw.po.Orders" id="OrdersAndOrderDetailResultMap" extends="OrdersUserResultMap">

      <!-- 订单信息 -->
      <!-- 用户信息 -->
      <!-- 使用extends继承，不用在中配置订单信息和用户信息的映射 -->

      <!-- 订单明细信息
          一个订单关联查询出了多条明细，要使用collection进行映射
          collection：对关联查询到多条记录映射到集合对象中
          property：将关联查询到多条记录映射到com.zxw.po.Orders哪个属性
          ofType：指定映射到list集合属性中pojo的类型
       -->

       <collection property="orderdetails" ofType="com.zxw.po.Orderdetail">
         <!-- id：订单明细唯 一标识
         	property:要将订单明细的唯 一标识 映射到cn.itcast.mybatis.po.Orderdetail的哪个属性
          -->

         <id column="orderdetail_id" property=*"id"/>
         <result column="items_id" property=*"itemsId"/>
         <result column="items_num" property=*"itemsNum"/>
         <result column="orders_id" property=*"ordersId"/>
       </collection>
   </resltMap>
```



## 3.7     mapper.java

```java
public List<Orders> OrdersAndOrderDetailResultMap() throws Exception;
```

 

## 3.8     小结

mybatis使用resultMap的collection对关联查询的多条记录映射到一个list集合属性中。

 

使用resultType实现：

将订单明细映射到orders中的orderdetails中，需要自己处理，使用双重循环遍历，去掉重复记录，将订单明细放在orderdetails中。

 

# 4       多对多查询

 

## 4.1     需求

查询用户及用户购买商品信息。

 

## 4.2     sql语句

查询主表是：用户表

关联表：由于用户和商品没有直接关联，通过订单和订单明细进行关联，所以关联表：

orders、orderdetail、items

```sql
SELECT 
  orders.*,
  USER.username,
  USER.sex,
  USER.address,
  orderdetail.id orderdetail_id,
  orderdetail.items_id,
  orderdetail.items_num,
  orderdetail.orders_id,
  items.name items_name,
  items.detail items_detail,
  items.price items_price
FROM
  orders,
  USER,
  orderdetail,
  items
WHERE orders.user_id = user.id AND orderdetail.orders_id=orders.id AND orderdetail.items_id = items.id
```



## 4.3     映射思路

 

将用户信息映射到user中。

在user类中添加订单列表属性List<Orders> orderslist，将用户创建的订单映射到orderslist

在Orders中添加订单明细列表属性List<OrderDetail>orderdetials，将订单的明细映射到orderdetials

在OrderDetail中添加Items属性，将订单明细所对应的商品映射到Items

 

## 4.4     mapper.xml

```xml
<!-- 查询所有订单信息 -->
<select id="findOrdersAndOrderDetailResultMap" resultMap="com.zxw.po.OrdersCustom">
	sql语句
</select>
```



## 4.5     resultMap定义

```xml
<!-- 查询用户及购买的商品 -->
   <resultMap type="com.zxw.po.User"id="UserAndItemsResultMap">

      <!-- 用户信息 -->
      <id column="user_id" property="id"/>
      <result column="username" property="username"/>
      <result column="sex" property="sex"/>
      <result column="address" property="address"/>

      <!-- 订单信息
    	  一个用户对应多个订单，使用collection映射
       -->
       <collection property="ordersList" ofType=*"com.zxw.po.Orders"*>
         <id column="id" property="id"/>
         <result column="user_id" property="userId"/>
         <result column="number" property="number"/>
         <result column="createtime" property="createtime"/>
         <result column="note" property="note"/>     
          <!-- 订单明细 一个订单包括 多个明细-->
         <collection property="orderdetails" ofType="com.zxw.po.Orderdetail">
            <id column="orderdetail_id" property="id"/>
            <result column="items_id" property="itemsId"/>
            <result column="items_num" property="itemsNum"/>
            <result column="orders_id" property="ordersId"/>       
            <!-- 商品信息 一个订单明细对应一个商品 -->
            <association property="items" javaType="com..po.Items">
               <id column="items_id" property="id"/>
               <result column="items_name" property="name"/>
               <result column="items_detail" property="detail"/>
               <result column="items_price" property="price"/>
            </association>
         </collection>
       </collection>
   </resultMap>
```





## 4.6     mapper.java

```java
public List<User> findOrdersAndOrderDetailResultMap() throws Exception;
```



 

## 4.7     多对多查询总结

 

将查询用户购买的商品信息明细清单，（用户名、用户地址、购买商品名称、购买商品时间、购买商品数量）

针对上边的需求就使用resultType将查询到的记录映射到一个扩展的pojo中，很简单实现明细清单的功能。 

一对多是多对多的特例，如下需求：

查询用户购买的商品信息，用户和商品的关系是多对多关系。



需求1：

查询字段：用户账号、用户名称、用户性别、商品名称、商品价格(最常见)

企业开发中常见明细列表，用户购买商品明细列表，

使用resultType将上边查询列映射到pojo输出。



 需求2：

查询字段：用户账号、用户名称、购买商品数量、商品明细（鼠标移上显示明细）

使用resultMap将用户购买的商品明细列表映射到user对象中。

 

总结：

使用resultMap是针对那些对查询结果映射有特殊要求的功能，，比如特殊要求映射成list中包括 多个list。

 

 

# 5       resultMap总结

 

* resultType：

  作用：

  ​         将查询结果按照sql列名pojo属性名一致性映射到pojo中。

  场合：

  ​         常见一些明细记录的展示，比如用户购买商品明细，将关联查询信息全部展示在页面时，此时可直接使用resultType将每一条记录映射到pojo中，在前端页面遍历list（list中是pojo）即可。

 

* resultMap：

​         使用association和collection完成一对一和一对多高级映射（对结果有特殊的映射要求）。 

association：

作用：

​         将关联查询信息映射到一个pojo对象中。

场合：

​         为了方便查询关联信息可以使用association将关联订单信息映射为用户对象的pojo属性中，比如：查询订单及关联用户信息。

​         使用resultType无法将查询结果映射到pojo对象的pojo属性中，根据对结果集查询遍历的需要选择使用resultType还是resultMap。

​         

collection：

作用：

​         将关联查询信息映射到一个list集合中。

场合：

​         为了方便查询遍历关联信息可以使用collection将关联信息映射到list集合中，比如：查询用户权限范围模块及模块下的菜单，可使用collection将模块映射到模块list中，将菜单列表映射到模块对象的菜单list属性中，这样的作的目的也是方便对查询结果集进行遍历查询。

​         如果使用resultType无法将查询结果映射到list集合中。

 

# 6       延迟加载

## 6.1     什么是延迟加载

 

resultMap可以实现高级映射（使用association、collection实现一对一及一对多映射），**association、collection具备延迟加载功能。**

需求：

如果查询订单并且关联查询用户信息。如果先查询订单信息即可满足要求，当我们需要查询用户信息时再查询用户信息。把对用户信息的按需去查询就是延迟加载。

 

延迟加载：先从单表查询、需要时再从关联表去关联查询，大大提高数据库性能，因为查询单表要比关联查询多张表速度要快。

 

## 6.2      一对一查询延迟加载

### 6.2.1 需求

查询订单信息，关联查询用户信息。

默认只查询订单信息，当需要查询用户信息时再去查询用户信息。

 

### 6.2.2 Sql语句：

 ```sql
SELECT 
  orders.*
FROM
  orders 
 ```



### 6.2.3 定义po类

在Orders类中加入User属性。 

 

### 6.2.4 Mapper.xml

```xml
<select id="findOrdersList" resultMap="userordermap">
   SELECT 
  orders.*
FROM
  orders 
</select>

```



### 6.2.5 定义resultMap

```xml
<!-- 订单信息resultmap -->

<resultMap type="com.zxw.po.Orders" id="userordermap2">
    <id property="id"column="id"/>
    <result property="user_id" column="user_id"/>
    <result property="number"column="number"/>

    <association property="user" javaType="com.zxw.po.User" select="findUserById" column="user_id"/>

</resultMap>

```



association：

select=*"findUserById"*：指定关联查询sql为*findUserById*

column=*"user_id"*：关联查询时将users_id列的值传入*findUserById*

最后将关联查询结果映射至com.zxw.po.User。



### 6.2.6 Mapper接口：

```xml
public List<Orders> findOrdersList() throws Exception;
```



### 6.2.7 测试：

 ```java
Public void testfindOrdersList3()throws Exception{

       //获取session
       SqlSession session = sqlSessionFactory.openSession();
      
       //获限mapper接口实例
       UserMapper userMapper = session.getMapper(UserMapper.class);

       //查询订单信息
       List<Orders> list = userMapper.findOrdersList();
       System.out.println(list);

       //开始加载，通过orders.getUser方法进行加载
	   for(Orders orders:list){
           System.out.println(orders.getUser());
        }

       //关闭session
       session.close();

    }
 ```

 

### 6.2.8 延迟加载的思考

不使用mybatis提供的延迟加载功能是否可以实现延迟加载？

 

实现方法：

针对订单和用户两个表定义两个mapper方法。

1、订单查询mapper方法

2、根据用户id查询用户信息mapper方法

默认使用订单查询mapper方法只查询订单信息。

当需要关联查询用户信息时再调用根据用户id查询用户信息mapper方法查询用户信息。

 

## 6.3 mapper.xml

 

需要定义两个mapper的方法对应的statement。

1、只查询订单信息

SELECT * FROM orders

在查询订单的statement中使用**association去延迟加载（执行）下边的satatement(关联查询用户信息)**

```xml
<select id = "findOrdersUserLazyLoading" resultMap = "OrderUserLazyLodingResultMap">
	SELECT * FROM orders
</select>
```



2、关联查询用户信息

​         通过上边查询到的订单信息中user_id去关联查询用户信息

​         使用UserMapper.xml中的findUserById

```xml
<select id = "findUserById" parameterType="int" resultType = "com.zxw.po.User">
	select * from User where id = #{value}
</select>
```

 

上边先去执行findOrdersUserLazyLoading，当需要去查询用户的时候再去执行findUserById，通过resultMap的定义将延迟加载执行配置起来。

 

## 6.4     延迟加载resultMap

使用association中的select指定延迟加载去执行的statement的id。

 ```xml
<!-- 延迟加载的resultMap -->

   <resultMap type="com.zxw.po.Orders" id="OrdersUserLazyLoadingResultMap>

         <!--对订单信息进行映射配置  -->
         <id column="id"property="id"/>
         <result column="user_id" property="userId"/>
         <result column="number" property="number"/>
         <result column="createtime" property="createtime"/>
         <result column="note"property="note"/>

         <!-- 实现对用户信息进行延迟加载
         select：指定延迟加载需要执行的statement的id（是根据user_id查询用户信息的statement）
         要使用userMapper.xml中findUserById完成根据用户id(user_id)用户信息的查询，如果findUserById不在本mapper中需要前边加namespace
         column：订单信息中关联用户信息查询的列，是user_id
         关联查询的sql理解为：
         SELECT orders.*,
           (SELECT username FROM USER WHERE orders.user_id = user.id)username,
           (SELECT sex FROM USER WHERE orders.user_id = user.id)sex
   		 FROM orders
          -->
         <association property="user"  javaType="com.zxw.po.User"
          select="com.zxw.mapper.UserMapper.findUserById" column="user_id">
         <!-- 实现对用户信息进行延迟加载 -->
     </association>
 </resultMap>

 ```



 测试思路：

1、执行上边mapper方法（findOrdersUserLazyLoading），内部去调用com.zxw.mapper.OrdersMapperCustom中的findOrdersUserLazyLoading只查询orders信息（单表）。

 

2、在程序中去遍历上一步骤查询出的List<Orders>，当我们调用Orders中的getUser方法时，开始进行延迟加载。

 

3、延迟加载，去调用UserMapper.xml中findUserbyId这个方法获取用户信息。



## 6.5    延迟加载配置

mybatis默认没有开启延迟加载，需要在SqlMapConfig.xml中setting配置。

 

在mybatis核心配置文件中配置：

lazyLoadingEnabled、aggressiveLazyLoading

| 设置项                | 描述                                                         | 允许值        | 默认值 |
| --------------------- | ------------------------------------------------------------ | ------------- | ------ |
| lazyLoadingEnabled    | 全局性设置懒加载。如果设为‘false’，则所有相关联的都会被初始化加载。 | true \| false | false  |
| aggressiveLazyLoading | 当设置为‘true’的时候，懒加载的对象可能被任何懒属性全部加载。否则，每个属性都按需加载。 | true \| false | true   |

 

在SqlMapConfig.xml中配置：

```xml
<settings>
		<setting name="lazyLoadingEnabled" value="true"/>
		<setting name="aggressiveLazyLoading" value="false"/>
</settings>
```



## 6.6     延迟加载思考

 

不使用mybatis提供的association及collection中的延迟加载功能，如何实现延迟加载？？

 

实现方法如下：

定义两个mapper方法：

1、查询订单列表

2、根据用户id查询用户信息

实现思路：

先去查询第一个mapper方法，获取订单信息列表

在程序中（service），按需去调用第二个mapper方法去查询用户信息。

 

总之：

使用延迟加载方法，先去查询**简单的sql（最好单表，也可以关联查询），再去按需要加载关联查询的其它信息。**

 

# 7       查询缓存

 

## 7.1     什么是查询缓存

mybatis提供查询缓存，用于减轻数据压力，提高数据库性能。

mybaits提供一级缓存，和二级缓存。



 Mybatis一级缓存的作用域是同一个SqlSession，在同一个sqlSession中两次执行相同的sql语句，第一次执行完毕会将数据库中查询的数据写到缓存（内存），第二次会从缓存中获取数据将不再从数据库查询，从而提高查询效率。当一个sqlSession结束后该sqlSession中的一级缓存也就不存在了。Mybatis默认开启一级缓存。

 

Mybatis二级缓存是多个SqlSession共享的，其作用域是mapper的同一个namespace，不同的sqlSession两次执行相同namespace下的sql语句且向sql中传递参数也相同即最终执行相同的sql语句，第一次执行完毕会将数据库中查询的数据写到缓存（内存），第二次会从缓存中获取数据将不再从数据库查询，从而提高查询效率。Mybatis默认没有开启二级缓存需要在setting全局参数中配置开启二级缓存。

 

一级缓存是SqlSession级别的缓存。在操作数据库时需要构造 sqlSession对象，在对象中有一个数据结构（HashMap）用于存储缓存数据。不同的sqlSession之间的缓存数据区域（HashMap）是互相不影响的。

 

二级缓存是mapper级别的缓存，多个SqlSession去操作同一个Mapper的sql语句，多个SqlSession可以共用二级缓存，二级缓存是跨SqlSession的。

 

为什么要用缓存？

如果缓存中有数据就不用从数据库中获取，大大提高系统性能。

 

 

## 7.2     一级缓存

### 7.2.1     一级缓存工作原理

 

第一次发起查询用户id为1的用户信息，先去找缓存中是否有id为1的用户信息，如果没有，从数据库查询用户信息。得到用户信息，将用户信息存储到一级缓存中。

 

如果sqlSession去执行commit操作（执行插入、更新、删除），清空SqlSession中的一级缓存，这样做的目的为了让缓存中存储的是最新的信息，避免脏读。

 

第二次发起查询用户id为1的用户信息，先去找缓存中是否有id为1的用户信息，缓存中有，直接从缓存中获取用户信息。



 一级缓存区域是根据SqlSession为单位划分的。

每次查询会先从缓存区域找，如果找不到从数据库查询，查询到数据将数据写入缓存。

Mybatis内部存储缓存使用一个HashMap，key为hashCode+sqlId+Sql语句。value为从查询出来映射生成的java对象

sqlSession执行insert、update、delete等操作commit提交后会清空缓存区域。



### 7.2.2     一级缓存测试

mybatis默认支持一级缓存，不需要在配置文件去配置。

 

按照上边一级缓存原理步骤去测试。

 ```java
测试1
//获取session
SqlSession session = sqlSessionFactory.openSession();
//获限mapper接口实例
UserMapper userMapper = session.getMapper(UserMapper.class);
//第一次查询
User user1 = userMapper.findUserById(1);
System.out.println(user1);
//第二次查询，由于是同一个session则不再向数据发出语句直接从缓存取出
User user2 = userMapper.findUserById(1);
System.out.println(user2);
//关闭session
session.close();


测试2

//获取session
SqlSession session = sqlSessionFactory.openSession();
//获限mapper接口实例
UserMapper userMapper = session.getMapper(UserMapper.class);
//第一次查询
User user1 = userMapper.findUserById(1);
System.out.println(user1);
//在同一个session执行更新
User user_update = new User();
user_update.setId(1);
user_update.setUsername("李奎");
userMapper.updateUser(user_update);
session.commit();
//第二次查询，虽然是同一个session但是由于执行了更新操作session的缓存被清空，这里重新发出sql操作
User user2 = userMapper.findUserById(1);
System.out.println(user2);


 ```



 

### 7.2.3     一级缓存应用

 

正式开发，是将mybatis和spring进行整合开发，事务控制在service中。

一个service方法中包括 很多mapper方法调用。

 

service{

​         //开始执行时，开启事务，创建SqlSession对象

​         //第一次调用mapper的方法findUserById(1)

​         //第二次调用mapper的方法findUserById(1)，从一级缓存中取数据

​         //方法结束，sqlSession关闭

}

 

如果是执行两次service调用查询相同 的用户信息，不走一级缓存，因为session方法结束，sqlSession就关闭，一级缓存就清空。

 

## 7.3     二级缓存

### 7.3.1     原理

 

二级缓存区域是根据mapper的namespace划分的，相同namespace的mapper查询数据放在同一个区域，如果使用mapper代理方法每个mapper的namespace都不同，此时可以理解为二级缓存区域是根据mapper划分。

每次查询会先从缓存区域找，如果找不到从数据库查询，查询到数据将数据写入缓存。

Mybatis内部存储缓存使用一个HashMap，key为hashCode+sqlId+Sql语句。value为从查询出来映射生成的java对象

sqlSession执行insert、update、delete等操作commit提交后会清空缓存区域。

 

首先开启mybatis的二级缓存。

sqlSession1去查询用户id为1的用户信息，查询到用户信息会将查询数据存储到二级缓存中。

如果SqlSession3去执行相同 mapper下sql，执行commit提交，清空该 mapper下的二级缓存区域的数据。

sqlSession2去查询用户id为1的用户信息，去缓存中找是否存在数据，如果存在直接从缓存中取出数据。

二级缓存与一级缓存区别，二级缓存的范围更大，多个sqlSession可以共享一个UserMapper的二级缓存区域。

UserMapper有一个二级缓存区域（按namespace分） ，其它mapper也有自己的二级缓存区域（按namespace分）。

每一个namespace的mapper都有一个二缓存区域，两个mapper的namespace如果相同，这两个mapper执行sql查询到数据将存在相同 的二级缓存区域中。

 

 

### 7.3.2     开启二级缓存

 

mybaits的二级缓存是mapper范围级别，除了在SqlMapConfig.xml设置二级缓存的总开关，还要在具体的mapper.xml中开启二级缓存。

 

在核心配置文件SqlMapConfig.xml中加入

```xml
<setting name="cacheEnabled" value="true"/>
```



|              | 描述                                              | 允许值     | 默认值 |
| ------------ | ------------------------------------------------- | ---------- | ------ |
| cacheEnabled | 对在此配置文件下的所有cache 进行全局性开/关设置。 | true false | true   |

在UserMapper.xml中开启二缓存，UserMapper.xml下的sql执行完成会存储到它的缓存区域（HashMap）。

 ```xml
要在你的Mapper映射文件中添加一行：

<cache /> 

表示此mapper开启二级缓存。
 ```



 

### 7.3.3     调用pojo类实现序列化接口

 二级缓存需要查询结果映射的pojo对象实现java.io.Serializable接口实现序列化和反序列化操作，注意如果存在父类、成员pojo都需要实现序列化接口。

```xml
public class Orders implements Serializable

public class User implements Serializable 
```

为了将缓存数据取出执行反序列化操作，因为二级缓存数据存储介质多种多样，不一样在内存。

 

### 7.3.4     测试方法

 ```java
// 二级缓存测试

   @Test
   public void testCache2() throws Exception {

      SqlSession sqlSession1 = sqlSessionFactory.openSession();
      SqlSession sqlSession2 = sqlSessionFactory.openSession();
      SqlSession sqlSession3 = sqlSessionFactory.openSession();

      // 创建代理对象
      UserMapper userMapper1 = sqlSession1.getMapper(UserMapper.class);

      // 第一次发起请求，查询id为1的用户
      User user1 = userMapper1.findUserById(1);
      System.out.println(user1);

      //这里执行关闭操作，将sqlsession中的数据写到二级缓存区域
      sqlSession1.close();
      
      //使用sqlSession3执行commit()操作
      UserMapper userMapper3 = sqlSession3.getMapper(UserMapper.class);
      User user  = userMapper3.findUserById(1);
      user.setUsername("张明明");
      userMapper3.updateUser(user);
      //执行提交，清空UserMapper下边的二级缓存
      sqlSession3.commit();
      sqlSession3.close();
     
       UserMapper userMapper2 = sqlSession2.getMapper(UserMapper.class);
      // 第二次发起请求，查询id为1的用户
      User user2 = userMapper2.findUserById(1);
      System.out.println(user2);
      sqlSession2.close();
   }
 ```





### 7.3.5     useCache配置

 

在statement中设置useCache=false可以禁用当前select语句的二级缓存，即每次查询都会发出sql去查询，默认情况是true，即该sql使用二级缓存。

```xml
<select id="findOrderListResultMap" resultMap="ordersUserMap" useCache="false">
```

总结：针对每次查询都需要最新的数据sql，要设置成useCache=false，禁用二级缓存。

 

### 7.3.6     刷新缓存（就是清空缓存）

在mapper的同一个namespace中，如果有其它insert、update、delete操作数据后需要刷新缓存，如果不执行刷新缓存会出现脏读。

 

 设置statement配置中的flushCache="true" 属性，默认情况下为true即刷新缓存，如果改成false则不会刷新。使用缓存时如果手动修改数据库表中的查询数据会出现脏读。

如下：

```xml
<insert id="insertUser" parameterType="cn.itcast.mybatis.po.User" flushCache="true">
```

总结：一般下执行完commit操作都需要刷新缓存，flushCache=true表示刷新缓存，这样可以避免数据库脏读。

 

### 7.3.7  Mybatis Cache参数

flushInterval（刷新间隔）可以被设置为任意的正整数，而且它们代表一个合理的毫秒形式的时间段。默认情况是不设置，也就是没有刷新间隔，缓存仅仅调用语句时刷新。

size（引用数目）可以被设置为任意正整数，要记住你缓存的对象数目和你运行环境的可用内存资源数目。默认值是1024。

readOnly（只读）属性可以被设置为true或false。只读的缓存会给所有调用者返回缓存对象的相同实例。因此这些对象不能被修改。这提供了很重要的性能优势。可读写的缓存会返回缓存对象的拷贝（通过序列化）。这会慢一些，但是安全，因此默认是false。

 

如下例子：

```xml
<cache  eviction="FIFO"  flushInterval="60000"  size="512"  readOnly="true"/>
```



这个更高级的配置创建了一个 FIFO 缓存,并每隔 60 秒刷新,存数结果对象或列表的 512 个引用,而且返回的对象被认为是只读的,因此在不同线程中的调用者之间修改它们会导致冲突。可用的收回策略有, 默认的是 LRU:

1.       LRU – 最近最少使用的:移除最长时间不被使用的对象。

2.       FIFO – 先进先出:按对象进入缓存的顺序来移除它们。

3.       SOFT – 软引用:移除基于垃圾回收器状态和软引用规则的对象。

4.    WEAK – 弱引用:更积极地移除基于垃圾收集器状态和弱引用规则的对象。



# 8       逆向工程

 

什么是逆向工程



mybaits需要程序员自己编写sql语句，mybatis官方提供逆向工程 可以针对单表自动生成mybatis执行所需要的代码（mapper.java,mapper.xml、po..）

 

企业实际开发中，常用的逆向工程方式：

由于数据库的表生成java代码。



## 8.1     第一步：mapper生成配置文件：

在generatorConfig.xml中配置mapper生成的详细信息，注意改下几点：

 

1、  添加要生成的数据库表

2、  po文件所在包路径

3、  mapper文件所在包路径

 

配置文件如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">

<generatorConfiguration>
    <context id="test" targetRuntime="MyBatis3" defaultModelType="flat">
        <commentGenerator>
            <!-- 是否去除自动生成的注释 true：是 ： false:否 -->
            <property name="suppressAllComments" value="true"/>
            <property name="suppressDate" value="true"/> 
        </commentGenerator>
		
        <!--数据库连接的信息：驱动类、连接地址、用户名、密码 -->
        <jdbcConnection driverClass="com.mysql.jdbc.Driver" connectionURL="jdbc:mysql://localhost/scott" userId="root" password="123456"/>
		<!-- <jdbcConnection driverClass="oracle.jdbc.OracleDriver"
			connectionURL="jdbc:oracle:thin:@127.0.0.1:1521:yycg" 
			userId="yycg"
			password="yycg">
		</jdbcConnection> -->

        <!-- 默认false，把JDBC DECIMAL 和 NUMERIC 类型解析为 Integer，为 true时把JDBC DECIMAL 和 
			NUMERIC 类型解析为java.math.BigDecimal -->
		<javaTypeResolver>
			<property name="forceBigDecimals" value="false" />
		</javaTypeResolver>
        
		<!-- targetProject:生成PO类的位置 -->
		<javaModelGenerator targetPackage="cn.itcast.ssm.po"
			targetProject=".\src">
			<!-- enableSubPackages:是否让schema作为包的后缀 -->
			<property name="enableSubPackages" value="false" />
			<!-- 从数据库返回的值被清理前后的空格 -->
			<property name="trimStrings" value="true" />
		</javaModelGenerator>
     
        <!-- targetProject:mapper映射文件生成的位置 -->
		<sqlMapGenerator targetPackage="cn.itcast.ssm.mapper" 
			targetProject=".\src">
			<!-- enableSubPackages:是否让schema作为包的后缀 -->
			<property name="enableSubPackages" value="false" />
		</sqlMapGenerator>
	
        <!-- targetPackage：mapper接口生成的位置 -->
		<javaClientGenerator type="XMLMAPPER"
			targetPackage="cn.itcast.ssm.mapper" 
			targetProject=".\src">
			<!-- enableSubPackages:是否让schema作为包的后缀 -->
			<property name="enableSubPackages" value="false" />
		</javaClientGenerator>

        <!-- 要生成哪些表 -->     
        <table tableName="tb_provinces" domainObjectName="Province"
               enableCountByExample="true" enableUpdateByExample="true"
               enableDeleteByExample="true" enableSelectByExample="true"
               selectByExampleQueryId="true"
        />
    </context>
</generatorConfiguration>
```



 

## 8.2     第二步：使用java类生成mapper文件：

 ```java
Public void generator() throws Exception{

      List<String> warnings = new ArrayList<String>();

      boolean overwrite = true;

      File configFile = new File("generatorConfig.xml"); 

      ConfigurationParser cp = new ConfigurationParser(warnings);

      Configuration config = cp.parseConfiguration(configFile);

      DefaultShellCallback callback = new DefaultShellCallback(overwrite);

      MyBatisGenerator myBatisGenerator = new MyBatisGenerator(config,

            callback, warnings);

      myBatisGenerator.generate(null);

   }

   Public static void main(String[] args) throws Exception {

      try {

         GeneratorSqlmap generatorSqlmap = new GeneratorSqlmap();

         generatorSqlmap.generator();

      } catch (Exception e) {

         e.printStackTrace();

      }

}
 ```





 

 

 

## 8.3     第三步：拷贝生成的mapper文件到工程中指定的目录中

**Mapper.xml**

Mapper.xml的文件拷贝至mapper目录内



**Mapper.java**

Mapper.java的文件拷贝至mapper 目录内

注意：mapper xml文件和mapper.java文件在一个目录内且文件名相同。

 

 

## 8.4  第四步Mapper接口测试

 

学会使用mapper自动生成的增、删、改、查方法。



//删除符合条件的记录

**int** deleteByExample(UserExample example);

//根据主键删除

**int** deleteByPrimaryKey(String id);



//插入对象所有字段

**int** insert(User record);

//插入对象不为空的字段

**int** insertSelective(User record);



//自定义查询条件查询结果集

List<User> selectByExample(UserExample example);

//根据主键查询

UserselectByPrimaryKey(String id);



//根据主键将对象中不为空的值更新至数据库

**int** updateByPrimaryKeySelective(User record);

//根据主键将对象中所有字段的值更新至数据库

**int** updateByPrimaryKey(User record);

 

 

## 8.5     逆向工程注意事项

### 8.5.1  Mapper文件内容不覆盖而是追加

XXXMapper.xml文件已经存在时，如果进行重新生成则mapper.xml文件内容不被覆盖而是进行内容追加，结果导致mybatis解析失败。

解决方法：删除原来已经生成的mapper xml文件再进行生成。

Mybatis自动生成的po及mapper.java文件不是内容而是直接覆盖没有此问题。

 

### 8.5.2  Table schema问题

下边是关于针对oracle数据库表生成代码的schema问题：

 

Schma即数据库模式，oracle中一个用户对应一个schema，可以理解为用户就是schema。

当Oralce数据库存在多个schema可以访问相同的表名时，使用mybatis生成该表的mapper.xml将会出现mapper.xml内容重复的问题，结果导致mybatis解析错误。

解决方法：在table中填写schema，如下：

<table schema="XXXX" tableName=" " >

XXXX即为一个schema的名称，生成后将mapper.xml的schema前缀批量去掉，如果不去掉当oracle用户变更了sql语句将查询失败。

快捷操作方式：mapper.xml文件中批量替换：“from XXXX.”为空

 

Oracle查询对象的schema可从dba_objects中查询，如下：

select * from dba_objects