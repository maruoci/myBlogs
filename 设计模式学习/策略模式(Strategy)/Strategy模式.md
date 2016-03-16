# Strategy

## Java设计模式之策略模式

### 简述

 策略模式又称算法簇模式，定义了不同的算法族(行为)，并对他们分别进行封装，在运行时可动态的进行替换。策略模式使算法的变化独立于使用它们的客户。

### 设计原则

 封装变化、面向接口编程、

### 好处

 可动态改变对象行为

### 角色

 环境角色（Context）：持有策略类引用
 抽象策略（Strategy）：多个具体策略的公共接口
 具体策略（ConcreteStrategy）：实现抽象策略的相关算法和操作

### 应用场景示例

#### 示例一

  刘备要到江东娶老婆了，走之前诸葛亮给赵云（伴郎）三个锦囊妙计，说是按天机拆开能解决棘手问题，嘿，还别说，真解决了大问题，搞到最后是周瑜陪了夫人又折兵

  示例参考地址：[http://yangguangfu.iteye.com/blog/815107](http://yangguangfu.iteye.com/blog/815107)

#### 示例二

  鸭子示例，示例来源：&lt;&lt;headfirst 设计模式&gt;&gt;

  pdf下载: [http://pan.baidu.com/s/1i4gDmtf](http://pan.baidu.com/s/1i4gDmtf) (自己百度网盘)