# mybatis逆向生成

## 1、启动类

```java
import java.io.File;
import java.util.ArrayList;
import java.util.List;

import org.mybatis.generator.api.MyBatisGenerator;
import org.mybatis.generator.config.*;
import org.mybatis.generator.config.xml.*;
import org.mybatis.generator.internal.DefaultShellCallback;

public class App {

	public static void main(String[] args) throws Exception {

		String filePathName = "E:\\studyJavaWorkspace\\mybatiGenerator\\resources\\generatorConfig.xml";
		File configFile = new File(filePathName);

		List<String> warnings = new ArrayList<String>();
		boolean overwrite = true;
		ConfigurationParser cp = new ConfigurationParser(warnings);
		Configuration config = cp.parseConfiguration(configFile);
		DefaultShellCallback callback = new DefaultShellCallback(overwrite);
		MyBatisGenerator myBatisGenerator = new MyBatisGenerator(config, callback, warnings);
		myBatisGenerator.generate(null);
		System.out.println("执行完毕，请刷新");
		
	}

}

```



## 2、generatorConfig.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">
<generatorConfiguration>
    <context id="test" targetRuntime="MyBatis3" defaultModelType="flat">
    
          <!-- mybatis的自带分页插件-->
    	<plugin type="plugin.MySQLLimitPlugin"/>
    	
        <commentGenerator>
            <!-- 是否去除自动生成的注释 true：是 ： false:否 -->
            <property name="suppressAllComments" value="true"/>
            <property name="suppressDate" value="true"/> 
        </commentGenerator>
		
        <!--数据库连接的信息：驱动类、连接地址、用户名、密码 -->
        <jdbcConnection driverClass="com.mysql.jdbc.Driver" connectionURL="jdbc:mysql://localhost/schoolmanage" userId="root" password="123456"/>
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
		<javaModelGenerator targetPackage="com.zxw.po"
			targetProject=".\src">
			<!-- enableSubPackages:是否让schema作为包的后缀 -->
			<property name="enableSubPackages" value="false" />
			<!-- 从数据库返回的值被清理前后的空格 -->
			<property name="trimStrings" value="true" />
		</javaModelGenerator>
    
        <!-- targetProject:mapper映射文件生成的位置 -->
		<sqlMapGenerator targetPackage="mapper" 
			targetProject=".\resources">
			<!-- enableSubPackages:是否让schema作为包的后缀 -->
			<property name="enableSubPackages" value="false" />
		</sqlMapGenerator>
	
        <!-- targetPackage：mapper接口生成的位置 -->
		<javaClientGenerator type="XMLMAPPER"
			targetPackage="com.zxw.mapper" 
			targetProject=".\src">
			<!-- enableSubPackages:是否让schema作为包的后缀 -->
			<property name="enableSubPackages" value="false" />
		</javaClientGenerator>

        <!-- 要生成哪些表 -->     
        <table tableName="school_place" domainObjectName="SchoolPlace"
               enableCountByExample="true" enableUpdateByExample="true"
               enableDeleteByExample="true" enableSelectByExample="true"
               selectByExampleQueryId="true"
        >
        <property name="useActualColumnNames" value="false"/><!-- 取消直接使用列名，即使用驼峰命名 -->
		<!-- <columnRenamingRule searchString="^t_" replaceString=""/>   去掉列名固定前缀 -->
		</table>
		
        <table tableName="facility_category" domainObjectName="FacilityCategory"
               enableCountByExample="true" enableUpdateByExample="true"
               enableDeleteByExample="true" enableSelectByExample="true"
               selectByExampleQueryId="true"
        >
        <property name="useActualColumnNames" value="false"/><!-- 取消直接使用列名，即使用驼峰命名 -->
		<!-- <columnRenamingRule searchString="^t_" replaceString=""/> 去掉列名固定前缀 -->
		</table>
		
        <table tableName="facility" domainObjectName="Facility"
               enableCountByExample="true" enableUpdateByExample="true"
               enableDeleteByExample="true" enableSelectByExample="true"
               selectByExampleQueryId="true"
        >
        <property name="useActualColumnNames" value="false"/><!-- 取消直接使用列名，即使用驼峰命名 -->
		<!-- <columnRenamingRule searchString="^t_" replaceString=""/> 去掉列名固定前缀 -->
		</table>
		
		 <table tableName="manager" domainObjectName="Manager"
               enableCountByExample="true" enableUpdateByExample="true"
               enableDeleteByExample="true" enableSelectByExample="true"
               selectByExampleQueryId="true"
        >
        <property name="useActualColumnNames" value="false"/><!-- 取消直接使用列名，即使用驼峰命名 -->
		<!-- <columnRenamingRule searchString="^t_" replaceString=""/> 去掉列名固定前缀 -->
		</table>
    </context>
