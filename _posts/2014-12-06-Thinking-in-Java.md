---
layout: post
title: Thinking in Java 第四版 笔记
category: notes
description: Thinking in Java，中文名Java编程思想。学习Java必备书籍，难度适中，最大特点在于作者是站在语言设计者的角度,讲解Java的设计原理，正如标题所阐明的——THINKING in Java。做笔记的目的，一则方便查阅，二则在于精简书本的内容，方便浏览。时常看看，以备不时之需。
---

* 目录
{:toc}

# 第二章 一切都是对象

##2.1 一些特性
C++使用virtual实现方法的动态绑定（后期绑定），Java中的方法默认是动态绑定。

标识符是对象的一个“引用”。引用不一定需要有一个对象与它关联。

##2.2 Java内存结构
参考 深入理解Java虚拟机，那本书讲得更透彻。具体参见[lll](http)

1. PC Register(PC寄存器)——不能直接控制
2. JVM栈——基本类型和对象引用
3. 堆——所有Java对象
4. 方法区域（MethodArea）——存放了所加载的类的信息（名称、修饰符等）
5. 运行时常量池——常量值，字符串常量置于特殊的静态存储区
6. 本地方法堆栈——用于支持native方法的执行


##2.3 基本数据类型

基本数据类型的变量直接存储值而不是引用。

* boolean —— 无规定，只能为true或false。
* char —— **16 bit** 提供对Unicode的支持。参见[Java中的字符]()
* byte —— 8 bit
* short —— 16 bit
* int —— 32 bit
* long —— 64 bit
* float —— 32 bit
* double —— 64 bit
* void —— 无规定

char,byte,short 在参与数学和按位运算时会自动转换为int类型。  
所有数值类型都有正负号，每种基本类型都有对应的包装器类型(基本类型存储于堆栈中，包装器类型存储于堆中)，两者之间可以互相转换。

	Character ch = 'x';
	char c = ch;

有两个用于高精度计算的类：

* BigInteger 任意精度整数
* BigDecimal 任意精度定点数

##2.4 初始化
Java中类成员有默认初始化值。  
C/C++中类成员没有默认初始化值。  

局部变量，Java和C/C++都没有默认初始化值。但使用前，若没有初始化，Java编译会返回一个错误，C/C++一般会给予警告。  
Java中没有全局变量。C/C++中的全局变量有默认初始化。

数组是对象，所以初始化时内存会被初始化为0。  

* 若数组元素是基本数据类型：全部初始化为0;  
* 若数组元素是对象：存储的是对象的引用，全被初始化为null。  


##2.5 作用域

C/C++中，小作用域中的变量可以“隐藏”外层大作用域中的变量，但Java中不允许。

	{
		int x = 12;
		{
			int x = 1; // Illegal
		}
	}
	
基本类型和对象引用都有有限的生命周期，严格满足大括号规定的作用域。但引用消失后，它指向的对象仍会占据内存。



##2.6 参数传递

Java中函数的参数传递总是传"值"。基本类型拷贝数据，引用类型拷贝对象的引用。使用swap交换两个引用时，若使用
	
	swap(Object i, Object j) {Object t = i; i = j; j = t;}
	
交换的只是两个形参的引用。此处必须使用实参的引用

	swap(Object[] a, int i, int j) {Object t = a[i]; a[i] = a[j]; a[j] = t;}

##2.7 消除向前引用
  
Java 只要是一个类中的成员变量或成员函数都可以被引用。  
C/C++ 	只有在当前成员**之前**定义的成员才能被当前成员引用。

##2.8 static成员或方法
可以用一个对象使用，s.i。或者使用类名直接引用，String.i。  

##2.9 import
必须在文件开头，类定义之外。  
Java中import并不导入类，只是声明了类的查找路径，编译时按需导入使用到的类。  
C/C++中的include直接导入文件的所有内容。

##2.10 类名
类的名字必须和文件名相同。
main函数必须是：`public static void main(String[] args){}`  
其中`args[0]`是程序的第一个参数，而在C/C++中是程序名。

