---
layout: post
title: Thinking in Java C3 操作符
category: notes
description: 
---
#####1. 赋值
基本数据类型：复制值  
对象：复制引用，引起“别名现象”（aliasing）。在使用对象做函数参数时，也是复制引用。

#####2. 整数除法
截取整数部分，和C/C++相同。

#####3. Random类
`Random(long seed)`如果创建过程中没有参数，以当前时间作为种子。相同种子能产生相同随机数序列。  
`nextBoolean(),nextInt(),nextInt(int i),nextLong(),nextFloat(),nextDouble(),nextGaussian(),nextBytes(byte[] bytes)`获取随机数,浮点数得到[0,1)的值，整型得到区间内的值{-MaxValue-1,MaxValue}，nextInt可设置区间为[0,i)。   
`void setSeed(long seed)`重置种子。   
_注：`Math.random()`方法随机产生一个[0,1)的double型值。_

#####4. ==和!=
对于对象，比较两者的引用是否相等。
equals()方法是所有对象都使用的特殊方法，用于比较两个对象的实际内容是否相同。但equals()方法的默认行为是比较引用，所以在自定义的类中需要覆盖原equals()方法。

#####5. 布尔类型
可用于

* 逻辑操作符（短路）: && || !
* 部分按位操作符(做单比特，结果还是boolean，不会短路): & | ^ 
* 不可用于按位非~，移位  

与C/C++不同，Java不可以将一个非布尔值当作布尔值在逻辑表达式中使用。布尔类型只能转为String。

#####6. 直接常量

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

#####7. 移位操作
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

#####8. +和+=作用于字符串的连接
'+'从左往右依次运算，若一个String类型后面跟着'+'，会把后面的数据都作为字符串拼接。若前面有一个'+'，会把之前的数据先运算，再作为String和当前String拼接，其中非String类型通过调用自己的toString()函数转换，null引用没有toString()方法，但会输出字符串"null"。

	1+2+"s" // "3s"
	"s"+1+2 // "s12"
	"s"+(1+2) // "s3"
	""+1: "1" // 简单的转换为字符串的方法

#####9.
Java的逻辑运算&&和||也会短路。  
Java没有运算符重载函数。  
Java没有sizeof()操作符。

#####10. 窄化转换(narrowing conversion)
窄化转换时，编译器会强制要求使用显示类型转换(cast)。  
float或double转换为整型值时，总是截尾。若想用舍入，需用java.lang.Math.round()方法。参见[Math的用法][math usage]  
char,byte,short在执行算数操作和按位运算之前，都会被转换为int类型。

	char i = 5;
	i = i + 5; // compile error
	i = (char) (i + 5); // ok
	i += 5; // ok
复合赋值时，虽然中间结果是int，但不需要显示类型转换(cast)。  

#####11. 优先级
![Java操作符优先级][java operator]

[math usage]: http://
[java operator]: http://zhuyuanhao.com/images/java/java_operator.png

