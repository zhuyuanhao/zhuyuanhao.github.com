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

##3.9
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
