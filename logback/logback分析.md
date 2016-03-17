---
title: 'logback原理分析 '
date: 2016-01-13 16:44:33
categories: 消息队列 
tags: [ActiveMQ,Jms]
---
## OutputStreamAppender
	
OutputStreamAppender的继承关系：
![outputStream的上下层关系](http://logback.qos.ch/manual/images/chapters/appenders/appenderClassDiagram.jpg)
> 它添加了一个事件到OutputStream上，这个类提供了其他继承该类的appender的一些基础服务。用户一般不直接实例化该appender对象。因为一般的java.io.outputstream类型不能方便地映射到一个字符串，因为没有办法在配置脚本指定目标输出流对象。简单地说，你不能从一个配置文件中配置一个outputstreamappender。然而，这并不意味着outputstreamappender缺乏可配置属性。它包含一个encoder属性。

从顶层的doAppender()方法，进入，这里用到了**模版方法**的设计模式，确保Appender已启动，然后进入append方法，然后进入下一个subAppend()方法，执行writeOut()方法调的encoder.doEncode()方法。
<pre><code>
	public void doEncode(E event) throws IOException {
	  String txt = layout.doLayout(event);	//委托layout把日志事件转换为字符串
	  outputStream.write(convertToBytes(txt)); //output输出
	  if (immediateFlush)
		outputStream.flush();
	}
</code></pre>
### FileAppender
FileAppender首先覆写start()方法，判断一系列参数配置，然后根据文件名新建FileOutPutStream，设置到outputStrea。start()方法执行完毕。
然后append方法是调用OutputStream内的append，用start()内构建的文件流把日志信息写入到文件中。