</generatorConfiguration>
```



## 3、分页类

```java
package plugin;

import java.util.List;

import org.mybatis.generator.api.*;
import org.mybatis.generator.api.dom.java.*;
import org.mybatis.generator.api.dom.xml.Attribute;
import org.mybatis.generator.api.dom.xml.TextElement;
import org.mybatis.generator.api.dom.xml.XmlElement;

public class MySQLLimitPlugin extends PluginAdapter {

	@Override
	public boolean validate(List<String> arg0) {
		// TODO Auto-generated method stub
		return true;
	}
	
	/**
	 *  为每个Example类添加limit和offset属性已经set、get方法
	*/
	@Override
	public boolean modelExampleClassGenerated(TopLevelClass topLevelClass, IntrospectedTable introspectedTable) {
	PrimitiveTypeWrapper integerWrapper = FullyQualifiedJavaType.getIntInstance().getPrimitiveTypeWrapper();
	
	Field limit = new Field();
	limit.setName("limit");
	limit.setVisibility(JavaVisibility.PRIVATE);
	limit.setType(integerWrapper);
	topLevelClass.addField(limit);
	
	Method setLimit = new Method();
	setLimit.setVisibility(JavaVisibility.PUBLIC);
	setLimit.setName("setLimit");
	setLimit.addParameter(new Parameter(integerWrapper, "limit"));
	setLimit.addBodyLine("this.limit = limit;");
	topLevelClass.addMethod(setLimit);
	
	Method getLimit = new Method();
	getLimit.setVisibility(JavaVisibility.PUBLIC);
	getLimit.setReturnType(integerWrapper);
	getLimit.setName("getLimit");
	getLimit.addBodyLine("return limit;");
	topLevelClass.addMethod(getLimit);
	
	Field offset = new Field();
	offset.setName("offset");
	offset.setVisibility(JavaVisibility.PRIVATE);
	offset.setType(integerWrapper);
	topLevelClass.addField(offset);
	
	Method setOffset = new Method();
	setOffset.setVisibility(JavaVisibility.PUBLIC);
	setOffset.setName("setOffset");
	setOffset.addParameter(new Parameter(integerWrapper, "offset"));
	
	setOffset.addBodyLine("this.offset = offset;");
	topLevelClass.addMethod(setOffset);
	
	Method getOffset = new Method();
	getOffset.setVisibility(JavaVisibility.PUBLIC);
	getOffset.setReturnType(integerWrapper);
	getOffset.setName("getOffset");
	getOffset.addBodyLine("return offset;");
	topLevelClass.addMethod(getOffset);
	
	return true;
	}
	
	/**
	 * 为Mapper.xml的selectByExample添加limit
	*/
	@Override
	public boolean sqlMapSelectByExampleWithoutBLOBsElementGenerated(XmlElement element, IntrospectedTable introspectedTable) {
	XmlElement ifLimitNotNullElement = new XmlElement("if");
	ifLimitNotNullElement.addAttribute(new Attribute("test", "limit!= null"));
	
	XmlElement ifOffsetNotNullElement = new XmlElement("if");
	ifOffsetNotNullElement.addAttribute(new Attribute("test", "offset != null"));
	ifOffsetNotNullElement.addElement(new TextElement("limit ${offset}, ${limit}"));
	ifLimitNotNullElement.addElement(ifOffsetNotNullElement);

	XmlElement ifOffsetNullElement = new XmlElement("if");
	ifOffsetNullElement.addAttribute(new Attribute("test", "offset== null"));
	ifOffsetNullElement.addElement(new TextElement("limit ${limit}"
	));
	ifLimitNotNullElement.addElement(ifOffsetNullElement);
	
	element.addElement(ifLimitNotNullElement);
	
	 return true;
	}

}

```



## 4、在generatorConfig.xml中注入分页类

```x,l
<!-- mybatis的自带分页插件-->
<plugin type="plugin.MySQLLimitPlugin"/>
```

