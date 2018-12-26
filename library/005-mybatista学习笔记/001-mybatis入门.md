# 1    jdbc回顾



## 1.1. jdbc编程步骤：

1、	加载数据库驱动

2、	创建并获取数据库链接

3、	设置sql语句 

4、 创建jdbc statement对象或者preparedStatement

5、	设置sql语句中的参数(使用preparedStatement)

6、	通过statement执行sql并获取结果

7、	对sql执行结果进行解析处理

8、	释放资源(resultSet、preparedstatement、connection)



## 1.2. JDBC代码实现

```java
public class JdbcReview {

	public static void main(String[] args) {
		Connection connection = null;
		PreparedStatement preparedStatement = null;
		ResultSet resultSet = null;

		try {
			// 加载数据库驱动
			Class.forName("com.mysql.jdbc.Driver");

			// 通过驱动管理类获取数据库链接
			connection =DriverManager.getConnection("jdbc:mysql://localhost:3306/sc?characterEncoding=utf-8", "root", "root");
			// 定义sql语句 ?表示占位符
			String sql = "select * from user where username = ?";
			// 获取预处理statementh
			preparedStatement = connection.prepareStatement(sql);
			// 设置参数，第一个参数为sql语句中参数的序号（从1开始），第二个参数为设置的参数值
			preparedStatement.setString(1, "王五");
			// 向数据库发出sql执行查询，查询出结果集
			resultSet = preparedStatement.executeQuery();
			// 遍历查询结果集
			while (resultSet.next()) {
				System.out.println(resultSet.getString("id") + "  " + resultSet.getString("username"));
			}
		} catch (Exception e) {
			e.printStackTrace();
		} finally {
			// 释放资源
			if (resultSet != null) {
				try {
					resultSet.close();
				} catch (SQLException e) {
					// TODO Auto-generated catch block
					e.printStackTrace();
				}
			}
			if (preparedStatement != null) {
				try {
					preparedStatement.close();
				} catch (SQLException e) {
					// TODO Auto-generated catch block
					e.printStackTrace();
				}
			}
			if (connection != null) {
				try {
					connection.close();
				} catch (SQLException e) {
					// TODO Auto-generated catch block
					e.printStackTrace();
				}
			}
		}
	}
}

```



## 1.3  主要对象分析

Class.forName ------>加载数据库的驱动
	
DriverManager.getConnection（） ----->得到Connection对象，并加载数据库连接
	
connection.prepareStatement（）------>得到PreparedStatement对象并传入SQL。

resultSet = preparedStatement.executeQuery() ----->获取到查询的结果集，并存入ResultSet中
	

## 1.4 jdbc存在问题及改进

1、数据库连接，使用时就创建，不使用立即释放，对数据库进行频繁连接开启和关闭，造成数据库资源浪费，影响 数据库性能。

设想：使用数据库连接池管理数据库连接。

 

2、将sql语句硬编码到java代码中，如果sql 语句修改，需要重新编译java代码，不利于系统维护。

设想：将sql语句配置在xml配置文件中，即使sql变化，不需要对java代码进行重新编译。

 

3、向preparedStatement中设置参数，对占位符号位置和设置参数值，硬编码在java代码中，不利于系统维护。

设想：将sql语句及占位符号和参数全部配置在xml中。

 

4、从resutSet中遍历结果集数据时，存在硬编码，将获取表的字段进行硬编码，不利于系统维护。

设想：将查询的结果集，自动映射成java对象。



# 2  mybatis概述

 

## 2.1   mybatis是什么？

 

mybatis是一个持久层的框架，是apache下的顶级项目。

