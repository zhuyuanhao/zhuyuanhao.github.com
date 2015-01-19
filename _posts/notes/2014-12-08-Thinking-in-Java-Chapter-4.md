---
layout: post
title: Thinking in Java C4 控制流程
category: notes
description: 
---
#####1. 条件表达式
Java控制语句只能用布尔表达式做判定条件。
C/C++可以用数字做布尔值。

#####2. 逗号
做操作符：定义多个**相同类型**的变量，在for语句的初始化语句和步进(step)语句中。或在普通定义语句中。

	for(int i=1,j=i; i<5; i++,j=i*2){}
	float p=0,q=0;
做分隔符：分隔函数参数。

#####3. foreach用于数组和容器中
	float f[] = new float[10];
	for(float x: f) {}
	for(char c: "Hello word!".toCharArray()) {}

#####4. 用标签实现跳转
1) goto是Java中的保留字，但Java中并没有使用。  
2) 标签写在迭代语句(for, while, do-while)之前
	
	label1:
	for(){}
3) 跳转规则： 
 
* break: 跳出本循环，步进不执行。
* continue: 跳出本循环，步进仍然执行。
* break label1: 中断当前循环，也中断label1的循环，label1的步进不执行。
* continue label1: 中断当前循环，当前循环步进不执行。跳转到label1循环处继续执行，label1的步进仍然执行。内层for循环重新执行，注意若有初始化语句，会执行初始化。

#####5. switch
switch的选择因子是一个能产生整数值的表达式，enum,int或char等。

	switch(c){
		case 'a':
		case 'e':
		case 'i':
		case 'o':
		case 'u': syso("vowel"); break;
		default: syso("consonant");
	}
