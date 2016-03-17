---
title: 'logback配置说明 '
date: 2016-01-13 17:00:00
categories: 消息队列 
tags: [Jms]
---


## logback配置
参考地址：  
http://blog.csdn.net/haidage/article/details/6794529  
http://blog.csdn.net/haidage/article/details/6794509  
http://logback.qos.ch/manual/appenders.html#JMSTopicAppender
### 根节点configuration
scan:
当此属性设置为true时，配置文件如果发生改变，将会被重新加载，默认值为true。  
scanPeriod:
设置监测配置文件是否有修改的时间间隔，如果没有给出时间单位，默认单位是毫秒。当scan为true时，此属性生效。默认的时间间隔为1分钟。  
debug:
当此属性设置为true时，将打印出logback内部日志信息，实时查看logback运行状态。默认值为false。
<pre><code>&lt;configuration scan="true" scanPeriod="60 seconds" debug="false">
&lt;/configuration>
</code></pre>

### 子节点
#### &lt;property>
设置变量，变量定义的值会被引入到logger的上下文中。  
定义变量后可使用${}来使用。
<pre><code>&lt;property name="APP_Name" value="myAppName" />   
&lt;contextName>${APP_Name}&lt;/contextName>  
</code></pre>
实际项目中，使用了文件引入：
<pre><code>&lt;property resource="app.properties"/>
--app.properties配置了多个变量
</code></pre>

#### &lt;logger>和&lt;root>  

&lt;logger>用来设置某一个包或者具体的某一个类的日志打印级别、以及指定&lt;appender>。&lt;loger>仅有一个name属性，一个可选的level和一个可选的addtivity属性。

* name :用来指定受此loger约束的某一个包或者具体的某一个类
* level:用来设置打印级别，大小写无关：TRACE, DEBUG, INFO, WARN, ERROR, ALL 和 OFF，还有一个特俗值INHERITED或者同义词NULL,代表强制执行上级的级别。<font color=red>如果未设置此属性，那么当前loger将会继承上级root的级别。</font>
* addtivity:是否向上级loger传递打印信息。<font color=red>默认是true</font>。 如果level设置了也会被传递给上级。 

&lt;loger>可以包含零个或多个<appender-ref>元素，标识这个appender将会添加到这个loger。  
&lt;root>也是&lt;loger>元素，但是它是根loger。只有一个level属性，因为已经被命名为"root"。level不能设置为INHERITED或者同义词NULL。默认是DEBUG。
#### &lt;apender>
&lt;encoder>：appender的子节点，负责将日志信息转换成字节数组，把字节数组写入到输出流。
<pre><code>--示例
&lt;encoder>
    &lt;pattern>%d{yyyy-MM-dd HH:mm:ss} [%thread] %-5level %logger{36} - %msg%n&lt;/pattern>
&lt;/encoder>
--pattern设置日志的输入格式，使用% 加 转换符 的格式。
</code></pre>

##### ConsoleAppender
ConsoleAppender把日志输出到控制台。
<pre><code>&lt;configuration>
 &lt;appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
  &lt;encoder>	&lt;!--encoder:对日志进行格式化-->
   &lt;pattern>%-4relative [%thread] %-5level %logger{35} - %msg %n&lt;/pattern>
   &lt;!--pattern:设置日志的输入格式，使用% 加 转换符 的格式-->
  &lt;/encoder>
 &lt;/appender>
 &lt;root level="DEBUG">
  &lt;appender-ref ref="STDOUT" />
 &lt;/root>
&lt;/configuration></code></pre>

##### FileAppender
FileAppender:把日志输出到文件
<pre><code>&lt;configuration>
 &lt;appender name="FILE" class="ch.qos.logback.core.FileAppender">
   &lt;!-- 被写入的文件名，相对目录或绝对目录都行，上级目录不存在会自动创建，没有默认值-->
   &lt;file>testFile.log&lt;/file>
   &lt;!--如果是 true，日志追加到文件结尾，false，则清空原有，默认是true-->
   &lt;append>true&lt;/append>
   &lt;!--如果是 true，支持多个程序写入该log文件。执行效率低-->
   &lt;prudent>true&lt;/prudent>
   &lt;encoder>
     &lt;pattern>%-4relative [%thread] %-5level %logger{35} - %msg%n&lt;/pattern>
   &lt;/encoder>
 &lt;/appender>
 &lt;root level="DEBUG">
   &lt;appender-ref ref="FILE" />
 &lt;/root>
