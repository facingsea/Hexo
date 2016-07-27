---
title: JDK源码调试并显示变量信息
date: 2016-06-28 23:20:20
description: JDK源码调试并显示变量信息
categories: 
- java
tags:
- java
- jdk
---

在调试程序时，如果进入JDK源码后，会发现源码中的变量是无法显示具体值的，这是因为JDK在打包时默认去除了debug的信息，来减少jar包的大小。

下面可以自己手动编译（使用系统：windows10，JDK：1.8.0_60）：

## 创建目录
新创建一个目录，如`E:\tmp`，在tmp目录中，创建存放源码的`jdk8_src`和输出信息的`jdk8_debug`两个目录。

## 拷贝JDK源码
从`JDK_HOME`中拿到`src.zip`，并将其解压到`jdk8_src`，此时目录结构为：
```
- E:\tmp
-      jdk8_src\
-          com\...
-          java\...
-          javax\...
-          launcher\...
-          org\...
-      jdk8_debug\
```
可以从中挑选一部分来编译，比如`java/`和`javax/`，这里暂时先编译全部。

## 拷贝依赖jar
从`JDK_HOME\jre\lib`下找到`rt.jar`，将其拷贝到`tmp/`目录下。

## 生成需编译文件列表
在`tmp`目录下，打开cmd（按着`shift`键，然后右键，选择`在此处打开命令窗口`），执行以下命令：
```bash
dir /B /S /X jdk8_src\*.java > filelist.txt
```
该命令主要是将所要编译的java文件列表写入到当前目录的filelist.txt文件中。

## 编译
执行以下`javac`命令：
```bash
javac -J-Xms16m -J-Xmx1024m -sourcepath E:\tmp\jdk8_src -cp E:\tmp\rt.jar -d E:\tmp\jdk8_debug -g @filelist.txt >> log.txt 2>&1
```
该命令将会把编译后的所有文件放到`jdk8_debug`目录中，并在`tmp`目录下生成`log.txt`来存放编译的结果信息，打开该文件，忽略前面的警告信息，会发现文件的最后出现许多类似以下错误信息：
```
E:\tmp\jdk8_src\com\sun\java\swing\plaf\gtk\GTKLookAndFeel.java:45: 错误: 找不到符号
import sun.awt.UNIXToolkit;
              ^
  符号:   类 UNIXToolkit
  位置: 程序包 sun.awt
E:\tmp\jdk8_src\com\sun\java\swing\plaf\gtk\GTKStyle.java:37: 错误: 找不到符号
import sun.awt.UNIXToolkit;
              ^
  符号:   类 UNIXToolkit
  位置: 程序包 sun.awt
```
查看所有错误，会发现都是`com`包下的，最简单的方式就是删除`jdk8_src`目录下的`com`目录，重新执行上面的`生成需编译文件列表`和`编译`两个步骤即可编译成功。
如果不想删除整个`com`目录，可以考虑删除对应报错的文件，下面列出出错的文件目录：
```
E:\tmp\jdk8_src\com\sun\java\swing\plaf\gtk\GTKLookAndFeel.java
E:\tmp\jdk8_src\com\sun\java\swing\plaf\gtk\GTKStyle.java
E:\tmp\jdk8_src\com\sun\java\swing\plaf\gtk\GTKPainter.java
E:\tmp\jdk8_src\com\sun\java\swing\plaf\gtk\PangoFonts.java
E:\tmp\jdk8_src\com\sun\source\util\JavacTask.java
E:\tmp\jdk8_src\com\sun\java\swing\plaf\gtk\GTKEngine.java
```
从目录结构中可以看出，报错的基本都是`swing`包下`gtk`相关的，熟悉`linux`应该都知道`gtk`是`GNU/Linux`下开发图形界面的应用程序的主流开发工具之一，在`windows`下不需要这些，可以**手动删除整个`gtk`包**。
还有一个是`JavacTask.java`，根据该文件中的类注释：
```
Provides access to functionality specific to the JDK Java Compiler, javac.
```
可理解为提供了访问JDK中Java编译器的指定功能，根据错误信息：
```
E:\tmp\jdk8_src\com\sun\source\util\JavacTask.java:40: 错误: 程序包com.sun.tools.javac.api不存在
import com.sun.tools.javac.api.BasicJavacTask;
```
可以看出应该是`com.sun`包中某些类已经被移除，才导致的报错，由于该类被引用的很多，只删除该文件肯定还是会报错的，这里可以这样做：
> 
1. 从`JDK_HOME\jre\lib`下找到`tools.jar`，将其拷贝到`tmp/`目录下，这时该目录下有两个jar：`rt.jar`和`tools.jar`。
2. 删除之前生成的`filelist.txt`和`log.txt`两个文件（因为之前删除了`gtk`包，列表需要重新生成）
3. 重新生成`filelist.txt`，参见步骤5。
4. 重新执行`javac`命令，并加入新引入的`tools.jar`包：
```bash
javac -J-Xms16m -J-Xmx1024m -sourcepath E:\tmp\jdk8_src -cp E:\tmp\rt.jar;E:\tmp\tools.jar -d E:\tmp\jdk8_debug -g @filelist.txt >> log.txt 2>&1
```
	
执行完后，查看`log.txt`，可以看出没了错误信息，只剩下警告信息。进入`jdk8_debug`目录，可以看到该目录下已经有了编译好的class文件。

## 打包
使用cmd进入`jdk8_debug`目录，执行打包命令：
```bash
jar cf0 rt_debug.jar *
```
该命令生成的rt_debug.jar包就是带有debug信息的rt包。

## 加入到JDK下
拷贝`rt_debug.jar`文件到`JDK_HOME\jre\lib\endorsed`，如果`endorsed`目录不存在就新建一个。

现在已经完成所有的编译工作，可以使用eclipse来调试了。

## 参考
1. [debug jdk source can't watch variable what it is](  http://stackoverflow.com/questions/18255474/debug-jdk-source-cant-watch-variable-what-it-is/18255852#18255852)


