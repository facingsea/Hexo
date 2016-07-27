---
title: Java没有引用传递，只有值传递
date: 2016-06-26 17:20:20
description: Java没有引用传递，只有值传递
categories: 
- java
tags:
- java
---
> 
之前一直以为Java中除了基本类型外都是引用传递，最近才发现并不是这样，Java中只有值传递，没有引用传递！

下面来分类看一下：

## 基本类型
Java中基本类型分为三类：数值类型、字符类型（char）、布尔类型（boolean），数值类型又分为整数型和浮点型。整数型包括byte、short、int、long，浮点型包括float、double，字符类型对应char，布尔类型就是所谓的true和false。
先看以下程序（使用Junit测试）：
```java
@Test
public void testIntPassing(){
    int num = 0;
    processInt(num);
    System.out.println(num);
}

public void processInt(int param){
    param = 2;
}
```
运行结果为`0`，可以看出在`processInt`方法中的赋值其实是不影响原值`num`的，

`num`作为参数传递给`processInt()`方法时，是将内存空间中num所指向的那个**存储单元中存放的值**，即`0`,传送给了`processInt()`方法中的`param`变量，而这个`param`变量**也在内存空间中分配了一个存储单元**，这个时候，就把`num`的值`0`传送给了这个存储单元中。此后，在`processInt()`方法中对`param`的一切操作都是针对`param`所指向的这个存储单元，与`num`所指向的那个存储单元没有关系了！
自然，在函数调用之后，`num`所指向的存储单元的值还是没有发生变化，这就是所谓的“值传递”！值传递的精髓是：**传递的是存储单元中的内容，而非地址或者引用！**

## 对象类型
首先来看程序：
```java
public class Person {
    private String name = "jack";
	
    public static void main(String[] args) {
        Person person = new Person();
        System.out.println("old: " + person.name);
        changeName(person);
        System.out.println("new: " + person.name);
    }
	
    public static void changeName(Person p){
        p.name = "rose";
    }
}
```
输出结果：
```
old: jack
new: rose
```
> 
Java 编程语言只有值传递参数。当一个对象实例作为一个参数被传递到方法中时，参数的值就是**该对象引用的一个副本**。指向同一个对象,对象的内容可以在被调用的方法中改变，但对象的引用(不是引用的副本)是永远不会改变的。 

下面借助图来说明一下：
![](/assert/java-no-transfer-reference-just-value/object-passing-descript.png)

主函数中new了一个对象`Person`，实际分配了两个对象：新创建的`Person`类的实体对象，和指向该对象的引用变量`person`。

> 
注意：在java中，新创建的实体对象在堆内存中开辟空间，而引用变量在栈内存中开辟空间

正如如上图所示，左侧是堆空间，用来分配内存给新创建的实体对象，红色框是新建的`Person`类的实体对象，`000012`是该实体对象的起始地址；而右侧是栈空间，用来给引用变量和一些临时变量分配内存，新实体对象的引用`person`就在其中，可以看到它的存储单元的内容是`000012`，记录的正是新建`Person`类实体对象的起始地址，也就是说它指向该实体对象。
这时候，好戏上台了：
调用了`changeName()`方法，`person`作为对象参数传入该方法，但是大家特别注意，它传入的是什么！！！`person`引用变量将自己的存储单元的内容传给了`changeName()`方法的`p`变量！也就是将实体对象的地址传给了`p`变量，从此，在`changeName()`方法中对`p`的一切操作都是针对`p`所指向的这个存储单元，与`person`引用变量所指向的那个存储单元再没有关系了！
回顾一下上面的一个值传递的例子，值传递，就是将存储单元中的内容传给调用函数中的那个参数，这里是不是异曲同工，是所谓“值传递”，而非“引用传递”！
 
那为什么对象内部能够发生变化呢？
那是因为：`p`所指向的那个存储单元中的内容是实体对象的地址，使得`p`也指向了该实体对象，所以才能改变对象内部的属性！
这也是我们大多数人会误以为是“引用传递”的终极原因！


## 参考
1、[Java中只有按值传递，没有按引用传递](http://guhanjie.iteye.com/blog/1683637)
