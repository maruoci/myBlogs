# MyBatis 通用Mapper的配置及使用

Mapper组件官网地址：[http://git.oschina.net/free/Mapper](http://git.oschina.net/free/Mapper)  

优点：极大的方便开发人员，方便使用MyBatis单表的增删改查

### 示例场景

　　MyBatis3.2.8
　　Spring4.0.5
　　Mapper3.2.1(注意：3.2.1和之前版本在集成spring时是不同的)
　　MySql

### 准备工作

首先搭建了一个干净的SpringMVC+MyBatis的集成框架。
[http://blog.csdn.net/gebitan505/article/details/44455235](http://blog.csdn.net/gebitan505/article/details/44455235)
PS: (框架集成文档稍后再补)

### Mapper配置步骤

*   导入依赖jar包。

```xml
    <dependency>
        <groupId>javax.persistence</groupId>
        <artifactId>persistence-api</artifactId>
        <version>1.0</version>
    </dependency>
    <dependency>
        <groupId>tk.mybatis</groupId>
        <artifactId>mapper</artifactId>
        <version>3.2.1</version>
    </dependency>
```

*   新建Mapper父接口继承MySqlMapper接口。

```
	import tk.mybatis.mapper.common.Mapper;
    import tk.mybatis.mapper.common.MySqlMapper;

    public interface BaseMapper<T> extends Mapper<T>,MySqlMapper<T>{

    }
```

*   UserMapper业务mapper继承BaseMapper
    `public interface UserMapper extends BaseMapper<User>{

    }
    `

*   实体类添加注解映射
   `... ...
    @Table(name="user")
    public class User {
        @Id
        @GeneratedValue(generator="JDBC")
        private Integer id;

        private String name;
    ... ...
    `

*   集成到Spring
    `<bean class="tk.mybatis.spring.mapper.MapperScannerConfigurer">
            <property name="basePackage" value="com.test.IDao"/>
            <property name="properties">
                <value>
                    mappers=tk.mybatis.mapper.common.Mapper
                </value>
            </property>
        </bean>
    `
*   单元测试

    访问：[http://localhost:8080/demo1/user/showUser](http://localhost:8080/demo1/user/showUser)
输出到控制台的结果证明已经成功使用Mapper。

### 示例demo：

*   修改jdbc.properties里的MySql配置文件。
*   库内新建表User(**注意添加注解配置**)

### 后记

　　暂时就想了这么多，我也是刚跑通就过来先整理了。稍后如果还有其他再补充。