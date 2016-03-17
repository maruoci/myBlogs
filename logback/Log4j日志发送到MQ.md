---
title: 'log4j发送日志到MQ '
date: 2016-01-13 16:59:33
categories: 消息队列 
tags: [ActiveMQ,Jms]
---


应用场景：将log4j的日志发送到专门用于记录日志的日志服务器。  
1. 可以集中管理日志：可以把多台服务器上的日志都发送到一台日志服务器上，方便管理和分析。  
2. 可以减轻服务器的开销：日志不在服务器上了，因此服务器有更多可用的磁盘空间。  
3. 可以提高服务器的性能：通过异步方式，记录日志时服务器只负责发送消息，不关心日志记录的时间和位置，服务器甚至不关心日志到底有没有记录成功

具体实现原理：
>项目A需要打印日志，而A调用Log4j来打印日志，Log4j的JMSAppender又给配置的地址（ActiveMQ地址）发送一条JMS消息，此时绑定在Queue上的项目B的监听器发现有消息到来，于是立即唤醒监听器的方法开始输出日志。

#### DEMO
1. 环境准备
2. 安装[ActiveMQ](http://activemq.apache.org/download.html)。下载，直接解压。进入bin目录，双击activemq.bat。
3. 看到下图信息，即成功安装。
4. 输入localhost:8161/，点击`Manage ActiveMQ broker`,用户名/密码：admin/admin,进入管理界面。
```xml
INFO | ActiveMQ WebConsole available at http://0.0.0.0:8161/
INFO | ActiveMQ Jolokia REST API available at http://0.0.0.0:8161/api/jolokia/
```
1. Maven构建log监听项目B。
2. 主要4个文件。pom.xml、web.xml、spring.xml和监听类LogMessageListener
3. 文件配置如图：
```xml
/** pom 文件 */
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
	    <modelVersion>4.0.0</modelVersion>
		<groupId>com.test.mk</groupId>
		<artifactId>logging</artifactId>
		<packaging>war</packaging>
		<version>0.0.1-SNAPSHOT</version>
		<name>logging Maven Webapp</name>
		<url>http://maven.apache.org</url>
		<dependencies>
		<!-- Use to cast object to LogEvent when received a log -->
		<dependency>
			<groupId>log4j</groupId>
			<artifactId>log4j</artifactId>
			<version>1.2.17</version>
		</dependency>
		<!-- Use to receive jms message -->
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-jms</artifactId>
			<version>4.0.0.RELEASE</version>
		</dependency>
		<!-- Use to load spring.xml -->
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-web</artifactId>
			<version>4.0.0.RELEASE</version>
		</dependency>
		<!-- ActiveMQ lib -->
		<dependency>
			<groupId>org.apache.activemq</groupId>
			<artifactId>activemq-core</artifactId>
			<version>5.7.0</version>
		</dependency>
	</dependencies>
	<build>
		<finalName>logging</finalName>
	</build>
</project>
/*** web.xml 配置 */
<!DOCTYPE web-app PUBLIC
 "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
 "http://java.sun.com/dtd/web-app_2_3.dtd" >
<web-app>
  <display-name>Archetype Created Web Application</display-name>
   <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:spring.xml</param-value>
    </context-param>    
    <!-- Use to load spring.xml -->
    <listener>
        <listener-class>
            org.springframework.web.context.ContextLoaderListener
        </listener-class>
    </listener>   
    <welcome-file-list>
        <welcome-file>index.jsp</welcome-file>
    </welcome-file-list>
</web-app>
/*** spring 配置*/
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
    xsi:schemaLocation="
        http://www.springframework.org/schema/beans 
        http://www.springframework.org/schema/beans/spring-beans-4.0.xsd">
    <bean id="jmsTemplate" class="org.springframework.jms.core.JmsTemplate">
        <property name="connectionFactory" ref="connectionFactory"/>
    </bean>
    <bean id="connectionFactory" class="org.springframework.jms.connection.SingleConnectionFactory">
        <property name="targetConnectionFactory" ref="targetConnectionFactory"/>
    </bean>
    <bean id="targetConnectionFactory" class="org.apache.activemq.ActiveMQConnectionFactory">
        <property name="brokerURL" value="tcp://localhost:61616"/>
    </bean>
<!-- As JMSAppender only support the topic way to send messages, 
    thus queueDestination here is useless.
    <bean id="queueDestination" class="org.apache.activemq.command.ActiveMQQueue">
        <constructor-arg name="name" value="queue" />
    </bean>
 -->
    <bean id="topicDestination" class="org.apache.activemq.command.ActiveMQTopic">
   	 <constructor-arg name="name" value="logTopic" />
       <!--       <constructor-arg name="name" value="testLog" />  -->
    </bean>
    <bean id="jmsContainer" class="org.springframework.jms.listener.DefaultMessageListenerContainer">
        <property name="connectionFactory" ref="connectionFactory" />
        <!-- <property name="destination" ref="queueDestination" />  -->
        <property name="destination" ref="topicDestination" />
        <property name="messageListener" ref="logMessageListener" />
    </bean>
    <bean id="logMessageListener" class="com.demo.logging.LogMessageListener"/>
</beans>
// java 监听类
package com.demo.logging;
import javax.jms.Message;
import javax.jms.MessageListener;
import org.apache.activemq.command.ActiveMQObjectMessage;
import org.apache.log4j.spi.LoggingEvent;
public class LogMessageListener implements MessageListener {
public void onMessage(Message message) {
    try {
        // receive log event in your consumer
        LoggingEvent event = (LoggingEvent)((ActiveMQObjectMessage)message).getObject();
        System.out.println("Logging project: [" + event.getLevel() + "]: "+ event.getMessage());
    } catch (Exception e) {
        e.printStackTrace();
    }
}
}
```
1. 构建项目A，日志生成的项目。
2. 主要4个文件。pom.xml、Main.java、jndi.properties、log4j.properties
```xml
=============pom.xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
   <modelVersion>4.0.0</modelVersion>
   <groupId>com.test.mk</groupId>
   <artifactId>Product</artifactId>
   <version>0.0.1-SNAPSHOT</version>
   <name>Product</name>

   <dependencies>
    <dependency>
      <groupId>log4j</groupId>
      <artifactId>log4j</artifactId>
      <version>1.2.17</version>
    </dependency>

    <!-- log4j use this lib -->
    <dependency>
      <groupId>org.slf4j</groupId>
      <artifactId>slf4j-log4j12</artifactId>
      <version>1.7.13</version>
    </dependency>

    <!-- spring jms lib -->
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-jms</artifactId>
      <version>4.0.0.RELEASE</version>
     </dependency>

     <!-- ActiveMQ lib -->
     <dependency>
       <groupId>org.apache.activemq</groupId>
       <artifactId>activemq-core</artifactId>
       <version>5.7.0</version>
     </dependency>
</dependencies>
</project>
=============log4j.properties
log4j.rootLogger=INFO, stdout, jms
  
## Be sure that ActiveMQ messages are not logged to 'jms' appender
log4j.logger.org.apache.activemq=INFO, stdout
  
log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern=%d %-5p %c - %m%n
  
## Configure 'jms' appender. You'll also need jndi.properties file in order to make it work
log4j.appender.jms=org.apache.log4j.net.JMSAppender
log4j.appender.jms.InitialContextFactoryName=org.apache.activemq.jndi.ActiveMQInitialContextFactory
log4j.appender.jms.ProviderURL=tcp://localhost:61616
###log4j.appender.jms.TopicBindingName=logTopic
log4j.appender.jms.TopicBindingName=logTopic
log4j.appender.jms.TopicConnectionFactoryBindingName=ConnectionFactory
=============jndi.properties
topic.logTopic=logTopic
```
```java
=============Main.java
package com.demo.product;
import org.apache.log4j.Logger;
public class Main2 {
 private static final Logger logger = Logger.getLogger(Main.class);
    public static void main(String[] args) throws Exception {
	// just log a message
	logger.info("Info Log.");
	logger.warn("Warn Log");
	logger.error("Error Log.");
	System.exit(0);
    }
}

```
1. 启动logging项目，然后允许main方法。可以看到logging的控制台监听到main方法的日志，并输出了。  

参考地址：http://www.linuxidc.com/Linux/2015-12/126163.htm