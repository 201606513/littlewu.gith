# mybatis分页插件

## 1 原理

SqlSessionFactory ------------->SqlSession（提供很多用户方法 ）--------------->Executor（执行器对象）------------>MappedStatement(Sql语句的封装)--------->数据库

在Executor和mappedStatement之间添加（拦截器，需要实现mybatis的拦截器接口。对sql语句进行编辑。把修改后sql语句设置回去。）



## 2.使用方法

插件叫做PageHelper如果你也在用Mybatis，建议尝试该分页插件，这个一定是最方便使用的分页插件。

该插件目前支持Oracle,Mysql,MariaDB,SQLite,Hsqldb,PostgreSQL六种数据库分页。

使用方法：

第一步：需要在SqlMapConfig.xml，配置一个plugin。

第二步：在sql语句执行之前，添加一个PageHelper.startPage(page,rows);

第三步：取分页结果。创建一个PageInfo对象需要参数，查询结果返回的list。从PageInfo对象中取分页结果。



### 2.1   添加jar包到工程中

```java
<dependency>
	<groupId>com.github.pagehelper</groupId>
	<artifactId>pagehelper</artifactId>
</dependency>
```

 

### 2.2     修改SqlMapConfig.xml

 ```xml
<!-- 配置分页插件 -->
	<plugins>
		<plugin interceptor="com.github.pagehelper.PageHelper">
			<!-- 指定使用的数据库是什么 -->
			<property name="dialect" value="mysql"/>
		</plugin>
	</plugins>
 ```



### 2.3         代码测试

```java
public class TestPageHelper {
	
	@Test
	public void testPageHelper() throws Exception {
		//1、获得mapper代理对象
		ApplicationContext applicationContext = new ClassPathXmlApplicationContext("classpath:spring/applicationContext-*.xml");
		TbItemMapper itemMapper = applicationContext.getBean(TbItemMapper.class);
		//2、设置分页
		PageHelper.startPage(1, 30);
		//3、执行查询
		TbItemExample example = new TbItemExample();
		List<TbItem> list = itemMapper.selectByExample(example);
		//4、取分页后结果
		PageInfo<TbItem> pageInfo = new PageInfo<>(list);
		long total = pageInfo.getTotal();
		System.out.println("total:" + total);
		int pages = pageInfo.getPages();
		System.out.println("pages:" + pages);
		int pageSize = pageInfo.getPageSize();
		System.out.println("pageSize:" + pageSize);
		
	}
}

```



 