1       SqlMapConfig.xml

mybatis的全局配置文件SqlMapConfig.xml，配置内容如下：

 

properties（属性）

settings（全局配置参数）

typeAliases（类型别名）

typeHandlers（类型处理器）

objectFactory（对象工厂）

plugins（插件）

environments（环境集合属性对象）

​		environment（环境子属性对象）

​			transactionManager（事务管理）

​			dataSource（数据源）

mappers（映射器）

 

## 1.1     properties属性

 

需求：

将数据库连接参数单独配置在db.properties中，只需要在SqlMapConfig.xml中加载db.properties的属性值。

在SqlMapConfig.xml中就不需要对数据库连接参数硬编码。

 

将数据库连接参数只配置在db.properties中，原因：方便对参数进行统一管理，其它xml可以引用该db.properties。

 ```properties
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/mybatis
jdbc.username=root
jdbc.password=mysql
 ```

在sqlMapConfig.xml加载属性文件：

```xml
<!-- 加载属性文件 -->
<properties resource="db.properties">
    <!--properties中还可以配置一些属性名和属性值  -->
    <!-- <property name="jdbc.driver" value=""/> -->
</properties>
<!-- 和spring整合后 environments配置将废除-->
<environments default="development">
    <environment id="development">
        <!-- 使用jdbc事务管理，事务控制由mybatis-->
        <transactionManager type="JDBC" />
        <!-- 数据库连接池，由mybatis管理-->
        <dataSource type="POOLED">
            <property name="driver" value="${jdbc.driver}" />
            <property name="url" value="${jdbc.url}" />
            <property name="username" value="${jdbc.username}" />
            <property name="password" value="${jdbc.password}" />
        </dataSource>
    </environment>
</environments>
```



properties特性：

注意： MyBatis 将按照下面的顺序来加载属性：

* 在 properties 元素体内定义的属性首先被读取。 
* 然后会读取properties 元素中resource或 url 加载的属性，它会覆盖已读取的同名属性。 
* 最后读取parameterType传递的属性，它会覆盖已读取的同名属性。

建议：

* 不要在properties元素体内添加任何属性值，只将属性值定义在properties文件中。
* 在properties文件中定义属性名要有一定的特殊性，如：XXXXX.XXXXX.XXXX

 

## 1.2     settings全局参数配置

 

mybatis框架在运行时可以调整一些运行参数。

比如：开启二级缓存、开启延迟加载。

全局参数将会影响mybatis的运行行为。 

详细

