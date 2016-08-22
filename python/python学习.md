# python 

## 1 python 核心
### 前沿
+ python 是龟叔  Guido van Rossum 1989 年编写的编程语言。
+ python的优缺点：
	+ 优点：简单，明确，优雅。适用于网络应用包括网站及后台服务；日常工具包括系统管理员的脚本任务；包装其他语言开发的程序。
	+ 缺点：python作为解释性语言，执行速度慢。 代码无法加密。
+ python是跨平台的，python有多种解释器。比如：CPython(官方),IPython,PyPy,Jython 等等。
### 1.1 语法基础 (7.30 ~ 7.31)
+ 1.1.1 输入和输出
	+ 输入：
		<pre><code>
		//当你输入name = input()并按下回车后，Python交互式命令行就在等待你的输入了。这时，你可以输入任意字符，然后按回车后完成输入。
		>>> name = input()  
			Michael
		>>> print(name)
		   'Michael'
		</code></pre>
	+ 输出：
		`print('hello','world')`

-----------------------------------------------------

+ 1.1.2 数据类型
	+ 整数
	+ 浮点数。1.5e4 
	+ 字符串。'' , "" 表示，需要转义的用\、\t、\n，还可以用r''标识''内的内容不转义。
	+ 布尔值。 True False 可以用and or 和 not来对布尔值进行运算
	+ 空值。 None
	+ 变量。
	+ 常量。
	
>编码：ASCII编码、Unicode编码、UTF-8。ASCII编码是1个字节，而Unicode编码是两个字节，把所有语言都统一到一套编码里。  
>字母A，用ASCII编码是十进制的65，二进制的01000001;Unicode编码是00000000 01000001。  
>而UTF-8则是可变长的编码。

-----------------------------------------------------
+ 1.1.3 List
  + list是一种有序的集合，可以随时添加和删除其中的元素。``` list = ['java','Js','Hadoop']```。
  + 变量list是一个集合，len()函数可以获得list个数。``` len(list)```。
  + 用索引可以获取list的每个元素。list[0]获取第一个元素。list[-1]可以获取倒数第一个元素。
  + 可以在指定位置添加元素。list.insert(1,'python'),此时集合变为``['java','python','Js','Hadoop']``。
  + 可以通过list.pop(),删除末尾元素，或删除指定位置元素。list.pop(1)删掉上步添加的python。``['java','Js','Hadoop']``。
  + 可以直接通过`list[1]='PHP'`直接替换元素。``['java','PHP','Hadoop']``。
  + list集合元素可以有不同的数据类型，诸如`[123,True,None,'Java']`,也可以在list集合内引用list，诸如`['三年一班','张老师',['张三','李四','王五']]`	。

 -----------------------------------------------------
+ 1.1.4 Tuple
  + tuple 是list外的另一有序数组，与list极其类似，不过tuple是不可变的。也就是说tuple一旦定义后，不能修改。它没有insert和pop方法。
  + 当tuple只有一个元素时，需定义为`t = (1,)`,以避免与数学计算混淆。
  + 可变的tuple。
   		<pre><code>>>> t = ('a' , 'b' , ['X','Y'])
		>>>t[2][0] = 'MK'
		>>>t[2][1] = 'KX'
		>>> t 
		('a' , 'b' , ['MK','KX']) //tuple的不可变指每个元素的指向不变。
  		</code></pre>

+ 1.1.5 条件判断
	<pre><code> a = 19
	 if a >= 20 :
	   print('你的年龄有20岁了！')
	 elif a <= 18:
	   print('你的年龄还不够18呢！')
	 else:
	   print('老夫掐指一算，少年现在应该是19岁了')
	</code></pre>

+ 1.1.6 循环

	<pre><code> // 方法一 
	 skills = ['Java','Jsp','Servlet','Struts','Spring','Hibernate']
	 for skill in skills
	   print(skill)  
	 // 方法二:高斯算法
	 sum = 0 
	 n = 0
	 while n < 101:
	    sum = sum + n
        n = n + 1 
	print('当前的高斯算的是5050，我现在算的是',sum)
	</code></pre>

