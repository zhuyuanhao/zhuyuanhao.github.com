---
layout: post
title: Thinking in Java C12 异常
category: notes
description: 
---

#####1. 基本异常
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

#####2. 异常的捕获
使用try-catch块
	
	try{
		// Code that might generate exceptions
	} catch(Type1 e1) { // 异常声名不能省略
		// Handle Type1 exceptions
	} catch(Type2 e2) { // 先匹配Type1，在匹配Type2，所以Type1一般为Type2的子类
		// Handle Type2 exceptions
	}

#####3. 抛出异常
异常说明用来说明方法可能产生异常  
`void f() throws TooBig, TooSmall {}`  
在函数中使用`throw`关键字抛出异常，如果在`try`中抛出异常，则`catch`块将不执行。  
如果把当前catch的异常重新抛出，则`printStackTrace()`显示的还是原抛出点的信息，使用`throw (Exception)e.fillInStackTrace()`则会显示抛出点的异常信息。

#####4. 异常方法
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

#####5. 异常链
`Error,Exception,RuntimeException`提供了带case参数（原始异常）的构造器，对其他的异常类型，使用`initCause()`方法声明异常原因。  
其中，`Throwable initCause(Throwable case)`返回原对象，但类型变为`Throwable`，所以返回值需要强制转换后才能重新抛出，或者返回原对象。

	dfe.initCause(e);
	throw dfe;
	// or you can use 
	//throw (Exception)dfe.initCause(e);

#####6. Java标准异常
`Error`异常是编译时或系统错误。一般只用关心`Exception`异常。  
所有异常都应该被捕获，但`RuntimeException`除外，因此也被称为“不受检查的异常”，但如果没有捕获`RuntimeException`异常，则会直达`main()`，并在退出程序前调用异常的`printStackTrace()`方法。  
`RuntimeException`包括：  
`NullPointerException, ArithmeticException, ClassCastException, IndexOutOfBoundsException, IllegalArgumentException-NumberFormatException(字符串传给做使用数字参数的函数时)`

#####7. finally
可以用在`try-finally`结构或者`try-catch-finally`结构中。  
无论`try`块是否抛出异常，`finally` 子句都会执行，主要用来清理。（C++中没有`finally`，使用析构函数完成清理）包含：

* 捕获到异常
* 有未捕获的异常，在跳到上一层环境之前执行
* 正常执行
* 在`try`块中有`break`或`continue`中断循环
* 在`try`块中包含`return`语句

若`finally`中抛出新异常，则原异常失效，外层只处理`finally`中的新异常。  
若`finally`中有返回语句`return`，则所有异常都被忽略，程序正常返回。 

#####8. 异常的限制
* 继承类可以不抛出异常，在继承关系中，异常说明的范围只能变小。
* 若继承基类和接口中抛出的异常类型不同，则以基类为准，不能抛出接口中的异常
* 构造函数的异常说明可以包含新异常，但必须包含基类的异常。因为它会调用基类的构造函数
* 当覆盖方法时，只能抛出在基类方法的异常说明中的异常或那些异常的派生类。

#####9. 构造器中的异常
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