|       Setting(设置)       | Description（描述）                                          | Valid Values(验证值组)                                       | Default(默认值)                                              |
| :-----------------------: | ------------------------------------------------------------ | ------------------------------------------------------------ | :----------------------------------------------------------- |
|       cacheEnabled        | 在全局范围内启用或禁用缓存配置任何映射器在此配置下。         | true \| false                                                | TRUE                                                         |
|    lazyLoadingEnabled     | 在全局范围内启用或禁用延迟加载。禁用时，所有协会将热加载。   | true \| false                                                | TRUE                                                         |
|   aggressiveLazyLoading   | 启用时，有延迟加载属性的对象将被完全加载后调用懒惰的任何属性。否则，每一个属性是按需加载。 | true \| false                                                | TRUE                                                         |
| multipleResultSetsEnabled | 允许或不允许从一个单独的语句（需要兼容的驱动程序）要返回多个结果集。 | true \| false                                                | TRUE                                                         |
|      useColumnLabel       | 使用列标签，而不是列名。在这方面，不同的驱动有不同的行为。参考驱动文档或测试两种方法来决定你的驱动程序的行为如何。 | true \| false                                                | TRUE                                                         |
|     useGeneratedKeys      | 允许JDBC支持生成的密钥。兼容的驱动程序是必需的。此设置强制生成的键被使用，如果设置为true，一些驱动会不兼容性，但仍然可以工作。 | true \| false                                                | FALSE                                                        |
|    autoMappingBehavior    | 指定MyBatis的应如何自动映射列到字段/属性。NONE自动映射。 PARTIAL只会自动映射结果没有嵌套结果映射定义里面。 FULL会自动映射的结果映射任何复杂的（包含嵌套或其他）。 | NONE, PARTIAL, FULL                                          | PARTIAL                                                      |
|    defaultExecutorType    | 配置默认执行人。SIMPLE执行人确实没有什么特别的。 REUSE执行器重用准备好的语句。 BATCH执行器重用语句和批处理更新。 | SIMPLE REUSE BATCH                                           | SIMPLE                                                       |
|  defaultStatementTimeout  | 设置驱动程序等待一个数据库响应的秒数。                       | Any positive integer                                         | Not Set (null)                                               |
|   safeRowBoundsEnabled    | 允许使用嵌套的语句RowBounds。                                | true \| false                                                | FALSE                                                        |
| mapUnderscoreToCamelCase  | 从经典的数据库列名A_COLUMN启用自动映射到骆驼标识的经典的Java属性名aColumn。 | true \| false                                                | FALSE                                                        |
|      localCacheScope      | MyBatis的使用本地缓存，以防止循环引用，并加快反复嵌套查询。默认情况下（SESSION）会话期间执行的所有查询缓存。如果localCacheScope=STATMENT本地会话将被用于语句的执行，只是没有将数据共享之间的两个不同的调用相同的SqlSession。 | SESSION \| STATEMENT                                         | SESSION                                                      |
|      dbcTypeForNull       | 指定为空值时，没有特定的JDBC类型的参数的JDBC类型。有些驱动需要指定列的JDBC类型，但其他像NULL，VARCHAR或OTHER的工作与通用值。 | JdbcType enumeration. Most common are: NULL, VARCHAR and OTHER | OTHER                                                        |
|  lazyLoadTriggerMethods   | 指定触发延迟加载的对象的方法。                               | A method name list separated by commas                       | equals,clone,hashCode,toString                               |
| defaultScriptingLanguage  | 指定所使用的语言默认为动态SQL生成。                          | A type alias or fully qualified class name.                  | org.apache.ibatis.scripting.xmltags.XMLDynamicLanguageDriver |
|    callSettersOnNulls     | 指定如果setter方法或地图的put方法时，将调用检索到的值是null。它是有用的，当你依靠Map.keySet（）或null初始化。注意原语（如整型，布尔等）不会被设置为null。 | true \| false                                                | FALSE                                                        |
|         logPrefix         | 指定的前缀字串，MyBatis将会增加记录器的名称。                | Any String                                                   | Not set                                                      |
|          logImpl          | 指定MyBatis的日志实现使用。如果此设置是不存在的记录的实施将自动查找。 | SLF4J \| LOG4J \| LOG4J2 \| JDK_LOGGING \| COMMONS_LOGGING \| STDOUT_LOGGING \| NO_LOGGING | Not set                                                      |
|       proxyFactory        | 指定代理工具，MyBatis将会使用创建懒加载能力的对象。          | CGLIB \| JAVASSIST                                           |                                                              |



## 1.3     typeAliases（别名）重点

 

### 1.3.1     需求

在mapper.xml中，定义很多的statement，statement需要parameterType指定输入参数的类型、需要resultType指定输出结果的映射类型。

 

如果在指定类型时输入类型全路径，不方便进行开发，可以针对parameterType或resultType指定的类型定义一些别名，在mapper.xml中通过别名定义，方便开发。

 

### 1.3.2     mybatis默认支持别名

 

| 别名       | 映射的类型 |
| ---------- | ---------- |
| _byte      | byte       |
| _long      | long       |
| _short     | short      |
| _int       | int        |
| _integer   | int        |
| _double    | double     |
| _float     | float      |
| _boolean   | boolean    |
| string     | String     |
| byte       | Byte       |
| long       | Long       |
| short      | Short      |
| int        | Integer    |
| integer    | Integer    |
| double     | Double     |
| float      | Float      |
| boolean    | Boolean    |
| date       | Date       |
| decimal    | BigDecimal |
| bigdecimal | BigDecimal |



### 1.3.3     自定义别名

 

#### 1.3.3.1              单个别名定义

 ```xml
<!-- 别名定义 -->
<typeAliases>

    <!-- 针对单个别名定义
          type：类型的路径
          alias：别名
   -->
    <typeAlias type="com.zxw.po.User" alias="user"/>

</typeAliases>
 ```



#### 1.3.3.2              批量定义别名（常用）

 

```xml
<!-- 别名定义 -->
<typeAliases>

    <package name="com.zxw.po"/>

</typeAliases>
```



## 1.4     typeHandlers（类型处理器）

 

mybatis中通过typeHandlers完成jdbc类型和java类型的转换。

通常情况下，mybatis提供的类型处理器满足日常需要，不需要自定义.

mybatis支持类型处理器：

 

