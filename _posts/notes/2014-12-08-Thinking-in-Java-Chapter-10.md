---
layout: post
title: Thinking in Java C10 内部类
category: notes
description: 
---

#####1. 特征
内部类是一种名字隐藏，在非static时当生成一个内部类对象时，此对象和他的外围对象之间就有了一种联系，它能够访问外围对象（不管多少层）的所有成员(包括private)而不需要任何特殊条件。 

#####2. 创建内部类
指明内部类类型使用OuterClass.InnerClass的方式。当内部类不是static类（嵌套内部类）时，必须使用某个外围类的对象创建，此时它不能有static数据和static函数，也不能包含static内部类。  
非static内部类如果需要在内部类中生存对外部对象的引用,可以采用DotThis.this这种方式。  
如果需要创建某个内部类的对象可以采用外部类.new 内部类构造方法的方式获得。如`DotNew.Inner dni = dn.new Inner();`也就是说内部类的创建必须依赖于外部类的对象。内部类无论包含多少层，`.new`都能产生正确的对象。  
当一个内部类被定义为private时，在它的外部类之外是无法使用的。 

#####3. 局部内部类
定义在方法内部或任意作用域内的内部类。在作用域或方法外不能使用该内部类。  

#####4. 匿名内部类
可以扩展类或者实现接口，但只能实现一个接口或继承一个类，不能同时兼备。

	new BaseClass(基类构造器参数){
		\\内部类定义
	};
匿名内部类不能有构造器，但可以通过实例初始化。 如果想使用外部定义的对象，编译器要去外部对象的引用必须是final的。

#####5. 嵌套类
声明为static的内部类，又称静态内部类。静态内部类的创建不需要外部类的引用。也不能从静态内部类中访问非静态的外围类对象。  
接口中的域默认是public static (final)的，嵌套类可以放置在接口中（不需要使用static关键字），甚至可以实现（implements）接口。  
嵌套内用于实现接口代码的共用。  
在测试中很有用，嵌套内会生成Outer$Innner.class文件，使用java Outer$Inner执行测试，发布时删除此class文件。

#####6. 内部类的继承
必须使用一个外部类对象初始化

	public class InheritInner extends WithInner.Inner {
		//! InheritInner() {} // can not compile
		InheritInner(WithInner wi) {
			wi.super();
		}
	}