mybatis托管到goolecode下，再后来托管到github下(https://github.com/mybatis/mybatis-3/releases)。

 

Mybatis是面向sql的持久层框架，他封装了jdbc访问数据库的过程，让程序将主要精力放在sql上，通过mybatis提供的映射方式，自由灵活生成（半自动化，大部分需要程序员编写sql）满足需要sql语句。

 

mybatis可以将向 preparedStatement中的输入参数自动进行输入映射，将查询结果集灵活映射成java对象。（输出映射）

 

## 2.2  mybatis和hibernate比较

 

hibernate：是一个标准ORM框架（对象关系映射）。入门门槛较高的，不需要程序写sql，sql语句自动生成了。

对sql语句进行优化、修改比较困难的。

应用场景：

​         适用与需求变化不多的中小型项目，比如：后台管理系统，erp、orm、oa。。

 

mybatis：专注是sql本身，需要程序员自己编写sql语句，sql修改、优化比较方便。mybatis是一个不完全 的ORM框架，虽然程序员自己写sql，mybatis 也可以实现映射（输入映射、输出映射）。

应用场景：

​         适用与需求变化较多的项目，比如：互联网项目。

 

企业进行技术选型，以低成本 高回报作为技术选型的原则，根据项目组的技术力量进行选择。



## 2.3  mybatis原理

> SqlMapConfig.xml（是mybatis的全局配置文件，名称不固定的）配置了数据源、事务等mybatis运行环境配置映射文件（配置sql语句）mapper.xml（映射文件）、mapper.xml、mapper.xml.....

 

> SqlSessionFactory（会话工厂），根据配置文件创建工厂作用：创建SqlSession



> SqlSession（会话）是一个接口，面向程序员的接口, 作用：操作数据库（发出sql增、删、改、查）

 

> Executor（执行器）是一个接口（基本执行器、缓存执行器）作用：SqlSession内部通过执行器操作数据库



> mapped statement（底层封装对象）作用：对操作数据库存储封装，包括 sql语句，输入参数、输出结果类型(输入输出参数类型 : java简单类型， hashmap， pojo自定义)



# 3   入门程序

## 3.1     需求

根据用户id（主键）查询用户信息

根据用户名称模糊查询用户信息

添加用户

删除 用户

更新用户

 

## 3.2     环境

mybatis运行环境（jar包）：

从https://github.com/mybatis下载，



lib下：依赖包

mybatis-3.2.7.jar：核心 包

加入mysql的驱动包

 

## 3.3   log4j.properties

```properties
# Global logging configuration
# 开始DEBUG，生产info
log4j.rootLogger=DEBUG, stdout
# Console output...
log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern=%5p [%t] - %m%n

```



 

## 3.4     SqlMapConfig.xml

配置mybatis的运行环境，数据源、事务等。

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
	
    <!-- 加载属性文件 -->
	<properties resource="db.properties">
		<!--properties中还可以配置一些属性名和属性值  -->
		<!-- <property name="jdbc.driver" value=""/> -->
	</properties>
    
	<!-- 和spring整合后 environments配置将废除 -->
	<environments default="development">
		<environment id="development">
			<!-- 使用jdbc事务管理，事务控制由mybatis -->
			<transactionManager type="JDBC" />
			<!-- 数据库连接池，由mybatis管理 -->
			<dataSource type="POOLED">
				<property name="driver" value="${jdbc.driver}" />
				<property name="url" value="${jdbc.url}" />
				<property name="username" value="${jdbc.username}" />
				<property name="password" value="${jdbc.password}" />
			</dataSource>
		</environment>
	</environments>

</configuration>
```



 

## 3.5     根据id（主键）查询用户

 

### 3.5.1     创建po类

```java
public class User {
		//属性名和数据库表的字段对应
		private int id;
		private String username;// 用户姓名
		private String sex;// 性别
		private Date birthday;// 生日
		private String address;// 地址
```



### 3.5.2     映射文件

映射文件命名：

User.xml（原始ibatis命名），mapper代理开发映射文件名称叫XXXMapper.xml，比如：UserMapper.xml、ItemsMapper.xml。

在映射文件中配置sql语句。

 ```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<!-- namespace命名空间，作用就是对sql进行分类化管理，理解sql隔离 
注意：使用mapper代理方法开发，namespace有特殊重要的作用
-->
<mapper namespace="test">

	<!-- 在 映射文件中配置很多sql语句 -->
	<!-- 需求：通过id查询用户表的记录 -->
	<!-- 通过 select执行数据库查询
	id：标识 映射文件中的 sql
	将sql语句封装到mappedStatement对象中，所以将id称为statement的id
	parameterType：指定输入 参数的类型，这里指定int型 
	#{}表示一个占位符号
	#{id}：其中的id表示接收输入 的参数，参数名称就是id，如果输入 参数是简单类型，#{}中的参数名可以任意，可以value或其它名称
	
	resultType：指定sql输出结果 的所映射的java对象类型，select指定resultType表示将单条记录映射成的java对象。
	 -->
	<select id="findUserById" parameterType="int" resultType="cn.zxw.po.User">
		SELECT * FROM USER WHERE id=#{value}
	</select>	
</mapper>



 ```



 

### 3.5.3     在SqlMapConfig.xml加载映射文件

在sqlMapConfig.xml中加载User.xml:

```xml
<mappers>
    <mapper resource = "SQlmap/userMapping.xml"/>
</mappers>
```



### 3.5.4     程序编写

 ```java
// 根据id查询用户信息，得到一条记录结果
	@Test
	public void findUserByIdTest() throws IOException {

		// mybatis配置文件
		String resource = "SqlMapConfig.xml";
		// 得到配置文件流
		InputStream inputStream = Resources.getResourceAsStream(resource);

		// 创建会话工厂，传入mybatis的配置文件信息
		SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder()
				.build(inputStream);

		// 通过工厂得到SqlSession
		SqlSession sqlSession = sqlSessionFactory.openSession();

		// 通过SqlSession操作数据库
		// 第一个参数：映射文件中statement的id，等于=namespace+"."+statement的id
		// 第二个参数：指定和映射文件中所匹配的parameterType类型的参数
		// sqlSession.selectOne结果 是与映射文件中所匹配的resultType类型的对象
		// selectOne查询出一条记录
		User user = sqlSession.selectOne("test.findUserById", 1);

		System.out.println(user);

		// 释放资源
		sqlSession.close();

	}
 ```



 

## 3.6     根据名称模糊查询用户

 

### 3.6.1     映射文件

 

使用User.xml，添加根据用户名称模糊查询用户信息的sql语句。

 ```xml
<!-- 根据用户名称模糊查询用户信息，可能返回多条
	resultType：指定就是单条记录所映射的java对象 类型
	${}:表示拼接sql串，将接收到参数的内容不加任何修饰拼接在sql中。
	使用${}拼接sql，引起 sql注入
	${value}：接收输入 参数的内容，如果传入类型是简单类型，${}中只能使用value
-->
<select id="findUserByName" parameterType="java.lang.String"       resultType="cn.zxw.po.User">
	SELECT * FROM USER WHERE username LIKE '%${value}%'
</select>
 ```



### 3.6.2     程序代码

```java
	// 根据用户名称模糊查询用户列表
	@Test
	public void findUserByNameTest() throws IOException {
		// mybatis配置文件
		String resource = "SqlMapConfig.xml";
		// 得到配置文件流
		InputStream inputStream = Resources.getResourceAsStream(resource);

		// 创建会话工厂，传入mybatis的配置文件信息
		SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder()
				.build(inputStream);

		// 通过工厂得到SqlSession
		SqlSession sqlSession = sqlSessionFactory.openSession();
		// list中的user和映射文件中resultType所指定的类型一致
		List<User> list = sqlSession.selectOne("test.findUserByName", "小明");
		System.out.println(list);
		sqlSession.close();

	}
```



 

## 3.7     添加用户

 

### 3.7.1     映射文件

在 User.xml中配置添加用户的Statement

 ```xml
<!-- 添加用户 
	parameterType：指定输入参数类型是pojo（包括 用户信息）
	#{}中指定pojo的属性名，接收到pojo对象的属性值，mybatis通过OGNL获取对象的属性值
-->
	<insert id="insertUser" parameterType="cn.zxw.po.User">
		insert into user(username,birthday,sex,address) value(#{username},#{birthday},#{sex},#{address})	
	</insert>
 ```



 

### 3.7.2     程序代码

 ```java
// 添加用户信息
	@Test
	public void insertUserTest() throws IOException {
		// mybatis配置文件
		String resource = "SqlMapConfig.xml";
		// 得到配置文件流
		InputStream inputStream = Resources.getResourceAsStream(resource);

		// 创建会话工厂，传入mybatis的配置文件信息
		SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder()
				.build(inputStream);

		// 通过工厂得到SqlSession
		SqlSession sqlSession = sqlSessionFactory.openSession();
		// 插入用户对象
		User user = new User();
		user.setUsername("王小军");
		user.setBirthday(new Date());
		user.setSex("1");
		user.setAddress("河南郑州");

		sqlSession.insert("test.insertUser", user);

		// 提交事务
		sqlSession.commit();

		// 获取用户信息主键
		System.out.println(user.getId());
		// 关闭会话
		sqlSession.close();

	}
 ```



 

### 3.7.3     自增主键返回

mysql自增主键，执行insert提交之前自动生成一个自增主键。

通过mysql函数获取到刚插入记录的自增主键：

LAST_INSERT_ID()

是insert之后调用此函数。

 修改insertUser定义：

```xml
<insert id="insertUser" parameterType="cn.zxw.po.User">
		<!-- 
              将插入数据的主键返回，返回到user对象中
              SELECT LAST_INSERT_ID()：得到刚insert进去记录的主键值，只适用与自增主键
              keyProperty：将查询到主键值设置到parameterType指定的对象的哪个属性
              order：SELECT LAST_INSERT_ID()执行顺序，相对于insert语句来说它的执行顺序
              resultType：指定SELECT LAST_INSERT_ID()的结果类型
		 -->
		<selectKey keyProperty="id" order="AFTER" resultType="java.lang.Integer">
			SELECT LAST_INSERT_ID()
		</selectKey>
		insert into user(username,birthday,sex,address) value(#{username},#{birthday},#{sex},#{address})
</insert>
```

 

 

### 3.7.4     非自增主键返回(使用uuid())

 

使用mysql的uuid()函数生成主键，需要修改表中id字段类型为string，长度设置成35位。 

执行思路：

先通过uuid()查询到主键，将主键输入 到sql语句中。

执行uuid()语句顺序相对于insert语句之前执行。

 ```xml
<!-- 
      使用mysql的uuid（）生成主键
      执行过程：
      首先通过uuid()得到主键，将主键设置到user对象的id属性中
      其次在insert执行时，从user对象中取出id属性值
-->
<selectKey keyProperty="id" order="BEFORE" resultType="java.lang.String">
	SELECT uuid()
</selectKey>
insert into user(id,username,birthday,sex,address) value(#{id},#{username},#{birthday},#{sex},#{address})
		
 ```

通过oracle的序列生成主键：

```xml
<selectKey keyProperty="id" order="BEFORE" resultType="java.lang.String">
         SELECT 序列名.nextval()
 </selectKey>

insert into user(id,username,birthday,sex,address) value(#{id},#{username},#{birthday},#{sex},#{address})

```



## 3.8     删除用户

### 3.8.1     映射文件

```xml
<!--
	删除 用户
	根据id删除用户，需要输入 id值
-->
	<delete id="deleteUser" parameterType="java.lang.Integer">
		delete from user where id=#{id}
	</delete>
```



### 3.8.2     代码：

```java
// 根据id删除 用户信息
	@Test
	public void deleteUserTest() throws IOException {
		// mybatis配置文件
		String resource = "SqlMapConfig.xml";
		// 得到配置文件流
		InputStream inputStream = Resources.getResourceAsStream(resource);

		// 创建会话工厂，传入mybatis的配置文件信息
		SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder()
				.build(inputStream);

		// 通过工厂得到SqlSession
		SqlSession sqlSession = sqlSessionFactory.openSession();

		// 传入id删除 用户
		sqlSession.delete("test.deleteUser", 39);

		// 提交事务
		sqlSession.commit();

		// 关闭会话
		sqlSession.close();

	}
```



## 3.9     更新用户

### 3.9.1     映射文件

```xml
<!-- 根据id更新用户
	分析：
	需要传入用户的id
	需要传入用户的更新信息
	parameterType指定user对象，包括 id和更新信息，注意：id必须存在
	#{id}：从输入 user对象中获取id属性值
-->
	<update id="updateUser" parameterType="cn.zxw.po.User">
		update user set username=#{username},birthday=#{birthday},sex=#{sex},address=#{address} 
		 where id=#{id}
	</update>
```



### 3.9.2     代码

 ```java
// 更新用户信息
	@Test
	public void updateUserTest() throws IOException {
		// mybatis配置文件
		String resource = "SqlMapConfig.xml";
		// 得到配置文件流
		InputStream inputStream = Resources.getResourceAsStream(resource);

		// 创建会话工厂，传入mybatis的配置文件信息
		SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder()
				.build(inputStream);

		// 通过工厂得到SqlSession
		SqlSession sqlSession = sqlSessionFactory.openSession();
		// 更新用户信息
		
		User user = new User();
		//必须设置id
		user.setId(41);
		user.setUsername("王大军");
		user.setBirthday(new Date());
		user.setSex("2");
		user.setAddress("河南郑州");

		sqlSession.update("test.updateUser", user);
		
		// 提交事务
		sqlSession.commit();

		// 关闭会话
		sqlSession.close();

	}
 ```



## 3.10        总结

 

### 3.10.1              parameterType

在映射文件中通过parameterType指定输入参数的类型。



### 3.10.2              resultType

在映射文件中通过resultType指定输出结果的类型。

 

### 3.10.3              #{}和${}

 

\#{}表示一个占位符号，#{}接收输入参数，类型可以是简单类型，pojo、hashmap。

如果接收简单类型，#{}中可以写成value或其它名称。

\#{}接收pojo对象值，通过OGNL读取对象中的属性值，通过属性.属性.属性...的方式获取对象属性值。

 

${}表示一个拼接符号，会引用sql注入，所以不建议使用.

${}接收输入参数，类型可以是简单类型，pojo、hashmap。

如果接收简单类型，${}中只能写成value。

${}接收pojo对象值，通过OGNL读取对象中的属性值，通过属性.属性.属性...的方式获取对象属性值。

 

### 3.10.4  selectOne和selectList

 

selectOne表示查询出一条记录进行映射。如果使用selectOne可以实现使用selectList也可以实现（list中只有一个对象）。

 

selectList表示查询出一个列表（多条记录）进行映射。如果使用selectList查询多条记录，不能使用selectOne。

 

如果使用selectOne报错：

org.apache.ibatis.exceptions.TooManyResultsException: Expected one result (or null) to be returned by selectOne(), but found: 4

​  

# 4       mybatis开发dao的方法

 

## 4.1     SqlSession使用范围

 

### 4.1.1     SqlSessionFactoryBuilder

 通过SqlSessionFactoryBuilder创建会话工厂SqlSessionFactory

将SqlSessionFactoryBuilder**当成一个工具类使用即可**，不需要使用单例管理SqlSessionFactoryBuilder。

在需要创建SqlSessionFactory时候，只需要new一次SqlSessionFactoryBuilder即可。



### 4.1.2     SqlSessionFactory

通过SqlSessionFactory创建SqlSession，使用单例模式管理sqlSessionFactory（工厂一旦创建，使用一个实例）。

 

将来mybatis和spring整合后，使用单例模式管理sqlSessionFactory。

 

### 4.1.3     SqlSession

SqlSession是一个面向用户（程序员）的接口。

SqlSession中提供了很多操作数据库的方法：如：selectOne(返回单个对象)、selectList（返回单个或多个对象）、。

 

SqlSession是线程不安全的，在SqlSesion实现类中除了有接口中的方法（操作数据库的方法）还有数据域属性。

 

**SqlSession最佳应用场合在方法体内，定义成局部变量使用。**

 

## 4.2     原始dao开发方法



程序员需要写dao接口和dao实现类



### 4.2.1     思路

程序员需要写dao接口和dao实现类。

需要向dao实现类中注入SqlSessionFactory，在方法体内通过SqlSessionFactory创建SqlSession

 

### 4.2.2     dao接口

```java
public interface UserDao {
	
	//根据id查询用户信息
	public User findUserById(int id) throws Exception;
	
	//根据用户名列查询用户列表
	public List<User> findUserByName(String name) throws Exception;
	
	//添加用户信息
	public void insertUser(User user) throws Exception;
	
	//删除用户信息
	public void deleteUser(int id) throws Exception;
	
}
```



### 4.2.3     dao接口实现类

```java
public class UserDaoImpl implements UserDao {

   // 需要向dao实现类中注入SqlSessionFactor
   // 这里通过构造方法注入

   private SqlSessionFactory sqlSessionFactory;

   public UserDaoImpl(SqlSessionFactory sqlSessionFactory) {

      this.sqlSessionFactory = sqlSessionFactory;

   }

   @Override
   public User findUserById(int id) throws Exception {

      SqlSession sqlSession = sqlSessionFactory.openSession();
      User user = sqlSession.selectOne("test.findUserById", id);
      // 释放资源
      sqlSession.close();
      return user;
   }

 
   @Override
   public void insertUser(User user) throws Exception {

      SqlSession sqlSession = sqlSessionFactory.openSession();

      //执行插入操作
      sqlSession.insert("test.insertUser", user);
      // 提交事务
      sqlSession.commit();
      // 释放资源
      sqlSession.close();
   }

 

   @Override
   public void deleteUser(int id) throws Exception {

      SqlSession sqlSession = sqlSessionFactory.openSession();
   
      sqlSession.delete("test.deleteUser", id);
      // 提交事务
     sqlSession.commit();
      // 释放资源
      sqlSession.close();
   }
}

```



### 4.2.4     测试代码：

```java
private SqlSessionFactory sqlSessionFactory;

	// 此方法是在执行testFindUserById之前执行
	@Before
	public void setUp() throws Exception {
		
		String resource = "SqlMapConfig.xml";
		// 得到配置文件流
		InputStream inputStream = Resources.getResourceAsStream(resource);

		// 创建会话工厂，传入mybatis的配置文件信息
		sqlSessionFactory = new SqlSessionFactoryBuilder()
				.build(inputStream);
	}

	@Test
	public void testFindUserById() throws Exception {
		// 创建UserDao的对象
		UserDao userDao = new UserDaoImpl(sqlSessionFactory);

		// 调用UserDao的方法
		User user = userDao.findUserById(1);
		
		System.out.println(user);
	}
```

 

### 4.2.5     总结原始 dao开发问题

1、dao接口实现类方法中存在大量模板方法，设想能否将这些代码提取出来，大大减轻程序员的工作量。

 

2、调用sqlsession方法时将statement的id硬编码了

 

3、调用sqlsession方法时传入的变量，由于sqlsession方法使用泛型，即使变量类型传入错误，在编译阶段也不报错，不利于程序员开发。



## 4.3     mapper代理方法



程序员只需要mapper接口（相当于dao接口）

 

### 4.3.1     思路（mapper代理开发规范）

 

程序员还需要编写mapper.xml映射文件

程序员编写mapper接口需要遵循一些开发规范，mybatis可以自动生成mapper接口实现类代理对象。

 

开发规范：

1、在mapper.xml中namespace等于mapper接口地址

```xml
<!-- namespace命名空间，作用就是对sql进行分类化管理，理解sql隔离 
注意：使用mapper代理方法开发，namespace有特殊重要的作用
-->
<mapper namespace="com.zxw.mapper.UserMapper">
```



2、mapper.java接口中的方法名和mapper.xml中statement的id一致

 

3、mapper.java接口中的方法输入参数类型和mapper.xml中statement的parameterType指定的类型一致。

 

4、mapper.java接口中的方法返回值类型和mapper.xml中statement的resultType指定的类型一致。

 mapper.xml

```xml
<select id="findUserById" parameterType="int" resultType="cn.itcast.mybatis.po.User">
		SELECT * FROM USER WHERE id=#{value}
</select>
```

mapper.java

```java
public interface UserMapper {
	
	//根据id查询用户信息
	public User findUserById(int id) throws Exception;
```



总结：

以上开发规范主要是对下边的代码进行统一生成：

```java
User user = sqlSession.selectOne("test.findUserById", id);

sqlSession.insert("test.insertUser", user);

```



### 4.3.4     在SqlMapConfig.xml中加载mapper.xml

 ```xml
<mappers>
    <mapper resource = "mappper/userMapping.xml"/>
</mappers>
 ```



### 4.3.5     测试

```java
public class UserMapperTest {

	private SqlSessionFactory sqlSessionFactory;

	// 此方法是在执行testFindUserById之前执行
	@Before
	public void setUp() throws Exception {
		
		String resource = "SqlMapConfig.xml";
		// 得到配置文件流
		InputStream inputStream = Resources.getResourceAsStream(resource);

		// 创建会话工厂，传入mybatis的配置文件信息
		sqlSessionFactory = new SqlSessionFactoryBuilder()
				.build(inputStream);
	}

	@Test
	public void testFindUserById() throws Exception {
		
		SqlSession sqlSession = sqlSessionFactory.openSession();
		
		//创建UserMapper对象，mybatis自动生成mapper代理对象
		UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
		
		//调用userMapper的方法
		User user = userMapper.findUserById(1);
		System.out.println(user);
    }
	@Test
	public void testFindUserByName() throws Exception {
		
		SqlSession sqlSession = sqlSessionFactory.openSession();
		
		//创建UserMapper对象，mybatis自动生成mapper代理对象
		UserMapper userMapper = sqlSession.getMapper(UserMapper.class);	
		//调用userMapper的方法
		List<User> list = userMapper.findUserByName("小明");
		sqlSession.close();
		System.out.println(list);	
	}

}
```



 

 

### 4.3.6     一些问题总结

#### 4.3.6.1              代理对象内部调用selectOne或selectList

 

如果mapper方法返回单个pojo对象（非集合对象），代理对象内部通过selectOne查询数据库。

如果mapper方法返回集合对象，代理对象内部通过selectList查询数据库。

 

#### 4.3.6.2         mapper接口方法参数只能有一个是否影响系统 开发



mapper接口方法参数只能有一个，系统是否不利于扩展维护。



**系统框架中，dao层的代码是被业务层公用的。**

**即使mapper接口只有一个参数，可以使用包装类型的pojo满足不同的业务方法的需求。**

 

注意：持久层方法的参数可以包装类型、map。。。，service方法中建议不要使用包装类型（不利于业务层的可扩展）。

 

 