##2.11 命令行编译
javac命令： 将.java源文件编译为.class文件  
java命令: 执行.class文件（参数不需要后缀）  
命令的使用参见[JAVA和JAVAC命令][javacmd]

[javacmd]: http://zhuyuanhao.com/Java-and-javac-command-line/ "Java and Javac command"

# 第三章 操作符

##3.1 赋值
基本数据类型：复制值  
对象：复制引用，引起“别名现象”（aliasing）。在使用对象做函数参数时，也是复制引用。

##3.2 整数除法
截取整数部分，和C/C++相同。

##3.3 Random类
`Random(long seed)`如果创建过程中没有参数，以当前时间作为种子。相同种子能产生相同随机数序列。  

`nextBoolean(),nextInt(),nextInt(int i),nextLong(),
nextFloat(),nextDouble(),nextGaussian(),nextBytes(byte[] bytes)`  
 获取随机数,浮点数得到[0,1)的值，整型得到区间内的值{-MaxValue-1,MaxValue}，nextInt可设置区间为[0,i)。   
 
`void setSeed(long seed)`重置种子。   
_注：`Math.random()`方法随机产生一个[0,1)的double型值。_

##3.4 ==和!=
对于对象，比较两者的引用是否相等。

equals()方法是所有对象都使用的特殊方法，用于比较两个对象的实际内容是否相同。但equals()方法的默认行为是比较引用，所以在自定义的类中需要覆盖原equals()方法。

##3.5 布尔类型
可用于

* 逻辑操作符（短路）: `&& || ! `  
* 部分按位操作符(做单比特，结果还是boolean，不会短路): `& | ^ `
* 不可用于按位非~和移位操作  

与C/C++不同，Java不可以将一个非布尔值当作布尔值在逻辑表达式中使用。布尔类型只能转为String。

##3.6 直接常量

* l L: long
* f F: float
* d D: double
* 0x9aF2或0X9aF2: 16进制数
* 076: 8进制数
* 253: int型整数，超出int范围时，加L表示long型整数。
* 1.39e-3: 默认是double型指数
* 1.39e-3f: float型指数

将一个变量初始化为超出变量范围的值，编译器会报告错误。  
float f4 = 1e-43f;  

打印二进制字符串: 

	Integer.toBinaryString() 
	Long.toBinaryString()

##3.7 移位操作
只能处理整数类型

	<< 左移
	>> 有符号右移
	>>> 无符号右移

char,byte,short,int移位之前会被转换为int类型，得到的结果也是int类型。对表示移位位数的数值，只有低5为有效，高于5位的部分忽略。  
long类型移位，结果也是long类型，移位数值的低6位有效。

复合赋值'>>>='，对于byte,short,char类型，由于中间会先提升为int型，最后会被截断赋值。

	int i = -1 >>> 10; // i的结果是4194303
	byte t = -1;
	t >>>= 10; // t结果还是-1
	
所以最后可能得到不同的结果。

##3.8 +和+=作用于字符串的连接
'+'从左往右依次运算，若一个String类型后面跟着'+'，会把后面的数据都作为字符串拼接。若前面有一个'+'，会把之前的数据先运算，再作为String和当前String拼接，其中非String类型通过调用自己的toString()函数转换，null引用没有toString()方法，但会输出字符串"null"。

	1+2+"s" // "3s"
	"s"+1+2 // "s12"
	"s"+(1+2) // "s3"
	""+1: "1" // 简单的转换为字符串的方法

##3.9 与C/C++运算符的异同
Java的逻辑运算&&和||也会短路。  
Java没有运算符重载函数。  
Java没有sizeof()操作符。

##3.10 窄化转换(narrowing conversion)
窄化转换时，编译器会强制要求使用显示类型转换(cast)。  
float或double转换为整型值时，总是截尾。若想用舍入，需用java.lang.Math.round()方法。参见[Math的用法][math usage]  
char,byte,short在执行算数操作和按位运算之前，都会被转换为int类型。

	char i = 5;
	i = i + 5; // compile error
	i = (char) (i + 5); // ok
	i += 5; // ok
	