+ 1.1.7 dict 
	+ 相比之前的list，dict查找与插入的速度极快。不会随着key的变多而变慢。
	+ dict需要占用大量的内存。内存浪费较多。
	+ 总结来说：dict就是拿空间换时间的利器。
	<pre><code> // dict也就是键值对 
	 mySkill = {'Java':90,'Js':60,'Hadoop':20,'Python':10}
	 print(mySkill['Java']) //90
	 mySkill['asp'] = 0  	//往dict里放入一个asp值为0
	 mySkill['asp'] = 2  	//往dict里放入一个asp值为2，冲掉之前的0
	 'asp' in mySkill       //可以判断dict中是否有asp的key。True False
	 mySkill.get('asp') 	//可以判断dict中是否有asp的key。None 或对应的值
     mySkill.get('asp',-1) 	//可以判断dict中是否有asp的key。-1 或对应的值
	 mySkill.pop('Js')      //可以删除Js这个key-value
	</code></pre>

+ 1.1.8 set
	+	set与dict相似，也是一组key的集合。set中没有重复的key。
	<pre><code> // set无序 
	 >>> st = set([1,2,3,3,2,4])
	 >>> st
	  {1,2,3,4}           //重复元素自动过滤
	 >>> st.add(5)
	 >>> st
	  {1,2,3,4,5}        //添加一个元素
     >>> st.add(5)
	 >>> st
	  {1,2,3,4,5}        //重复添加，没有作用
	 >>> st.remove(5)
	 >>> st
	  {1,2,3,4}          //删除一个元素
	 >>> ab = set([3,4,5])
	 >>> st && ab 
	  {3,4}              //两个集合可以求交集，并集
     >>> st | ab
      {1,2,3,4,5}
	</code></pre>

