---
title: 'ActiveMQ 学习（一） '
date: 2016-01-04 23:37:10
categories: 消息队列 
tags: [ActiveMQ,Jms]
photos:
- http://bruce.u.qiniudn.com/2013/11/27/reading/photos-1.jpg
---
### 基础概念理解
* JMS：Java消息服务。属于java消息中间件服务的一个标准和API定义。
* MQ：Message Queue消息队列，MS的一个标准，通常用于企业级应用的消息传递。主要有topic消息(订阅)和queue消息(p2p)。而MQ是遵循AMQP协议的具体实现和产品。大部分的MQ产品都实现了JMS API，MQ产品类似与JMS的服务器。包括（ActiveMQ、RabbitMQ、RocketMQ，以及新兴的NSQ
* ActiveMQ ：支持JMS，底层实现使用NIO的一个MQ产品。配置比较复杂，代码量较大。应用协议：AMQP、XMPP、Notification、REST、OpenWire、Stomp、WS。
### JMS的一些简单梳理
1.	JMS定义了两种消息传递域，点对点(Peer-to-peer)和发布-订阅(publish-subscribe)
2.	ptp中，每个消息只能有一个消费者，目的地被称为队列(queue)，发布订阅时，类似于群发，目的地被称为主题(topic)。
3.	消息的消费有同步(receive)或异步两种方式(注册消息监听)。
4.	消息主要分为消息头，消息属性和消息体三部分，消息类型有TextMessage、MapMessage、ByteMessage、StreamMessage和ObjectMessage。
5.	消息成功消费经历三阶段，接收消息，处理消息，消息被确认。事务性会话中producer，事务提交后，消息自动确认。非事务性会话，消息的确认取决于应答模式。
6.	session 创建时指定应答模式，AUTO_ACKNOWLEDGE/CLIENT_ACKNOWLEDGE/DUPS_OK_ACKNOWLEDGE。
7.	消息持久化，JMS支持两种消息提交模式，NON_PERSISTENT和PERSISTENT。
### 简单示例
* 安装[ActiveMQ](http://activemq.apache.org/download.html)。
* activeMQ启动服务的主要流程：
```java
     //consumer
     //获得JMS connection factory. 通过我们提供特定环境的连接信息来构造factory。
     JmsConnectionFactory connectionFactory = new JmsConnectionFactory("amqp://localhost:5672");
     //利用factory构造JMS connection
     Connection connection = connectionFactory.createConnection("admin","password");
     //启动connection
     connection.start();
     //通过connection创建JMS session
     Session session = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
     //指定JMS destination
     Destination destination = session.createTopic(ConnectUtil.topic);
     //创建JMS consumer或注册JMS message listener
     MessageConsumer consumer = session.createConsumer(destination);
	 
     while(true){
	//发送或接收JMS message
        TextMessage msg = (TextMessage) consumer.receive();
     }
     //关闭所有JMS资源。connection、session、producer、consumer等
     //publisher
     JmsConnectionFactory connectionFactory = new JmsConnectionFactory("amqp://localhost:5672");
     Connection connection = connectionFactory.createConnection("admin","password");
     connection.start();
     Session session = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
     Destination dest = session.createTopic(ConnectUtil.topic);
     //创建JMS producer或者创建JMS message并提供destination.
     MessageProducer producer = session.createProducer(dest);
     //考虑持久化的操作
     producer.setDeliveryMode(DeliveryMode.NON_PERSISTENT);
     TextMessage message = session.createTextMessage();
     message.setText("PublishTest" + count);
     producer.send(message);

```
### ActiveMQ
#### broker
*	Running Broker: `~/bin/win64/activemq.bat`,运行可直接启动一个broker。此外可以通过broker ConfigurationURI 或broker Xbean URI对broker进行配置。
```xml
   activemq.bat xbean:myconfig.xml  -- 使用myconfig.xml作为broker配置文件来启动broker。
   activemq xbean:file:./conf/broker1.xml
   activemq xbean:file:C:/ActiveMQ/conf/broker2.xml
   activemq broker:(tcp://localhost:61616,tcp://localhost:5000)?useJmx=true
   activemq broker:(tcp://localhost:61616,network:tcp://localhost:5000)?persistent=false
```

*  Embedded Broker : 嵌入式中间件。
```java
  //---编码方式启动broker。
  BrokerService service = new BrokerService();
  service.setBrokerName("test2"); //如果需要启动多个broker，需要为broker设置name
  service.addConnector("tcp://localhost:61616");
  service.start();
  
  ---如果希望在同一个jvm中访问这个broker，可以通过VM Transport,URI是vm://brokeName
  
  --- 此外也可以通过BrokerFactory来创建broker
  String someURI = "broker:tcp://localhost:61610";
  BrokerService service1 = BrokerFactory.createBroker(new URI(someURI));
  ---someURI的可选值有：xbean:activemq.xml、file:foo/bar/activemq.xml、broker://tcp://localhost:61616
  xbean举例： 
   编码：BrokerService broker = BrokerFactory.createBroker(new URI("xbean:com/test/activemq.xml"));
   spring配置：
　　<bean id="broker" class="org.apache.activemq.xbean.BrokerFactoryBean">
  　　<property name="config" value="classpath:org/apache/activemq/xbean/activemq.xml" />
  　　<property name="start" value="true" />
　　</bean> 
```

* Monitoring Broker 监听
```xml
   -----**JMX监听，使用前需要启用broker的JMX监控功能。**
   --比如new URI("broker:tcp://localhost:61610?useJmx=true")
   --或者配置方式的：
   <broker useJmx="true" brokerName="broker1>
   <managementContext>
      <managementContext createConnector="true"/>
   </managementContext>
      ...
   </broker>
   -- 然后使用JDK自带的jconsole。选择JMX URL：
   -- service:jmx:rmi:///jndi/rmi://localhost:1099/jmxrmi （不需用户密码）
   -- 可以自己在conf/jmx.access，jmx.password文件内配置用户、密码,并修改启动脚本。
   set SUNJMX=-Dcom.sun.management.jmxremote.port=1616
	-Dcom.sun.management.jmxremote.authenticate=true
	-Dcom.sun.management.jmxremote.ssl=false
	-Dcom.sun.management.jmxremote.password.file=%ACTIVEMQ_BASE%/conf/jmx.password
	-Dcom.sun.management.jmxremote.access.file=%ACTIVEMQ_BASE%/conf/jmx.access
    --重启，重新进入。如果出现：Error: Password file read access must be restricted:
    --参考：http://java.sun.com/j2se/1.5.0/docs/guide/management/security-windows.html

	---**WebConsole 被集成进ActiveMQ中，可http://localhost:8161/admin直接访问。**
    ---可修改conf/jetty.xml内的jettyPort来修改web console缺省端口,如果需要单独部署web console
	---则下载源码，进入${base}/src/activemq-web-console目录执行mvn install,拿war包部署到webserver中
	---部署还需要其他jar包支持和属性设置，详细配置请查阅其他资料。或者后续补充。	

    ---**Advisory Message**
    ---** Command Agent**
```

> ActiveMQ 支持 Advisory Messages，它允许你通过标准的 JMS 消息来监控系统。目前的 Advisory Messages 支持：
> consumers, producers and connections starting and stopping
> temporary destinations being created and destroyed
> messages expiring on topics and queues
> brokers sending messages to destinations with no consumers.
> connections starting and stopping

#### Transport
目前，ActiveMQ支持的transport有VM、TCP、SSL、Peer、UDP、MultiCast、HTTP、HTTPS、FailOver、FailOut、Discovery、ZeroConf。
1.	VM Transport：允许在VM内部通信，避免了网络开销，这时候的连接是方法调用，而不是socket。例如`vm://broker1?marshal=false&broker.persistent=false`
2.	TCP Transport:允许客户端通过TCP Socket连接到远程的broker。比如`tcp://localhost:61616?trace=false`
3.	Failover Transport:是一种重连机制。它工作于其他transport上层，用于建立可靠的传输。它允许定制任意多个复合的URI。Failover Transport会自动连接其中的一个URI。如果没有成功，会选择一个其他的URI建立新连接。比如：`failover:(tcp://localhost:61616,tcp://remotehost:61616)?initialReconnectDelay=100`
4.	Discovery Transport:是可靠的transport，它使用Discovery transport来定位用来连接的URI列表。比如：`discovery:(multicast://default)?initialReconnectDelay=100`  


#### ActiveMQ模型
* broker： 消息中转器。ActiveMQ的核心，它接收信息并进行相关处理后分发给消息消费者。
* RegionBroker 负责分发broker操作到相应的消息区域
* Region：ActiveMQ目前主要有4种消息区域。队列（QueueRegion）、主题（TopicRegion）、临时队列（TempQueueRegion)、临时主题（TempTopicRegion）
* TransportConnection：通讯连接
* Destination：消息目的地，主要包括queue、topic两种
* Subscription：消息的消费者和订阅者
* MessageStore：消息持久化存储，像比较复杂的kaha存储机制就在这。
* PendingMessageCursor：等待发给消费者的分发指针。
* ConnectionContext：维护发送请求所需的连接上下文。
#### ActiveMQ模型分析
1.	一个RegionBroker拥有4种消息域的对象
2.	RegionBroker拥有所有目的地对象(destination)。
3.	每个消息域也拥有他们对应的0或n个目的地对象
4.	同时每个消息域也拥有对应的0或n个消息消费者、订阅者(subscription)。
5.	每个目的地都有一个相应的持久化存储（messageStore）、以及一个等待发送的消息分发指针。（PendingMessageCursor）
6.	消息消费者和目的地可以彼此拥有0或n个
7.	每个消费者都有一个对应的ConnectionContext，ConnectionContext里包含一个TransportConnection，通过TransportConnection把真实消息发送给消费者
8.	TransportConnection也可以作为通讯链接，侦听消息生产者发出的消息。所以每个TransportConnection都指向broker对象。
