---
layout: post
title: Thinking in Java C2 一切都是对象
category: notes
description: 
---


#####0. C++使用virtual实现方法的动态绑定（后期绑定），Java中的方法默认是动态绑定。

#####1. 操纵的标识符是对象的一个“引用”。引用不一定需要有一个对象与它关联。

#####2. Java内存结构（非书中内容）

1. PC Register(PC寄存器)——不能直接控制
2. JVM栈——基本类型和对象引用
3. 堆——所有Java对象
4. 方法区域（MethodArea）——存放了所加载的类的信息（名称、修饰符等）
5. 运行时常量池——常量值，字符串常量置于特殊的静态存储区
6. 本地方法堆栈——用于支持native方法的执行

具体参见[lll](http)

#####3. 基本类型

自动变量，直接存储值而不是引用。

* boolean —— 无规定，只能为true或false。
* char —— **16 bit** 提供对Unicode的支持。参见[Java中的字符]()
* byte —— 8 bit
* short —— 16 bit
* int —— 32 bit
* long —— 64 bit
* float —— 32 bit
* double —— 64 bit
* void —— 无规定

char,byte,short 在参与数学和按位运算时会自动抓换为int类型。  
所有数值类型都有正负号，每种基本类型都有对应的包装器类型(基本类型存储于堆栈中，包装器类型存储于堆中)，两者之间可以互相转换。

	Character ch = 'x';
	char c = ch;

有两个用于高精度计算的类：

* BigInteger 任意精度整数
* BigDecimal 任意精度定点数

#####4. Java中的数组

若数组元素是基本数据类型：全部初始化为0;  
若数组元素是对象：存储的是对象的引用，全被初始化为null。  
*数组是对象，所以初始化时内存会被初始化为0。*

#####5. 作用域

C/C++中，小作用域中的变量可以“隐藏”外层大作用域中的变量，但Java中不允许。

	{
		int x = 12;
		{
			int x = 1; // Illegal
		}
	}
基本类型和对象引用都有有限的生命周期，严格满足大括号规定的作用域。但引用消失后，它指向的对象仍会占据内存。

#####6. 初始化

Java中类成员有默认初始化值。  
C/C++中类成员没有默认初始化值。  
局部变量，Java和C/C++都没有默认初始化值。但使用前，若没有初始化，Java编译会返回一个错误，C/C++一般会给予警告。

#####7. 参数传递

Java中函数的参数传递总是传"值"。基本类型拷贝数据，引用类型拷贝对象的引用。使用swap交换两个引用时，若使用
	
	swap(Object i, Object j) {Object t = i; i = j; j = t;}
交换的只是两个形参的引用。此处必须使用实参的引用

	swap(Object[] a, int i, int j) {Object t = a[i]; a[i] = a[j]; a[j] = t;}

#####8. 消除向前引用
  
Java 只要是一个类中的成员变量或成员函数都可以被引用。  
C/C++ 	只有在当前成员**之前**定义的成员才能被当前成员引用。

#####9. static成员或方法
可以用一个对象使用，s.i。或者使用类名直接引用，String.i。  

#####10. import
必须在文件开头，类定义之外。  
Java中import并不导入类，只是声明了类的查找路径，编译时按需导入使用到的类。  
C/C++中的include直接导入文件的所有内容。

#####11. 类的名字必须和文件名相同。
main函数必须是：`public static void main(String[] args){}`  
其中`args[0]`是程序的第一个参数，而在C/C++中是程序名。

#####12. 命令行编译
javac命令： 将.java源文件编译为.class文件  
java命令: 执行.class文件（参数不需要后缀）  
命令的使用参见[JAVA和JAVAC命令][javacmd]

[javacmd]: http://zhuyuanhao.com/Java-and-javac-command-line/ "Java and Javac command"