复合赋值时，虽然中间结果是int，但不需要显示类型转换(cast)。  

##3.11 优先级
![Java操作符优先级][java operator]

[math usage]: http://
[java operator]: http://zhuyuanhao.com/images/java/java_operator.png

#第四章 控制流程

##4.1 条件表达式
Java控制语句只能用布尔表达式做判定条件。
C/C++可以用数字做布尔值。

##4.2 逗号
做操作符：定义多个**相同类型**的变量，在for语句的初始化语句和步进(step)语句中。或在普通定义语句中。

	for(int i=1,j=i; i<5; i++,j=i*2){}
	float p=0,q=0;
做分隔符：分隔函数参数。

##4.3 foreach用于数组和容器中
	float f[] = new float[10];
	for(float x: f) {}
	for(char c: "Hello word!".toCharArray()) {}

##4.4 用标签实现跳转
1) goto是Java中的保留字，但Java中并没有使用。  
2) 标签写在迭代语句(for, while, do-while)之前
	
	label1:
	for(){}
3) 跳转规则： 
 
* break: 跳出本循环，步进不执行。
* continue: 跳出本循环，步进仍然执行。
* break label1: 中断当前循环，也中断label1的循环，label1的步进不执行。
* continue label1: 中断当前循环，当前循环步进不执行。跳转到label1循环处继续执行，label1的步进仍然执行。内层for循环重新执行，注意若有初始化语句，会执行初始化。

##4.5 switch
switch的选择因子是一个能产生整数值的表达式，enum,int或char等。

	switch(c){
		case 'a':
		case 'e':
		case 'i':
		case 'o':
		case 'u': syso("vowel"); break;
		default: syso("consonant");
	}

#第五章 初始化与清理

##5.1 构造函数
构造函数与类名相同，没有return语句（也没有隐式return语句，void函数有隐式return语句）。构造器可以看做静态方法。  
访问权限可以为：private, (default), protected, public。默认构造函数都是public权限。  
如果已定义构造器，编译器不会提供默认构造器。

##5.2 Java中的方法重载
重载方法必须有独一无二的参数类型列表，参数顺序不同也可以，但不推荐这么做。
不能通过返回值区分重载方法。

重载方法匹配时

* 常数值默认做int类型处理。
* 如果传入数据类型小于方法中的形参类型，则传入数据类型会被提升为最接近的类型：  
byte->short->int->long->float->double
* char型略不同，直接提升为int：  
char->int->long->float->double
* 若传入数据类型大于所有形参类型，则必须显式窄化转换。

##5.3 this引用
通过return this;返回一个this引用，可以在一条语句中调用一个方法多次。  
static方法就是没有默认的this引用的方法，并且static方法内部不能调用非静态方法，但可以通过传入对象的引用来调用非静态方法。  
可以在构造函数中调用该类的其他构造函数，但只能调用一个，并且必须在最起始处，使用this(参数)即可。  
构造函数可以调用其他成员函数，但成员函数不能调用构造函数。

##5.4 垃圾回收
垃圾回收器只知道释放由new分配的内存。  
Java允许类中定义finalize()方法，一旦垃圾回收器准备释放内存，先调用其finalize()方法，并在下一次垃圾回收动作发生时，才会真正回收对象占用的内存。  
finalize()可以用于在Java中调用C/C++模块时（本地方法）分配的空间的释放。  
Java中没有析构函数，也没有delete命令。  
System.gc()用于强制进行终结动作。

##5.5 Java初始化顺序
自动初始化（分配空间时清零） -> 指定初始化 -> 实例初始化 -> 构造器  

变量定义的顺序决定了初始化的顺序，即使变量定义散布于方法定义之间，它们仍然会在任何方法（包括构造器）被调用之前得到初始化。  

