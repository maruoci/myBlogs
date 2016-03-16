# SpringMVC与MyBatis的集成

### 版本说明

　Spring4.0.5
　MyBatis3.2.8
　SpringMVC4.0.5

### 搭建步骤

*   新建Maven的web项目。
*   Pom文件导入需要引入的jar包。
  
```xml  
<properties>
	<spring.version>4.0.5.RELEASE</spring.version>
	<mybatis.version>3.2.8</mybatis.version>
</properties>

<dependencies>
	<dependency>
		<groupId>junit</groupId>
		<artifactId>junit</artifactId>
		<version>3.8.1</version>
		<scope>test</scope>
	</dependency>

	<!-- Spring包 start -->
	<dependency>
		<groupId>org.springframework</groupId>
		<artifactId>spring-core</artifactId>
		<version>${spring.version}</version>
	</dependency>

	<dependency>
		<groupId>org.springframework</groupId>
		<artifactId>spring-web</artifactId>
		<version>${spring.version}</version>
	</dependency>
	<dependency>
		<groupId>org.springframework</groupId>
		<artifactId>spring-oxm</artifactId>
		<version>${spring.version}</version>
	</dependency>
	<dependency>
		<groupId>org.springframework</groupId>
		<artifactId>spring-tx</artifactId>
		<version>${spring.version}</version>
	</dependency>

	<dependency>
		<groupId>org.springframework</groupId>
		<artifactId>spring-jdbc</artifactId>
		<version>${spring.version}</version>
	</dependency>

	<dependency>
		<groupId>org.springframework</groupId>
		<artifactId>spring-webmvc</artifactId>
		<version>${spring.version}</version>
	</dependency>
	<dependency>
		<groupId>org.springframework</groupId>
		<artifactId>spring-aop</artifactId>
		<version>${spring.version}</version>
	</dependency>

	<dependency>
		<groupId>org.springframework</groupId>
		<artifactId>spring-context-support</artifactId>
		<version>${spring.version}</version>
	</dependency>

	<dependency>
		<groupId>org.springframework</groupId>
		<artifactId>spring-test</artifactId>
		<version>${spring.version}</version>
	</dependency>

	<!-- MyBatis包 -->
	<dependency>
		<groupId>org.mybatis</groupId>
		<artifactId>mybatis</artifactId>
		<version>${mybatis.version}</version>
	</dependency>

	<!-- mybatis/spring包 -->
	<dependency>
		<groupId>org.mybatis</groupId>
		<artifactId>mybatis-spring</artifactId>
		<version>1.2.2</version>
	</dependency>

	<!-- MySql -->
	<dependency>
		<groupId>mysql</groupId>
		<artifactId>mysql-connector-java</artifactId>
		<version>5.1.22</version>
	</dependency>
	<!-- dbcp配置数据库 -->
	<dependency>
		<groupId>commons-dbcp</groupId>
		<artifactId>commons-dbcp</artifactId>
		<version>1.4</version>
	</dependency>

	<!-- qita 核心包 -->
	<dependency>
		<groupId>javax.servlet</groupId>
		<artifactId>jstl</artifactId>
		<version>1.1.2</version>
	</dependency>
	<dependency>
		<groupId>taglibs</groupId>
		<artifactId>standard</artifactId>
		<version>1.1.2</version>
	</dependency>
</dependencies>
```

* 新建JDBC.properties（修改编码为UTF-8）  

```xml
	driver=com.mysql.jdbc.Driver
	url=jdbc:mysql://127.0.0.1:3306/mydemo
	username=root
	password=123456
	#定义初始连接数 
	initialSize=0  
	#定义最大连接数  
	maxActive=20
	#定义最大空闲 
	maxIdle=20
	#定义最小空闲 
	minIdle=1
	#定义最长等待时间  
	maxWait=60000
```
      
* 集成Spring与Mybatis
 <font color=red>context:component-scan可以自动去扫描base-pack下面或者子包下面的java文件，如果扫描到有@Component @Controller@Service等这些注解的类，则把这些类注册为bean</font>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!-- 文件存放的spring与mybatis整合的配置文件 -->

