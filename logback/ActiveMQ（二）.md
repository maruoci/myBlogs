---
title: 'ActiveMQ 学习（二） '
date: 2016-01-13 16:44:33
categories: 消息队列 
tags: [ActiveMQ,Jms]
---

## ActiveMQ(二)

### ActiveMQ集群
参考地址：http://blog.csdn.net/kimmking/article/details/13768367  
ActiveMQ的集群分为消费者集群和提供者集群。即Consumer和Broker的集群。
1.	Consumer的集群，使用Queue即可做到某种意义的消费者集群，所有消费者共同处理同一类消息。发布-订阅类的消息，非持久化的情况时，目前无法集群；针对持久化的发布-订阅，可以通过Composite Destination（组合目标）机制来转换成针对具体的持久消费者的专用queue，从而实现多个消费者共同处理同一类消息。  
2.	Consumer的高可用，可以通过failover和Discovery Transport来实现。
	1.	当broker故障，会自动连接备用broker
	2.	断线重连机制，哪怕只有一个broker，也可以用来在broker down掉重启后，客户端重新连接上。
3.	broker的集群。
	1.	network-connector
	2.	master-slave
		1.	Pure Master-slave 5.9后已废弃，简单的主从复制。
		2.	Shared File System Master-slave
		3.	JDBC Master-slave
```xml
    -- network-connector的静态配置方式:
    --brokerA:
    <networkConnectors></networkConnectors>
    <transportConnectors>
     <transportConnector name="openwire" uri="tcp://0.0.0.0:61616"/>
    </transportConnectors>
    --brokerB: 这是个单向的连接，brokerB单向的连接到brokerA，连接brokerB的消费者可以消费生产者发送到brokerA的消息。
    <networkConnectors>
    <networkConnector uri="static:(tcp://localhost:61616)" duplex="true"/>
    </networkConnectors>
    <transportConnectors>
      <transportConnector name="openwire" uri="tcp://0.0.0.0:61618"/>
</transportConnectors>
```

4.	broker的高可用。master-slave实现broker的高可用

### ActiveMQ的高级特性