&lt;/configuration></code></pre>

##### RollingFileAppender
RollingFileAppender：滚动记录文件，将日志记录到指定文件，当符合条件时，记到其他文件。  
**这里的滚动，个人不太好理解，我自己觉得改为分割文件的策略要好理解些。**
配置滚动文件时，需要同时配置滚动策略及触发策略。

* rollingPolicy-->TimeBasedRollingPolicy:  最常用滚动策略，**它同时实现了RollingPolicy和TriggeriingPolicy两个接口**

<pre>&lt;appender name="FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
 &lt;file>logFile.log&lt;/file>
 &lt;rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
   &lt;!-- daily rollover -->
   &lt;fileNamePattern>logFile.%d{yyyy-MM-dd}.log&lt;/fileNamePattern>
   &lt;!-- keep 30 days' worth of history -->
   &lt;maxHistory>30&lt;/maxHistory>
 &lt;/rollingPolicy>
 &lt;encoder>
   &lt;pattern>%-4relative [%thread] %-5level %logger{35} - %msg%n&lt;/pattern>
 &lt;/encoder>
&lt;/appender> 
&lt;root level="DEBUG">
 &lt;appender-ref ref="FILE" />
&lt;/root><code>
</code></pre>

常用的分割文件的格式设定：
| FileNamePattern	                   |    Value		              |  
| :-----------------------------------:| :---------------------------:|  
| $dir/logFile.%d	                   |   by day		              |  
| $dir/%d{yyyy/MM}/logFile.txt	       |   by month		              |  
| $dir/logFile.%d{yyyy-ww}.log         |   by first day of week       |  
| $dir/logFile%d{yyyy-MM-dd_HH}.log    |   by hour		              |  
| $dir/logFile%d{yyyy-MM-dd_HH-mm}.log |   by minute		          |


* rollingPolicy-->FixedWindowRollingPolicy：根据固定算法重命名文件的滚动策略。
* triggeringPolicy-->SizeBasedTriggeringPolicy：查看文件大小，根据文件大小滚动。  
<pre><code>&lt;configuration>
   &lt;appender name="FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
    &lt;file>test.log&lt;/file>
    &lt;l!-- 滚动策略 -->
    &lt;rollingPolicy  class="ch.qos.logback.core.rolling.FixedWindowRollingPolicy">
      &lt;fileNamePattern>test.%i.log.zip&lt;/fileNamePattern>
      &lt;minIndex>1&lt;/minIndex>
      &lt;maxIndex>3&lt;/maxIndex>
    &lt;/rollingPolicy>	
    &lt;!-- 触发策略-->
    &lt;triggeringPolicy class="ch.qos.logback.core.rolling.SizeBasedTriggeringPolicy">
      &lt;maxFileSize>10MB&lt;/maxFileSize>
    &lt;/triggeringPolicy>
    &lt;encoder>
      &lt;pattern>%-4relative [%thread] %-5level %logger{35} - %msg%n&lt;/pattern>
    &lt;/encoder>
  &lt;/appender>       
  &lt;root level="DEBUG">
    &lt;appender-ref ref="FILE" />
  &lt;/root>
&lt;/configuration></code></pre>

##### JMSTopicAppender
一个简单的appender，发布事件到一个JMS topic。该事件会被序列化并作为一个JMS message传递。
<pre><code>&lt;appender name="jmsTopicTest" class="ch.qos.logback.classic.net.JMSTopicAppender">
  &lt;InitialContextFactoryName>
    org.apache.activemq.jndi.ActiveMQInitialContextFactory
  &lt;/InitialContextFactoryName>
  &lt;ProviderURL>tcp://localhost:61616&lt;/ProviderURL>
  &lt;TopicBindingName>logTopic&lt;/TopicBindingName>
  &lt;TopicConnectionFactoryBindingName>ConnectionFactory&lt;/TopicConnectionFactoryBindingName>
&lt;/appender>
--监听时，需采用LoggingEventVO接收
</code></pre>