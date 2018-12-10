

### 1.	jdbc问题总结如下：

1、	数据库链接创建、释放频繁造成系统资源浪费从而影响系统性能，如果使用数据库链接池可解决此问题。

2、	Sql语句在代码中硬编码，造成代码不易维护

3、	结果集获取与遍历复杂，存在硬编码，不利于维护，期望能够查询后返回一个java对象

### 2、	MyBatis介绍
综合jdbc存在的问题引入mybatis

MyBatis是一个优秀的持久层框架，它对jdbc的操作数据库的过程进行封装，使开发者只需要关注 SQL本身，而不需要花费精力去处理例如注册驱动、创建connection、创建statement、手动设置参数、结果集检索等jdbc繁杂的过程代码。
Mybatis通过xml或注解的方式将要执行的各种statement（statement、preparedStatemnt、CallableStatement）配置起来，并通过java对象和statement中的sql进行映射生成最终执行的sql语句，最后由mybatis框架执行sql并将结果映射成java对象并返回。

mybaits的代码由github.com管理，地址：https://github.com/mybatis/mybatis-3/releases

### 3、	mybatis和hibernate本质区别和应用场景

hibernate：是一个标准ORM框架（对象关系映射）。入门门槛较高的，不需要程序写sql，sql语句自动生成了。对sql语句进行优化、修改比较困难的。

应用场景：适用与需求变化不多的中小型项目，比如：后台管理系统，erp、orm、oa。。

mybatis：专注是sql本身，需要程序员自己编写sql语句，sql修改、优化比较方便。mybatis是一个不完全的ORM框架，虽然程序员自己写sql，mybatis 也可以实现映射（输入映射、输出映射）。
应用场景：适用与需求变化较多的项目，比如：互联网项目。











