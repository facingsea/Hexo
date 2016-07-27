---
title: Netty源码导入eclipse
date: 2016-06-25 14:46:20
description: Netty源码导入eclipse
categories: 
- netty
tags:
- netty
---

本文源码版本：[4.1.1.Final](https://github.com/netty/netty/tree/netty-4.1.1.Final)
官方环境要求：
> 
1. JDK：Oracle JDK 7 及以上版本
2. Eclipse：64位
3. Maven：3.1.1及以上

本文JDK为Oracle JDK 8 64位，Eclipse为Mars1 64位，Maven为3.3.9，系统为windows 10。

## 获取源码
首先从github上获取对应源码，我这里使用的是[4.1.1.Final](https://github.com/netty/netty/tree/netty-4.1.1.Final) ，下载后解压到合适的目录。

## 导入前准备
首先确保系统环境中maven配置正常。
进入`netty-netty-4.1.1.Final\common`，使用cmd执行以下命令：
```bash
mvn clean compile
```
该命令用来编译`common`包，先执行该命令的原因是该包下的`collection`包是通过groovy程序生成，该程序位于`netty-netty-4.1.1.Final\common\src\main\script`下。
命令执行时，可能会出现以下错误：
1. 格式错误，错误信息如：
```
[INFO]
[INFO] --- maven-checkstyle-plugin:2.10:check (check-style) @ netty-common ---
[INFO] Starting audit...
E:\source\netty\netty-netty-4.1.1.Final-bak\netty-netty-4.1.1.Final\common\src\main\java\io\netty\util\AbstractConstant.java:0: File does not end with a newline.
E:\source\netty\netty-netty-4.1.1.Final-bak\netty-netty-4.1.1.Final\common\src\main\java\io\netty\util\AbstractConstant.java:0: invalid newline character (expected: CRLF)
E:\source\netty\netty-netty-4.1.1.Final-bak\netty-netty-4.1.1.Final\common\src\main\java\io\netty\util\AbstractReferenceCounted.java:0: File does not end with a newline.
...
```
出现该信息的原因是源码中某些java文件不符合`check-style`规范，解决方案是从`netty-netty-4.1.1.Final\all\pom.xml`中找到如下配置：
```xml
      <!-- Disable checkstyle -->
      <plugin>
        <artifactId>maven-checkstyle-plugin</artifactId>
        <executions>
          <execution>
            <id>check-style</id>
            <phase>none</phase>
          </execution>
        </executions>
      </plugin>
```
把该配置复制到`netty-netty-4.1.1.Final\common\pom.xml`文件的`<plugins>...</plugins>`里，并且确保`<plugin>`内容前的缩进**都是空格，不能出现Tab键**，否则可能会出现以下错误：
```
[INFO] --- xml-maven-plugin:1.0.1:check-format (check-style) @ netty-common ---
[ERROR] E:\source\netty\netty-netty-4.1.1.Final-bak\netty-netty-4.1.1.Final\common\pom.xml:164,12: Insert 3 spaces. Expected 6 found 3 spaces before start element <plugin>
```
然后再次执行上面的编译命令即可编译成功。
然后在`netty-netty-4.1.1.Final\common\target\generated-sources\collections\java\io\netty\util\collection`目录下可以找到新生成的java文件，将该`collection`目录拷贝到`netty-netty-4.1.1.Final\common\src\main\java\io\netty\util`目录下，此时该目录下结构大致为：
```
- ...io\netty\util
-		collection\
-       concurrent\
-       internal\
-       *.java
```

## 导入

根据官方文档介绍：[Setting up development environment](http://netty.io/wiki/setting-up-development-environment.html) ：
> 
1. Ensure to use the 64-bit version of Eclipse.
2. [Download os-maven-plugin](http://repo1.maven.org/maven2/kr/motd/maven/os-maven-plugin/1.4.1.Final/os-maven-plugin-1.4.1.Final.jar) and put it into <ECLIPSE_INSTALLATION_DIR>/plugins directory to work around the problem where m2e does not evaluate an extension specified in our pom.xml. (Unlike its name, it's both a Maven plugin and an Eclipse plugin.)
3. Import the project via the 'File → Import... → Existing Maven Projects' menu.
4. Netty project Maven pom.xml settings dictate use of Java SE 1.6, while implicitly using Java 7/8 (1.7/1.8) features if present. This may result in compilation errors in Eclipse. There are two ways to work around this problem:
	1. Look in the 'Window → Preferences → Installed JRE' menu:
		* Make sure you have Java 7/8 installation available under 'Installed JRE'
		* Map this Java 7/8 installation onto Java 6: 'Installed JRE → Execution Environments → Java SE 1.6'
	2. Alternatively, Java 7/8 JRE can be selected on per-project basis for each Netty module.

大致翻译为：
1. 确保使用的是64位的Eclipse。
2. 	[下载 os-maven-plugin](http://repo1.maven.org/maven2/kr/motd/maven/os-maven-plugin/1.4.1.Final/os-maven-plugin-1.4.1.Final.jar) 并把它放到<ECLIPSE_INSTALLATION_DIR>/plugins 目录。
3. 通过Eclipse菜单导入：'File → Import... → Existing Maven Projects'.
4. Netty项目中的pom.xml文件使用的是Java SE 1.6，但是使用了Java 7/8 (1.7/1.8)的特性，所以这可能会导致Eclipse报错，有两个方法解决这个问题：
	1. 菜单中查找：'Window → Preferences → Installed JRE'：
		* 在'Installed JRE'下，确保已经有安装好的Java 7/8 。
		* 在'Installed JRE → Execution Environments → Java SE 1.6'下，将Java 6 换成已安装好的Java 7/8 。
	2. 另外一种就是手动点击每个模块来更新Java 7/8 JRE
	
按照以上步骤，即可完成导入，期间会出现一个Maven的错误信息，在`Programs`视图下右键点击`Quick Fix`即可。


## 参考
1. [developer-guide](http://netty.io/wiki/developer-guide.html)
