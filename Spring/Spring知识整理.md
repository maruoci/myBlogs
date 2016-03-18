## 整理
### 基础知识
 
 * Spring的7个核心模块：Core、Context、Web、MVC、AOP、ORM、DAO。
 ![图](http://image.it168.com/cms/2007-10-31/Image/2007103193225.jpg)
   1. Core:    核心容器提供 Spring 框架的基本功能。核心容器的主要组件是 BeanFactory，它是工厂模式的实现。BeanFactory 使用控制反转 （IOC） 模式将应用程序的配置和依赖性规范与实际的应用程序代码分开。
   2. Context: Spring 上下文是一个配置文件，向 Spring框架提供上下文信息。Spring上下文包括企业服务，例如 JNDI、EJB、电子邮件、国际化、校验和调度功能。
   3. Web：              Web包提供基本的面向web的综合特性,如Multipart,使用Servlet监听Context的初始化和面向Web的Application Context。整合Struts时用此包进行整合。
   4. MVC:     提供面向WEB的mvc的实现。
   5. AOP:     提供面向切面的编程实现，允许定义拦截器和切点，来对逻辑上应该被分离的功能实现解耦。
   6. ORM:     为关系对象映射提供了集成层，包括JDO，Hibernate和Ibatis。通过orm包可以与Spring提供的其他特性结合使用这些对象/关系映射，比如简单声明式事务管理。
   7. DAO:     提供了JDBC的抽象层

### BeanFactory 与 ApplicationContext

　　ApplicationContext建立在BeanFactory之上，更容易同AOP整合,消息资源等方面的增强。BeanFactory提供了配置框架和基本功能。而ApplicationContext为他增加了更强的功能。一般来说，ApplicationContext是BeanFactory的完全超集。J2EE应用中最好使用ApplicationContext。

　　#### BeanFactory实例化的两种方式
	
   ```java
   		
	   	ClassPathResource res = new ClassPathResource("beans.xml");    		//方法一 
		XmlBeanFactory factory = new XmlBeanFactory(res);
		
		ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("beans.xml"); //方法二
		BeanFactory factory = context;
		
		DemoBean1 bean1 = (DemoBean1) factory.getBean("demoBean1");
   ```
   
   * bean中的class指定了需创建的类，大多数beanFactory是直接new出该bean。
   * bean中id是必须唯一的且不可使用特殊符号，name可以是多个用都逗号分开.通过id和name都可以取到bean。

  #### ApplicationContext实例化的方式
  
   两种方式基本相同，listener不能兼容servlet2.2
  
   ```xml
     
     <context-param>
	   <param-name>contextConfigLocation</param-name>
	   <param-value>classpath:applicationContext-mybatis.xml</param-value>
	 </context-param>
     
     <!-- 1 : ContextLoaderListener 监听的方式加载ApplicationContext -->	
	 <listener>
	   <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
	 </listener>
   	
   	 <!-- 2 : ContextLoaderServlet 方式 -->
   	 <servlet>
   	  <servlet-name>context</servlet-name>
   	  <servlet-class>org.springframework.web.context.ContextLoaderServlet</servlet-class>
   	  <load-on-startup>1</load-on-startup>
   	 </servlet>
   		
   ```
     
  		   
### MVC

### 事务

### AOP
 
  Spring针对J2EE中大部分能用AOP解决的问题，提供了一个优秀的解决方案。它默认使用JDK动态代理。
 
  ####　概念 
  
  * AOP : 面向切面编程 Aspect oreinted Programming 。
  * Aspect ： 切面
  * advice ：通知
  * PointCut：切入点
  *　AOP代理
 
 #### 通知类型
 
  通知包括：Around通知，Before通知，Throws通知，After returnning通知 
 
 #### AOP实现
 
 AOP的实现主要有以下几种实现方式，基于代理，纯JAVA对象切面，@Aspect注解，注入形式的Aspect切面。下面逐一尝试。
 
 1. 基于代理的AOP。三步走，定义接口及业务实现，定义通知及切点，配置代理。
 
  示例：请客吃饭，先通知大家时间地点，然后胡吃海喝，吃完各回各家。
 
 2. 
 