指定初始化： 在定义成员变量时赋初值，甚至可以通过调用成员方法来提供初值，这个方法可以带有参数，但这些参数必须是已经初始化了的。C/C++中类的成员变量不能在定义时初始化。  

实例初始化： 通过用一个大括号包围的语句块实现，用于初始化非静态变量。

	Mug mug;
	...
	{
    	mug = new Mug(1);
    	System.out.println(mug.toString());
	}
会在调用任何构造器之前执行，对支持匿名内部类的初始化时必须的。

##5.6 静态成员的初始化
static关键字不能应用于局部变量，即方法中不能使用static。  
静态初始化只有在访问静态成员变量/函数的时候，或者初始化对象时才初始化该静态成员。  
显示静态初始化，使用“静态块”（也叫“静态子句”）。在需要的时候才执行，且只执行一次。

	static int i;
	...
	static{
	     i=47;
	     System.out.println(ps);
	}


##5.7 初始化过程：
a)Java查找类路径，定位.class文件并载入。此时执行所有静态初始化。  
b)分配存储空间，并清零。基本数据类型被设置成默认值，引用都被设置成null。  
c)执行所有出现于字段定义处的初始化动作（还有实例初始化语句或显示静态初始化语句）。  
d)执行构造器。

##5.8 数组初始化
定义数组时编译器不允许指定数组大小。

	int[] a1; // 或者
	int a1[]; 

数组是一个对象，有一个固定只读成员length。每次访问数组时会自动检查边界，且该功能无法禁用。  
不能用new创建单个基本类型数据，但可以创建其包装类或基本类型数据的数组。
数组初始化方式：  
a) 不指定初值

	Integer[] a = new Integer[10];
	Integer[] a = new int[10]; // compile error

b) 指定初值

	Integer[] a = {
	     new Integer(1),
	     new Integer(2),
	     3,//最后一个逗号可选
	}
该方案只能在定义处初始化数组。

	Integer[] a = new Integer[]{
    	 new Integer(1),
    	 new Integer(2),
    	 3,//最后一个逗号可选
	}
该方案可在任意处初始化数组。

##5.9 可变参数列表

	void fun(Object... args){}
	void fun(String... args){}
获得形参仍然是一个数组（可为空），可按数组访问规则使用。你应该总是只在重载方法的一个版本上使用可变参数列表，或者根本不用它。

##5.10 枚举类型

	public enum Spiciness{
		NOT, MILD, MEDIUM, HOT, FLAMING //一般全大写
	}
定义有点像类的定义。以上定义了一个名为Spiciness的枚举类型。它自动创建了

	String toString() // 显示名字
	int compareTo()	// 比较两个枚举对象在枚举时的顺序
	int ordinal()	// 返回枚举对象的声明顺序，从0开始
	static T[] values() 	// 按声明顺序返回枚举对象数组
	static T valueOf(Class<T> enumType, String name)	// 返回带指定名称的指定枚举类型的枚举常量。
	
平时使用时，需要有枚举名.枚举实例（例如Spiciness.NOT）。

	Weather today = Weather.Sunny;
	for(Weather w : Weather.values()){
    	System.out.println(w + " " + w.ordinal());
	}
	System.out.println(Weather.valueOf(Weather.class, "Sunny"));
在switch语句中，不需要枚举名，如:

	Spiciness s = Spiciness.MILD;
	switch(s){
    	 case NOT:
    	 case MILD:System.out.println("Acceptable");
    	 ...
	}
关于枚举类型中覆盖默认方法参见[Java枚举类型][java enum]


[java enum]: http://

#第六章 访问权限控制

##6.1 成员访问权限从大到小

* public
* protected ：子类不会自动拥有包访问权限，但同一包中其他类可以访问protected成员。
* 包访问权限（默认）：有时也表示成friendly。
* private

类只有public权限和包访问权限，内部类可以有其他权限。

