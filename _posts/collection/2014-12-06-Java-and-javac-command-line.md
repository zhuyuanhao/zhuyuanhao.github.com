---
layout: post
title: JAVA和JAVAC命令
category: collection
description: 
---

#####javac
* -classpath 设定要搜索类的路径，可以是目录，jar文件，zip文件（里面都是class文件），会**覆盖掉所有在CLASSPATH**里面的设定。
* -sourcepath 设定要搜索编译所需java 文件的路径，可以是目录，jar文件，zip文件（里面都是java文件）。  
* -d 用于指定编译成的class文件的存放位置，缺省情况下不指定class文件的存放目录，编译的class文件将和源文件在同一目录下。
* @files：一个或多个对源文件进行列表的文件，有时候要编译的文件很多，一个个敲命令会显得很长，也不方便修改，可以把要编译的源文件列在文件中，在文件名前加@，这样就可以对多个文件进行编译，对编译一个工程很有用，方便，省事。

所以一个完整的javac命令行应该是这样的，
假设abc.java在路径c:\src里面，在任何的目录的都可以执行以下命令来编译。  
`javac -classpath c:\classes;c:\jar\abc.jar;c:\zip\abc.zip -sourcepath c:\source;c:\src.jar;c:\src.zip c:\src\abc.java`

表示编译需要`c:\classed`下面的class文件，`c:\jar\abc.jar`里面的class文件，`c:\zip\abc.zip`里面的class文件
还需要`c:\source`下面的源文件，`c:\src.jar`里面的源文件，`c:\src.zip`里面的源文件。  
注意：jar，zip里面的源文件不会有什么改动，目录下的源文件，有可能会被重新编译。
#####java
* -classpath， 设定要搜索的类的路径，可以是目录，jar文件，zip文件（里面都是class文件），会覆盖掉所有的CLASSPATH的设定。

由于所要执行的类也是要搜索的类的一部分，所以一定要把这个类的路径也放到-classpath的设置里面。
如果要执行当前目录下的类，一定要添加上点号（.）标示本目录也要搜索。

假设abc.class在路径c:\src里面
可以在任何路径下执行以下命令  
`java -classpath c:\classes;c:\jar\abc.jar;c:\zip\abc.zip;c:\src abc`

问题：如果main.class属于c:\jar\abc.jar，并且在com.cnblogs.jeffchen这个包里，那么执行  
`java -classpath c:\classes;c:\jar\abc.jar;c:\zip\abc.zip com.cnblogs.jeffchen.main`
即可， 但是如果classpath中包含多个jar包呢？并且其他jar包中也有com.cnblogs.jeffchen会发生什么情况？错误？

#####路径分割符
* 在windows下，  
文件路径的分割符为反斜杠  \   
类或者java文件列表的分割符为分号 ;

* 在linux下  
文件路径的分隔符位斜杠 /  
类或者java文件列表的分隔符为冒号 :

一个linux下编译和运行的例子  
`javac -classpath /tmp/javatest/lib/mail-1.3.3.jar -d /tmp/javatest/bin/ /tmp/javatest/src/jp/co/realseed/Capability.java`  
`java -classpath /tmp/javatest/lib/mail-1.3.3.jar:/tmp/javatest/bin/ jp.co.realseed.Capability`