| 类型处理器              | **Java**类型          | **JDBC**类型                                               |
| ----------------------- | --------------------- | ---------------------------------------------------------- |
| BooleanTypeHandler      | Boolean，boolean      | 任何兼容的布尔值                                           |
| ByteTypeHandler         | Byte，byte            | 任何兼容的数字或字节类型                                   |
| ShortTypeHandler        | Short，short          | 任何兼容的数字或短整型                                     |
| IntegerTypeHandler      | Integer，int          | 任何兼容的数字和整型                                       |
| LongTypeHandler         | Long，long            | 任何兼容的数字或长整型                                     |
| FloatTypeHandler        | Float，float          | 任何兼容的数字或单精度浮点型                               |
| DoubleTypeHandler       | Double，double        | 任何兼容的数字或双精度浮点型                               |
| BigDecimalTypeHandler   | BigDecimal            | 任何兼容的数字或十进制小数类型                             |
| StringTypeHandler       | String                | CHAR和VARCHAR类型                                          |
| ClobTypeHandler         | String                | CLOB和LONGVARCHAR类型                                      |
| NStringTypeHandler      | String                | NVARCHAR和NCHAR类型                                        |
| NClobTypeHandler        | String                | NCLOB类型                                                  |
| ByteArrayTypeHandler    | byte[]                | 任何兼容的字节流类型                                       |
| BlobTypeHandler         | byte[]                | BLOB和LONGVARBINARY类型                                    |
| DateTypeHandler         | Date（java.util）     | TIMESTAMP类型                                              |
| DateOnlyTypeHandler     | Date（java.util）     | DATE类型                                                   |
| TimeOnlyTypeHandler     | Date（java.util）     | TIME类型                                                   |
| SqlTimestampTypeHandler | Timestamp（java.sql） | TIMESTAMP类型                                              |
| SqlDateTypeHandler      | Date（java.sql）      | DATE类型                                                   |
| SqlTimeTypeHandler      | Time（java.sql）      | TIME类型                                                   |
| ObjectTypeHandler       | 任意                  | 其他或未指定类型                                           |
| EnumTypeHandler         | Enumeration类型       | VARCHAR-任何兼容的字符串类型，作为代码存储（而不是索引）。 |

 

## 1.5     mappers（映射配置）

 

### 1.5.1     通过resource加载单个映射文件

 

```xml
<!--通过resource方法一次加载一个映射文件 -->
<mapper resource="mapper/UserMapper.xml"/>
```



 

### 1.5.2     通过mapper接口加载单个mapper

 

```xml
<!-- 通过mapper接口加载单个 映射文件
  遵循一些规范：需要将mapper接口类名和mapper.xml映射文件名称保持一致，且在一个目录 中
  上边规范的前提是：使用的是mapper代理方法
 -->
<mapper class="com.zxw.mapper.UserMapper"/>
```



按照上边的规范，将mapper.java和mapper.xml放在一个目录 ，且同名。

 

### 1.5.3     批量加载mapper(推荐使用)

 

```xml
<!-- 批量加载mapper
      指定mapper接口的包名，mybatis自动扫描包下边所有mapper接口进行加载
      遵循一些规范：需要将mapper接口类名和mapper.xml映射文件名称保持一致，且在一个目录 中
      上边规范的前提是：使用的是mapper代理方法
   -->
<package name="com.zxw.mapper"/>
```



# 2       输入映射

通过parameterType指定输入参数的类型，类型可以是简单类型、hashmap、pojo的包装类型。

 

## 2.1     传递pojo的包装对象

### 2.1.1     需求

完成用户信息的综合查询，需要传入查询条件很复杂（可能包括用户信息、其它信息，比如商品、订单的）

### 2.1.2     定义包装类型pojo

针对上边需求，建议使用自定义的包装类型的pojo。

在包装类型的pojo中将复杂的查询条件包装进去。

 ```java
public class UserCustom extends User{
	
	//可以扩展用户的信息
    
}

public class UserQueryVo {
	
	//传入多个id
	private List<Integer> ids;
	
	//在这里包装所需要的查询条件
	
	//用户查询条件
	private UserCustom userCustom;

	//setget方法
	
	//可以包装其它的查询条件，订单、商品
	//....
	

}
 ```



### 2.1.3     mapper.xml

在UserMapper.xml中定义用户信息综合查询（查询条件复杂，通过高级查询进行复杂关联查询）。