##6.2 编译单元
一个源代码文件被称为一个编译单元compilation unit（或转译单元translation unit）。  
每个编译单元只能有一个public类，该类名称和文件名称完全相同。其他类为主public类提供支持。  
也可以完全不带public的类，此时可以随意对文件命名。  
编译.java文件后，每个类（包括内部类）都会有一个.class输出文件。Java解释器负责这些文件的查找、装载和解释。

##6.3 package
如果使用package语句声明包名，该语句必须是文件中除注释以外的第一句程序代码。Java包名的命名规则全部使用小写字母。一般使用Internet域名的反序。如果没有设定包名称，则将该文件看作是隶属于该目录的默认包中，该目录是包访问权限的适用范围。

##6.4 类的查找
Java解释器按照环境变量CLASSPATH中的目录作为查找.class文件的根目录，不会默认查找当前目录，需要在CLASSPATH中指明"."作为路径之一。查找JAR文件时，需要CLASSPATH指明目录和文件名的完整名称。

##6.5 导入冲突
若两个含有相同类名的类库以"*"形式导入，如果没有使用冲突的类名，则没有问题。若使用冲突的类名，编译器提示出错，必须返回指定全名的方式。


#第七章 复用类

##7.1 复用类型

* 组合：在新的类中产生现有类的对象，直接让这些现有的对象为新类服务
* 继承：在现有类中添加新的方法，无需改变现有类
* 代理：把一个成员对象于新类中（组合，has-a）,但是在新类中编写方法暴露该成员对象的所有方法.

可以为每个类创建一个main()方法（可以是public或默认权限），只有命令行指定的那个类的main()方法才会调用。

##7.2 继承
单根继承：所有类都直接或间接继承自Object类。  
Java会自动在导出类的构造器中调用基类的默认构造器。若基类没有默认构造器，则在导出类的构造器的第一句使用`super(参数)`调用基类构造器。  
尽量避免使用继承，除非需要向上转型为基类。

名称屏蔽：导出类中的函数可以和基类函数重载。（C++中会隐藏基类中的同名非virtual方法）。  
@Override注解：若方法不是覆盖了基类的方法，会报编译错误。避免意外重载。

##7.3 正确清理
不要依赖finalize()函数。  
自定义清理的方法来清理对象。先清理自己，再清理基类。
对try块中的对象，在finally子句中调用清理函数。

##7.4 final关键字

* final 数据  
恒定不变的数据。  
对static final类型数据通常使用大写下划线表示。  
在定义时不必初始化（空白final），此时需要在所有构造器中赋值，这样使每个对象可以拥有不同的final值。还可以使用函数来赋值。  
在函数参数中使用时，不可改变参数的值或指向。主要用来向匿名内部类传递数据。

	* 基本类型：数据值不变。
	* 引用类型：引用指向恒定不变。  
* final 方法  
方法不能被覆盖。（可重载）  
类中所有的private方法都隐式指定为final的，此时在继承类中定义的相同方法不是覆盖，因为基类中的private不提供对外的接口。
* final 类  
类不能被继承。final类中的所有方法都隐式指定为final的。

#第八章 多态

##8.1 作用
面向对象的基本特征：数据抽象，继承，多态。  
通过分离分离做什么和怎么做，从另一个角度将接口与实现分离。  
将改变的事物（覆盖）与未变的事物分离。

##8.2 后期绑定（动态绑定）
C中没有后期绑定。  
C++中使用virtual实现。  
Java中除了static方法（构造器是隐式static方法）和final方法（private方法隐式属于final方法），都是后期绑定。
注：绑定是对方法来说的，对域的访问都不是多态的。

##8.3 构造器内的多态
导出类初始化时，会先调用基类的构造器。若基类构造器内使用的某个方法在导出类中被覆盖，则基类构造器会使用导出类中的被覆盖方法。  
基本原则：构造器尽量避免调用其他方法。唯一能够安全使用的是基类中的final方法（private方法是final方法）。