### 1.2 函数 07.31
> 首先python内置了很多函数，可以通过官方网站直接查看:[Python3官方文档](!http://docs.python.org/3/library/functions.html#abs)  
> 另外也可以在交互式命令行，通过`help(abs)`查看`abs`函数的具体使用方法。

+ 1.2.1 函数调用  
 函数名其实是指向一个函数对象的引用，可以完全把函数名附给一个变量。相当于给函数起了个别名。
	 <pre><code>
	 >>>a = abs    //变量a指向abs函数
	 >>>a(-20)     //通过a调用abs函数
	 </code></pre>

+ 1.2.2 函数定义
   + `from fileName import functionName` 可以引入已经写好的函数。后续会详细介绍使用方法。
   + `pass`可以来定义一个空函数。已空函数来作为占位符，让代码暂时先运行起来。
   + python解释器会为函数自动进行参数个数检查，但如果参数类型不对的话，则需要自己在定义函数的时候手动编码进行类型校验。
   + 函数在返回时可以返回多个结果，实际上函数返回的是一个tuple，不过在语法上，tuple的`()`可以省略。
	<pre><code> //def 来定义函数，以冒号开始
	 def myfunction(param):
         if not isinstance(param , (int , float )):
             raise TypeError('ERROR：这不是一个数字')
         if(param > 0 ):
             return "这是个正数"
         else:
             return "这是个负数"
	</code></pre>

+ 1.2.3 函数的参数
	+  位置参数，默认参数，可变参数，关键字参数，命名关键字参数。
	+  参数调用时，必选参数在前，默认参数在后。
	+  定义默认参数时，默认参数变量必须指向不可变对象。
	+  可变参数在接收一个tuple对象时，可以`calc(*tp)`来调用。

    <pre><code>// x 为位置参数， y为默认参数
    def my_power(x , y = 2 ):
	   if not isinstance ( x , (int ,float )):
		  raise TypeError('Error : 类型错误')
	   tmp = 1 
	   while y > 0:
		  tmp = x * tmp;
		  y = y - 1
	   return tmp
   //numbers为可变参数，calc(1,2,3)
   def calc(*numbers):
      sum = 0
      for n in numbers:
        sum = sum + n * n
      return sum
    </code></pre>
+ 参数： 位置参数，默认参数，可变参数，关键字参数，命名关键字参数
	+ 递归： 汉诺塔举例。递归
	+ input函数：

递归示例：
	
	## 汉诺塔的递归本质：
	###  当n = 1 时， 直接从A 移动 到 c
	###  当 n > 1 时，则分为三步：将n-1从A借助c移到B，将n从a移到c，将n-1从b借助a 移到c
	
	def move(n ,  fro , to ):
		print("将第%d个盘子 从%s 移动 到了 %s " % (n , fro , to ) )
	
	def moven(n , a , b , c ):
		if n == 1 :
			move( n , a , c )
		else :
			moven(n-1, a , c , b )
			move(n , a , c )
			moven(n-1, b , a , c)
	
	
	n = input('请输入您要移动的盘子个数:')
	n = int(n)
	moven(n,'a','b','c')
### 1.3 高级特性 08.22
* 1.3.1 切片  
  可以对list、tuple的部分元素进行操作。
* 1.3.2 迭代  
  可以通过collection模块的Iterable类型判断。  
	
		>>> from collections import Iterable
		>>> isinstance('abc',Iterable) #str 是否可迭代    True
		>>> isinstance([1,2,3],Iterable) # list是否可迭代 True
		>>> isinstance(123 , Iterable)   # 整数是否可迭代. False
		
  
* 1.3.3 列表生成式  

		L = list(range(1,11)] #生成1-10的list
		import os 
		L = [d for d in os.listdir('.')] #列出当前目录下的所有目录名和文件
		N = ['App' , 'Java' ,12, 'Hello']
		M = [s.lower() for s in N if isinstance(s , str)] #生成
* 1.3.4 生成器

	生成器和列表生成式类似，但受内存影响，列表容量是有限的。我们通过创建一个生成器，按某种算法实现。这样可以不必创建完整的list。从而节省大量控件。  
	难度指数：★★★★
* 1.3.5 迭代器  
	
	for循环操作的都是Iterable类型对象。  
	next操作的都是Iterator类型对象。
### 1.4 函数式编程 08.23
> 函数式编程的一个特点就是，允许把函数本身作为参数传入另一个函数，还允许返回一个函数！  
 Python对函数式编程提供部分支持。由于Python允许使用变量，因此，Python不是纯函数式编程语言。

* 1.4.1 高阶函数  
	* 函数返回值可以赋给变量。 f = abs(10).
	* 变量可以指向函数。 f = abs //abs 为取绝对值函数，把abs函数赋给f
	* 函数名可以做为参数传递给下一个函数。
	

			def add(x,y ,f):
				return f(x) + f(y)


* 1.4.2 python中的map/reduce
 
       map()函数接收两个参数，一个是函数，一个是Iterable。map将传入的函数依次作用到序列的每个元素。并把结果作为新的Iterable返回。  
	   reduce()同样接受俩参数，一个是函数，一个是Iterable，函数必须接收两个参数，将序列中的两个变量作用的结果作为下一个变量做累积计算。
	
		def f(x):
			return x*x	//求x的平方
		map(f , [1,2,3,4,5,6,7]) //得到[1,4,9,16,25,36,49]
		def add(x,y):
			return x+y
		reduce(add , [1,2,3,4])  //得到10
  
* 1.4.3 filter
### 1.5 模块
### 1.6 面向对象编程
### 1.7 面向对象高级编程 
### 1.8 调试与测试
### 1.9 IO编程
### 1.10 进程与线程
### 1.11 正则
### 1.12 常用内建模块
### 1.13 常用第三方模块
### 1.14 virtualenv
### 1.15 图形界面
### 1.16 网络编程
### 1.17 电子邮件
### 1.18 数据库访问
### 1.19 web开发
### 1.20 异步IO

## 2 python 实战