<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:p="http://www.springframework.org/schema/p"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:mvc="http://www.springframework.org/schema/mvc"
	xsi:schemaLocation="http://www.springframework.org/schema/beans    
          http://www.springframework.org/schema/beans/spring-beans-3.1.xsd    
          http://www.springframework.org/schema/context    
          http://www.springframework.org/schema/context/spring-context-3.1.xsd    
          http://www.springframework.org/schema/mvc    
          http://www.springframework.org/schema/mvc/spring-mvc-4.0.xsd">

	<!-- 自动扫描 -->
	<context:component-scan base-package="com.test" />

	<!-- 引入配置文件 -->
	<bean id="propertyConfigurer"
		class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">
		<property name="location" value="classpath:jdbc.properties" />
	</bean>

	<bean id="dataSource" class="org.apache.commons.dbcp.BasicDataSource"
		destroy-method="close">
		<property name="driverClassName" value="${driver}" />
		<property name="url" value="${url}" />
		<property name="username" value="${username}" />
		<property name="password" value="${password}" />
		<!-- 初始化连接大小 -->
		<property name="initialSize" value="${initialSize}"></property>
		<!-- 连接池最大数量 -->
		<property name="maxActive" value="${maxActive}"></property>
		<!-- 连接池最大空闲 -->
		<property name="maxIdle" value="${maxIdle}"></property>
		<!-- 连接池最小空闲 -->
		<property name="minIdle" value="${minIdle}"></property>
		<!-- 获取连接最大等待时间 -->
		<property name="maxWait" value="${maxWait}"></property>
	</bean>

	<!-- spring和MyBatis完美整合，扫描目录下的所有mapper的映射文件 -->
	<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
		<property name="dataSource" ref="dataSource" />
		<!-- 自动扫描mapping.xml文件 -->
		<property name="mapperLocations" value="classpath:com/test/mapper/*.xml"></property>
	</bean>

	<!-- Spring会自动扫描目录下的所有Mapper java文件 -->
	<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
		<property name="basePackage" value="com.test.IDao" />
		<property name="sqlSessionFactoryBeanName" value="sqlSessionFactory"></property>
	</bean>

	<!-- (事务管理)transaction manager, use JtaTransactionManager for global tx -->
	<bean id="transactionManager"
		class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
		<property name="dataSource" ref="dataSource" />
	</bean>
	
</beans>  
```

* Junit测试
 1. 建表User
 2. 利用MyBatis Generator自动生成Mapper及Mapper.xml文件。
 3. 编写service方法。
 4. 编写test方法。测试成功。

* 集成SpringMVC，添加mvc的配置文件applicationContext-mvc.xml

```xml
	<!-- 自动扫描该包，使SpringMVC认为包下用了@controller注解的类是控制器 -->
	<context:component-scan base-package="com.test.controller" />

	<!-- 定义跳转的文件的前后缀 ，视图模式配置 -->
	<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
		<!-- 这里的配置我的理解是自动给后面action的方法return的字符串加上前缀和后缀，变成一个 可用的url地址 -->
		<property name="prefix" value="/WEB-INF/jsp/" />
		<property name="suffix" value=".jsp" />
	</bean>
```

* 配置web.xml

```xml
	<!-- Spring和mybatis的配置文件 -->
	<context-param>
		<param-name>contextConfigLocation</param-name>
		<param-value>classpath:applicationContext-mybatis.xml</param-value>
	</context-param>

	<!-- 编码过滤器 -->
	<filter>
		<filter-name>encodingFilter</filter-name>
		<filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
		<async-supported>true</async-supported>
		<init-param>
			<param-name>encoding</param-name>
			<param-value>UTF-8</param-value>
		</init-param>
	</filter>
	<filter-mapping>
		<filter-name>encodingFilter</filter-name>
		<url-pattern>/*</url-pattern>
	</filter-mapping>
	<!-- Spring监听器 -->
	<listener>
		<listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
	</listener>
	<!-- 防止Spring内存溢出监听器 -->
	<listener>
		<listener-class>org.springframework.web.util.IntrospectorCleanupListener</listener-class>
	</listener>

	<!-- Spring MVC servlet -->
	<servlet>
		<servlet-name>SpringMVC</servlet-name>
		<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
		<init-param>
			<param-name>contextConfigLocation</param-name>
			<param-value>classpath:applicationContext-mvc.xml</param-value>
		</init-param>
	<load-on-startup>1</load-on-startup>
	<async-supported>true</async-supported>
	</servlet>
	<servlet-mapping>
		<servlet-name>SpringMVC</servlet-name>
		<!-- 此处可以可以配置成*.do，对应struts的后缀习惯 -->
		<url-pattern>/</url-pattern>
	</servlet-mapping>
```

* 测试
	1、新建test.jsp
	2、新建TestController
	3、部署访问

### 参考资料

![<context:component-scan>使用说明](http://blog.csdn.net/chunqiuwei/article/details/16115135)
![mapper自动生成文件](http://blog.csdn.net/zhshulin/article/details/23912615)
![SSM框架整合](http://blog.csdn.net/gebitan505/article/details/44455235)