##8.4 协变返回类型
导出类中被覆盖的方法可以返回基类方法的返回类型的某种导出类型。

#第九章 接口

##9.1 抽象类和抽象方法
使用`abstract`关键字声明的方法称为抽象方法（不能有方法体）：`abstract void f();`    
包含抽象方法的类叫做抽象类。如果一个类包含一个或者多个抽象方法，则该类被限定为抽象的（否则编译器会报错）。我们也可能会创建没有抽象方法的抽象类。  
抽象类使用`abstract class Name {}`限定，抽象类不能被实例化，一般作为其他类的基类。

##9.2 接口
接口被用来建立类与类之间的协议。  
接口可以包含域，但是这些域隐式地是static和final的。不能是“空final”，但可以被非常量表达式初始化。使用Interface.name引用。  
接口中的方法隐式是public的。  
如果要创建不带任何方法定义和成员变量的基类，首先考虑接口而不是抽象类。如果要创建基类，首先考虑把它实现为接口。

可以继承任意多个接口，实现接口时，若存在基类，可以采用基类中的函数实现。  
组合接口可以很容易在接口中添加新的方法名，但是在继承的多个接口中最好不要使用相同的方法名。

##9.3 嵌套接口
嵌套在另一个接口中的接口自动是public的。实现一个接口时，并不需要实现它内部的接口。  
嵌套在另一个类中的接口可以是private的。若函数返回一个private的借口，唯一的使用方式就是把这个返回值交给有权使用它的对象。

#第十章 内部类

##10.1 特征
内部类是一种名字隐藏，在非static时当生成一个内部类对象时，此对象和他的外围对象之间就有了一种联系，它能够访问外围对象（不管多少层）的所有成员(包括private)而不需要任何特殊条件。 

##10.2 创建内部类
指明内部类类型使用OuterClass.InnerClass的方式。当内部类不是static类（嵌套内部类）时，必须使用某个外围类的对象创建，此时它不能有static数据和static函数，也不能包含static内部类。  
非static内部类如果需要在内部类中生存对外部对象的引用,可以采用DotThis.this这种方式。  
如果需要创建某个内部类的对象可以采用外部类.new 内部类构造方法的方式获得。如`DotNew.Inner dni = dn.new Inner();`也就是说内部类的创建必须依赖于外部类的对象。内部类无论包含多少层，`.new`都能产生正确的对象。  
当一个内部类被定义为private时，在它的外部类之外是无法使用的。 

##10.3 局部内部类
定义在方法内部或任意作用域内的内部类。在作用域或方法外不能使用该内部类。  

##10.4 匿名内部类
可以扩展类或者实现接口，但只能实现一个接口或继承一个类，不能同时兼备。

	new BaseClass(基类构造器参数){
		\\内部类定义
	};
匿名内部类不能有构造器，但可以通过实例初始化。 如果想使用外部定义的对象，编译器要去外部对象的引用必须是final的。

##10.5 嵌套类
声明为static的内部类，又称静态内部类。静态内部类的创建不需要外部类的引用。也不能从静态内部类中访问非静态的外围类对象。  
接口中的域默认是public static (final)的，嵌套类可以放置在接口中（不需要使用static关键字），甚至可以实现（implements）接口。  
嵌套内用于实现接口代码的共用。  
在测试中很有用，嵌套内会生成Outer$Innner.class文件，使用java Outer$Inner执行测试，发布时删除此class文件。

##10.6 内部类的继承
必须使用一个外部类对象初始化

	public class InheritInner extends WithInner.Inner {
		//! InheritInner() {} // can not compile
		InheritInner(WithInner wi) {
			wi.super();
		}
	}

#第十二章 异常

##12.1 基本异常
出现异常情形，从当前环境下跳出，并将问题交给上一级环境。异常抛出后，自堆上使用new创建异常对象。异常处理机制接管程序，需找异常处理程序来继续执行。  
通常异常对象中仅有的信息就是异常类型。  

异常类型
	
	Object
		java.lang.Throwable
			Error
			Exception
				IOException
				...
				RuntimeExceptionn

