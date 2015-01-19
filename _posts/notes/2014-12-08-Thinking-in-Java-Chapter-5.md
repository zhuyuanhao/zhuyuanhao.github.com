---
layout: post
title: Thinking in Java C5 初始化与清理
category: notes
description: 
---

#####1. 构造函数
构造函数与类名相同，没有return语句（也没有隐式return语句，void函数有隐式return语句）。构造器可以看做静态方法。  
访问权限可以为：private, (default), protected, public。默认构造函数都是public权限。  
如果已定义构造器，编译器不会提供默认构造器。

#####2. Java中的方法重载
重载方法必须有独一无二的参数类型列表，参数顺序不同也可以，但不推荐这么做。
不能通过返回值区分重载方法。

重载方法匹配时

* 常数值默认做int类型处理。
* 如果传入数据类型小于方法中的形参类型，则传入数据类型会被提升为最接近的类型：  
byte->short->int->long->float->double
* char型略不同，直接提升为int：  
char->int->long->float->double
* 若传入数据类型大于所有形参类型，则必须显式窄化转换。

#####3. this引用
通过return this;返回一个this引用，可以在一条语句中调用一个方法多次。  
static方法就是没有默认的this引用的方法，并且static方法内部不能调用非静态方法，但可以通过传入对象的引用来调用非静态方法。  
可以在构造函数中调用该类的其他构造函数，但只能调用一个，并且必须在最起始处，使用this(参数)即可。  
构造函数可以调用其他成员函数，但成员函数不能调用构造函数。

#####4. 垃圾回收
垃圾回收器只知道释放由new分配的内存。  
Java允许类中定义finalize()方法，一旦垃圾回收器准备释放内存，先调用其finalize()方法，并在下一次垃圾回收动作发生时，才会真正回收对象占用的内存。  
finalize()可以用于在Java中调用C/C++模块时（本地方法）分配的空间的释放。  
Java中没有析构函数，也没有delete命令。  
System.gc()用于强制进行终结动作。

#####5. Java初始化顺序
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

#####6. 静态成员的初始化
static关键字不能应用于局部变量，即方法中不能使用static。  
静态初始化只有在访问静态成员变量/函数的时候，或者初始化对象时才初始化该静态成员。  
显示静态初始化，使用“静态块”（也叫“静态子句”）。在需要的时候才执行，且只执行一次。

	static int i;
	...
	static{
	     i=47;
	     System.out.println(ps);
	}


#####7. 初始化过程：
a)Java查找类路径，定位.class文件并载入。此时执行所有静态初始化。  
b)分配存储空间，并清零。基本数据类型被设置成默认值，引用都被设置成null。  
c)执行所有出现于字段定义处的初始化动作（还有实例初始化语句或显示静态初始化语句）。  
d)执行构造器。

#####8. 数组初始化
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

#####9. 可变参数列表

	void fun(Object... args){}
	void fun(String... args){}
获得形参仍然是一个数组（可为空），可按数组访问规则使用。你应该总是只在重载方法的一个版本上使用可变参数列表，或者根本不用它。

#####10. 枚举类型

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