```xml
 <!-- 用户信息综合查询

      #(userCustom.sex) : 取出pojo包装对象中的性别

      ${userCustom. username): 取出pojo包装对象中的用户名称

      <select id="findUserList " parameterType= "com.zxw.po.UserQueryVo"

      lesultType= "com.zxw.po.UserCustom">

      SELECT * FROM USER WHERE user.sex = #(userCustom.sex] AND user. username LIKE ' %${userCustom.username}%'</select>

```



### 2.1.4     mapper.java



```jav
//用户信息综合查询  .
public List<UserCustom> findUserlist (UserQueryVo userQueryVo) throws Exception;
```



### 2.1.5     测试代码



```java
@Test
public void testFindUserList() throws Exception {
		
		SqlSession sqlSession = sqlSessionFactory.openSession();
		
		//创建UserMapper对象，mybatis自动生成mapper代理对象
		UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
		
		//创建包装对象，设置查询条件
		UserQueryVo userQueryVo = new UserQueryVo();
		UserCustom userCustom = new UserCustom
		userCustom.setSex("1");
		userCustom.setUsername("小明");
		userQueryVo.setUserCustom(userCustom);
		//调用userMapper的方法

		List<UserCustom> list = userMapper.findUserList(userQueryVo);
		
		System.out.println(list);
			
	}
```



# 3       输出映射

 

## 3.1     resultType

**使用resultType进行输出映射，只有查询出来的列名和pojo中的属性名一致，该列才可以映射成功。**

如果查询出来的列名和pojo中的属性名全部不一致，没有创建pojo对象。

只要查询出来的列名和pojo中的属性有一个一致，就会创建pojo对象。

 

### 3.1.1     输出简单类型

#### 3.1.1.1              需求

用户信息的综合查询列表总数，通过查询总数和上边用户综合查询列表才可以实现分页。

 

#### 3.1.1.2              mapper.xml



```xml
<!-- 
    查询用户总页数
    parameterType; 指定输入参数的类型
    resultType:输出参数类型
-->

<select id= "findUserCount" parameterType= "cn.zxw.po.UserQueryVo" resultType= "int">
SELECT count(*) FROM USER WHERE user.sex = #(userCustom.sex] AND user . username LIKE '%${userCustom. user}%'
</select>
```



#### 3.1.1.3              mapper.java



```java
public int findUserCount(UserQueryVo userQueryVo) thorws Exception；
```



 

#### 3.1.1.4              测试代码



```java
@Test
public void testFindUserCount() throws Exception {

    SqlSession sqlSession = sqlSessionFactory.openSession();

    //创建UserMapper对象，mybatis自动生成mapper代理对象
    UserMapper userMapper = sqlSession.getMapper(UserMapper.class);

    //创建包装对象，设置查询条件
    UserQueryVo userQueryVo = new UserQueryVo();
    UserCustom userCustom = new UserCustom();
    userCustom.setSex("1");
    userCustom.setUsername("张三丰");
    userQueryVo.setUserCustom(userCustom);
    //调用userMapper的方法

    int count = userMapper.findUserCount(userQueryVo);

    System.out.println(count);

}
```



#### 3.1.1.5              小结

 

查询出来的结果集只有一行且一列，可以使用简单类型进行输出映射。

 

### 3.1.2     输出pojo对象和pojo列表

 

不管是输出的pojo单个对象还是一个列表（list中包括pojo），在mapper.xml中resultType指定的类型是一样的。

在mapper.java指定的方法返回值类型不一样：

​   1、输出单个pojo对象，方法返回值是单个对象类型

```java
public User findUserById(int id) throws Exception;
```



​    2、输出pojo对象list，方法返回值是List<Pojo>

 ```java
public List<User> findUserByName(String name) throws Exception;
 ```



生成的动态代理对象中是根据mapper方法的返回值类型确定是调用selectOne(返回单个对象调用)还是selectList （返回集合对象调用 ）.

 

## 3.2     resultMap

 

mybatis中使用resultMap完成高级输出结果映射。

 

### 3.2.1     resultMap使用方法

如果查询出来的列名和pojo的属性名不一致，通过定义一个resultMap对列名和pojo属性名之间作一个映射关系.

1、定义resultMap

2、使用resultMap作为statement的输出映射类型

 

### 3.2.2     将下边的sql使用User完成映射

SELECT id id_,username username_ FROM USER WHERE id=#{value}

User类中属性名和上边查询列名不一致。

 

#### 3.2.2.1              定义reusltMap

