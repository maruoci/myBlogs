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

      <span class="hljs-tag"><<span class="hljs-title">dependency</span>></span>
        <span class="hljs-tag"><<span class="hljs-title">groupId</span>></span>javax.persistence<span class="hljs-tag"></<span class="hljs-title">groupId</span>></span>
        <span class="hljs-tag"><<span class="hljs-title">artifactId</span>></span>persistence-api<span class="hljs-tag"></<span class="hljs-title">artifactId</span>></span>
        <span class="hljs-tag"><<span class="hljs-title">version</span>></span>1.0<span class="hljs-tag"></<span class="hljs-title">version</span>></span>
      <span class="hljs-tag"></<span class="hljs-title">dependency</span>></span>
      <span class="hljs-tag"><<span class="hljs-title">dependency</span>></span>
        <span class="hljs-tag"><<span class="hljs-title">groupId</span>></span>tk.mybatis<span class="hljs-tag"></<span class="hljs-title">groupId</span>></span>
        <span class="hljs-tag"><<span class="hljs-title">artifactId</span>></span>mapper<span class="hljs-tag"></<span class="hljs-title">artifactId</span>></span>
        <span class="hljs-tag"><<span class="hljs-title">version</span>></span>3.2.1<span class="hljs-tag"></<span class="hljs-title">version</span>></span>
      <span class="hljs-tag"></<span class="hljs-title">dependency</span>></span>
    `</pre>

*   新建Mapper父接口继承MySqlMapper接口。
    <pre>`    <span class="hljs-keyword">import</span> tk.mybatis.mapper.common.Mapper;
        <span class="hljs-keyword">import</span> tk.mybatis.mapper.common.MySqlMapper;

        <span class="hljs-keyword">public</span> <span <span class="hljs-class"><span class="hljs-keyword">class</span></span>=<span class="hljs-string">"hljs-class"</span>><span class="hljs-class"><span class="hljs-keyword">interface</span> <span class="hljs-title">BaseMapper</span><<span class="hljs-title">T</span>> <span class="hljs-keyword">extends</span> <span class="hljs-title">Mapper</span><<span class="hljs-title">T</span>>,<span class="hljs-title">MySqlMapper</span><<span class="hljs-title">T</span>></span>{

        }
    `</pre>

*   UserMapper业务mapper继承BaseMapper
    <pre>`<span class="hljs-keyword">public</span> <span <span class="hljs-class"><span class="hljs-keyword">class</span></span>=<span class="hljs-string">"hljs-class"</span>><span class="hljs-class"><span class="hljs-keyword">interface</span> <span class="hljs-title">UserMapper</span> <span class="hljs-keyword">extends</span> <span class="hljs-title">BaseMapper</span><<span class="hljs-title">User</span>></span>{

        }
    `</pre>

*   实体类添加注解映射
    <pre>`<span class="hljs-keyword">...</span> <span class="hljs-keyword">...</span>
        @Table(name=<span class=<span class="hljs-string">"hljs-string"</span>><span class="hljs-string">"user"</span>)
        public class User {
            @Id
            @GeneratedValue(generator=<span class=<span class="hljs-string">"hljs-string"</span>><span class="hljs-string">"JDBC"</span>)
            private Integer id;

            private String name;
        <span class="hljs-keyword">...</span> <span class="hljs-keyword">...</span>
    `</pre>

*   集成到Spring
    <pre>`<bean <span <span class="hljs-keyword">class</span>=<span class="hljs-string">"hljs-attribute"</span>><span class="hljs-keyword">class</span>=<span <span class="hljs-keyword">class</span>=<span class="hljs-string">"hljs-value"</span>><span class="hljs-string">"tk.mybatis.spring.mapper.MapperScannerConfigurer"</span>>
                <property <span <span class="hljs-keyword">class</span>=<span class="hljs-string">"hljs-attribute"</span>>name=<span <span class="hljs-keyword">class</span>=<span class="hljs-string">"hljs-value"</span>><span class="hljs-string">"basePackage"</span> <span <span class="hljs-keyword">class</span>=<span class="hljs-string">"hljs-attribute"</span>><span class="hljs-keyword">value</span>=<span <span class="hljs-keyword">class</span>=<span class="hljs-string">"hljs-value"</span>><span class="hljs-string">"com.test.IDao"</span>/>
                <property <span <span class="hljs-keyword">class</span>=<span class="hljs-string">"hljs-attribute"</span>>name=<span <span class="hljs-keyword">class</span>=<span class="hljs-string">"hljs-value"</span>><span class="hljs-string">"properties"</span>>
                    <<span class="hljs-keyword">value</span>>
                        mappers=tk.mybatis.mapper.common.Mapper
                    </<span class="hljs-keyword">value</span>>
                </property>
            </bean>

*   单元测试

    访问：[http://localhost:8080/demo1/user/showUser](http://localhost:8080/demo1/user/showUser)
输出到控制台的结果证明已经成功使用Mapper。

### 示例demo：

*   修改jdbc.properties里的MySql配置文件。
*   库内新建表User(**注意添加注解配置**)

### 后记

　　暂时就想了这么多，我也是刚跑通就过来先整理了。稍后如果还有其他再补充。