##12.2 异常的捕获
使用try-catch块
	
	try{
		// Code that might generate exceptions
	} catch(Type1 e1) { // 异常声名不能省略
		// Handle Type1 exceptions
	} catch(Type2 e2) { // 先匹配Type1，在匹配Type2，所以Type1一般为Type2的子类
		// Handle Type2 exceptions
	}

##12.3 抛出异常
异常说明用来说明方法可能产生异常  
`void f() throws TooBig, TooSmall {}`  
在函数中使用`throw`关键字抛出异常，如果在`try`中抛出异常，则`catch`块将不执行。  
如果把当前catch的异常重新抛出，则`printStackTrace()`显示的还是原抛出点的信息，使用`throw (Exception)e.fillInStackTrace()`则会显示抛出点的异常信息。

##12.4 异常方法
所有异常继承自Throwable基类，包含
	
	String getMessage()
	String getLocalizedMessage()
	String toString()
	void printStackTrace()
	void printStackTrace(PrintStream)
	void printStackTrace(java.io.PrintWriter)

	Throwable fillInStackTrace() // 在Throwable对象内部记录栈帧的当前状态
	Throwable getCause() // 返回异常cause
还有从Object类继承的方法
	
	o.getClass().getName()	// 获得完整类名
	o.getClass().getSimpleName()	// 获得类名

##12.5 异常链
`Error,Exception,RuntimeException`提供了带case参数（原始异常）的构造器，对其他的异常类型，使用`initCause()`方法声明异常原因。  
其中，`Throwable initCause(Throwable case)`返回原对象，但类型变为`Throwable`，所以返回值需要强制转换后才能重新抛出，或者返回原对象。

	dfe.initCause(e);
	throw dfe;
	// or you can use 
	//throw (Exception)dfe.initCause(e);

##12.6 Java标准异常
`Error`异常是编译时或系统错误。一般只用关心`Exception`异常。  
所有异常都应该被捕获，但`RuntimeException`除外，因此也被称为“不受检查的异常”，但如果没有捕获`RuntimeException`异常，则会直达`main()`，并在退出程序前调用异常的`printStackTrace()`方法。  
`RuntimeException`包括：  
`NullPointerException, ArithmeticException, ClassCastException, IndexOutOfBoundsException, IllegalArgumentException-NumberFormatException(字符串传给做使用数字参数的函数时)`

##12.7 finally
可以用在`try-finally`结构或者`try-catch-finally`结构中。  
无论`try`块是否抛出异常，`finally` 子句都会执行，主要用来清理。（C++中没有`finally`，使用析构函数完成清理）包含：

* 捕获到异常
* 有未捕获的异常，在跳到上一层环境之前执行
* 正常执行
* 在`try`块中有`break`或`continue`中断循环
* 在`try`块中包含`return`语句

若`finally`中抛出新异常，则原异常失效，外层只处理`finally`中的新异常。  
若`finally`中有返回语句`return`，则所有异常都被忽略，程序正常返回。 

##12.8 异常的限制
* 继承类可以不抛出异常，在继承关系中，异常说明的范围只能变小。
* 若继承基类和接口中抛出的异常类型不同，则以基类为准，不能抛出接口中的异常
* 构造函数的异常说明可以包含新异常，但必须包含基类的异常。因为它会调用基类的构造函数
* 当覆盖方法时，只能抛出在基类方法的异常说明中的异常或那些异常的派生类。

##12.9 构造器中的异常
派生类构造器不能捕获基类构造器抛出的异常，因为此时基类还没构造出来。所以`super()`函数不能包含在`try`块中。  
在构造器中，创建了需要清理的对象后，应该立即进入另一个`try-finally`块中，在`finally`子句中清理对象。

	try {
		Object o = new Object();
		try {
			...
		} catch(Exception e) {
			...
		} finally {
			o.dispose();
		}
	} catch(Exception e) {
		...
	}