```xml
<!-- 定义resultMap
   type:映射成的pojo类型，可以是别名
   id：resultMap唯一标识
-->
	<resultMap type="order" id="orderMap">
		<!-- id标签用于绑定主键 -->
		<!-- <id property="id" column="id"/> -->
		
		<!-- 使用result绑定普通字段
 			result：映射定义
			column：查询出来的列名
			property：指定pojo中的属性名
		-->
		<result property="userId" column="user_id"/>
		<result property="number" column="number"/>
		<result property="createtime" column="createtime"/>
		<result property="note" column="note"/>
	</resultMap>
```



#### 3.2.2.2              使用resultMap作为statement的输出映射类型

```xml
<!-- 使用resultMap 
	resultMap：指的是定义的resultMap的id，如果这个resultMap在其他的mapper文件，前面需要加namespace
-->
<select id="findUserByIdResultMap" resultMap="userResultMap">
    SELECT id id_,username username_ FROM USER WHERE id=#{value}
</select>
```



 

 

#### 3.2.2.3              mapper.java

```java
//根据id查询用户信息，使用resultMap输出
public User findUserByIdResultMap(int id) throws Exception;
```



#### 3.2.2.4              测试

```java
@Test
public void testFindUserByIdResultMap() throws Exception {

    SqlSession sqlSession = sqlSessionFactory.openSession();

    //创建UserMapper对象，mybatis自动生成mapper代理对象
    UserMapper userMapper = sqlSession.getMapper(UserMapper.class);

    //调用userMapper的方法
    User user = userMapper.findUserByIdResultMap(1);

    System.out.println(user);
}
```



## 3.3     小结

**使用resultType进行输出映射，只有查询出来的列名和pojo中的属性名一致，该列才可以映射成功。**

**如果查询出来的列名和pojo的属性名不一致，通过定义一个resultMap对列名和pojo属性名之间作一个映射关系。**

 

# 4       动态sql

 

## 

mybatis核心对sql语句进行灵活操作，通过表达式进行判断，对sql进行灵活拼接、组装。

 

## 4.1     If

基于完成用户列表查询功能，由多查询条件拼装引出if标签。

 ```xml
<!-- 演示动态sql-if标签的使用情景 -->

    <select id="getUserByWhere" parameterType="user"resultType="com.zxw.pojo.User">

       <!-- SELECT * FROM USER WHERE username LIKE '%${username}%' and id = #{id} -->

       SELECT * FROM USER where 1 = 1

       <!-- if标签的使用 -->
       <if test="id != null">
           and id = #{id}
       </if>

       <if test="username != null and username != ''">
           and username LIKE '%${username}%'
       </if>

  </select>
 ```

 

## 4.2     Where

```xml
<!-- 演示动态sql-where标签的使用情景 -->

    <select id="getUserByWhere2" parameterType="user"

       resultType="com.zxw.pojo.User">

       <!-- include:引入sql片段,refid引入片段id -->

       SELECT  *   FROM USER

       <!-- where会自动加上where同处理多余的and -->
       <where>
           <!-- if标签的使用 -->
           <if test=*"id != null"*>
              and id = #{id}
           </if>

           <if test=*"username != null and username != ''"*>
              and username LIKE '%${username}%'
           </if>
       </where>
	</select>
```



## 4.3       Foreach

```xml
<!-- 演示动态sql-foreach标签的使用情景 -->

    <select id="getUserByIds" parameterType="queryvo" resultType="com.zxw.pojo.User">

       SELECT  *  FROM USER

       <!-- where会自动加上where同处理多余的and -->
       <where>
           <!-- id IN(1,10,25,30,34) -->
           <!-- foreach循环标签 
               collection:要遍历的集合，来源入参 
			  item:每个遍历生成对象中
               open:循环开始前的sql 
               separator:分隔符 
               close:循环结束拼接的sql
           -->
           <foreach item="uid" collection="ids" open="id IN(" separator=","close=")">
              #{uid}
           </foreach>  
        </where>
  </select>
```



## 4.4     Sql片段

```xml
通过select * 不好引出查询字段名，抽取共用sql片段。

1. 	定义
<!-- sql片段 定义，id:片段唯一标识 -->
	<sql id="user_column">
		`id`,
		`username`,
		`birthday`,
		`sex`,
		`address`,
		`uuid2`
	</sql>

2. 	使用
<!-- sql片段的使用：include:引入sql片段,refid引入片段id -->
SELECT  <include refid="user_column" />  FROM USER

```



