---
title: Oracle官方并发教程
date: 2015-07-23 09:21:36
description: Oracle官方并发教程
categories: 
- java
tags:
- concurrency
---
# Oracle官方并发教程

计算机的使用者一直以为他们的计算机可以同时做很多事情。他们认为当其他的应用程序在下载文件，管理打印队列或者缓冲音频的时候他们可以继续在文 字处理程序上工作。甚至对于单个应用程序，他们任然期待它能在在同一时间做很多事情。举个例子，一个流媒体播放程序必须能同时完成以下工作：从网络上读取 数字音频，解压缩数字音频，管理播放和更新程序显示。甚至文字处理器也应该能在忙于重新格式化文本和刷新显示的情况下同时响应键盘和鼠标事件。这样的软件 就被称为并发软件。

通过Java语言和Java类库对于基础并发的支持，Java平台具有完全（from the ground up ）支持并发编程的能力。从JDK5.0起，Java平台还引入了高级并发APIs。这个课程不仅涵盖了Java平台基础并发内容，还对高级并发APIs有 一定的阐述。

## 进程和线程 

在并发编程中，有两个基本的执行单元：进程和线程。在java语言中，并发编程最关心的是线程，然而，进程也是非常重要的。

即使在只有单一的执行核心的计算机系统中，也有许多活动的进程和线程。因此，在任何给定的时刻，只有一个线程在实际执行。处理器的处理时间是通过操作系统的时间片在进程和线程中共享的。

现在具有多处理器或有多个执行内核的多处理器的计算机系统越来越普遍，这大大增强了系统并发执行的进程和线程的吞吐量–但在不没有多个处理器或执行内核的简单的系统中，并发任然是可能的。

### 进程

进程具有一个独立的执行环境。通常情况下，进程拥有一个完整的、私有的基本运行资源集合。特别地，每个进程都有自己的内存空间。

进程往往被看作是程序或应用的代名词，然而，用户看到的一个单独的应用程序实际上可能是一组相互协作的进程集合。为了便于进程之间的通信，大多数操作系统都支持进程间通信（IPC），如pipes 和sockets。IPC不仅支持同一系统上的通信，也支持不同的系统。

Java虚拟机的大多数实现是单进程的。Java应用可以使用的ProcessBuilder对象创建额外的进程，多进程应用超出了本课的范围。

### 线程

线程有时也被称为轻量级的进程。进程和线程都提供了一个执行环境，但创建一个新的线程比创建一个新的进程需要的资源要少。

线程是在进程中存在的 — 每个进程最少有一个线程。线程共享进程的资源，包括内存和打开的文件。这样提高了效率，但潜在的问题就是线程间的通信。

多线程的执行是Java平台的一个基本特征。每个应用都至少有一个线程 – 或几个，如果算上“系统”线程的话，比如内存管理和信号处理等。但是从程序员的角度来看，启动的只有一个线程，叫主线程。这个线程有能力创建额外的线程，我们将在下一节演示。
线程对象 

在Java中，每个线程都是Thread类的实例。并发应用中一般有两种不同的线程创建策略。

	* 直接控制线程的创建和管理，每当应用程序需要执行一个异步任务的时候就为其创建一个线程
	* 将线程的管理从应用程序中抽象出来作为执行器，应用程序将任务传递给执行器，有执行器负责执行。

这一节，我们将讨论Thread对象，有关Executors将在高级并发对象一节中讨论。

#### 定义并启动一个线程

应用程序在创建一个线程实例时，必须提供需要在线程中运行的代码。有两种方式去做到这一点：

	* 提供一个Runnable对象。Runnable对象仅包含一个run()方法，在这个方法中定义的代码将在会线程中执行。将Runnable对象传递给Thread类的构造函数即可，如下面这个HelloRunnable的例子：

```java
	 public class HelloRunnable implements Runnable {  
	   
	     public void run() {  
	         System.out.println("Hello from a thread!");  
	     }  
	   
	     public static void main(String args[]) {  
	         (new Thread(new HelloRunnable())).start();  
	     }  
	   
	 }  
```

	* 继承Thread类。Thread类自身已实现了Runnable接口，但它的run()方法中并没有定义任何代码。应用程序可以继承与Thread类，并复写run()方法。如例子HelloThread

```java
	 public class HelloThread extends Thread {  
	   
	     public void run() {  
	         System.out.println("Hello from a thread!");  
	     }  
	   
	     public static void main(String args[]) {  
	         (new HelloThread()).start();  
	     }  
	   
	 }  
```

需要注意的是，上述两个例子都需要调用Thread.start()方法来启动一个新的线程。 哪一种方式是我们应该使用的？相对来说，第一种更加通用，因为Runnable对象可以继承于其他类（Java只支持单继承，当一个类继承与Thread 类后，就无法继承与其他类）。第二种方法更易于在简单的应用程序中使用，但它的局限就是：你的任务类必须是Thread的子类。这个课程更加聚焦于第一种 将Runnable任务和Thread类分离的方式。不仅仅是因为这种方式更加灵活，更因为它更适合后面将要介绍的高级线程管理API。 Thread类定义了一些对线程管理十分有用的的方法。在这些方法中，有一些静态方法可以给当前线程调用，它们可以提供一些有关线程的信息，或者影响线程 的状态。而其他一些方法可以由其他线程进行调用，用于管理线程和Thread对象。我们将在下面的章节中，深入探讨这些内容。

#### 使用Sleep方法暂停一个线程

使用Thread.sleep()方法可以暂停当前线程一段时间。这是一种使处理器时间可以被其他线程或者运用程序使用的有效方式。sleep()方法还可以用于调整线程执行节奏（见下面的例子）和等待其他有执行时间需求的线程（这个例子将在下一节演示）。
在Thread中有两个不同的sleep()方法，一个使用毫秒表示休眠的时间，而另一个是用纳秒。由于操作系统的限制休眠时间并不能保证十分精 确。休眠周期可以被interrups所终止，我们将在后面看到这样的例子。不管在任何情况下，我们都不应该假定调用了sleep()方法就可以将一个线 程暂停一个十分精确的时间周期。

SleepMessages程序为我们展示了使用sleep()方法每四秒打印一个信息的例子

```java
	 public class SleepMessages {  
	     public static void main(String args[])  
	         throws InterruptedException {  
	         String importantInfo[] = {  
	             "Mares eat oats",  
	             "Does eat oats",  
	             "Little lambs eat ivy",  
	             "A kid will eat ivy too"  
	         };  
	   
	         for (int i = 0;  
	              i < importantInfo.length;  
	              i++) {  
	             //Pause for 4 seconds  
	             Thread.sleep(4000);  
	             //Print a message  
	             System.out.println(importantInfo[i]);  
	         }  
	     }  
	 }  
```

main()方法声明了它有可能抛出InterruptedException。当其他线程中断当前线程时，sleep()方法就会抛出该异常。由于这个应用程序并没有定义其他的线程，所以并不用关心如何处理该异常。

#### 中断（Interrupts）

中断是给线程的一个指示，告诉它应该停止正在做的事并去做其他事情。一个线程究竟要怎么响应中断请求取决于程序员，不过让其终止是很普遍的做法。这是本文重点强调的用法。

一个线程通过调用对被中断线程的Thread对象的interrupt()方法，发送中断信号。为了让中断机制正常工作，被中断的线程必须支持它自己的中断（即要自己处理中断）

#### 中断支持

线程如何支持自身的中断？这取决于它当前正在做什么。如果线程正在频繁调用会抛InterruptedException异常的方法，在捕获异常 之后，它只是从run()方法中返回。例如，假设在SleepMessages的例子中，关键的消息循环在线程的Runnable对象的run方法中，代 码可能会被修改成下面这样以支持中断：

```java
	 for (int i = 0; i < importantInfo.length; i++) {  
	     // Pause for 4 seconds  
	     try {  
	        Thread.sleep(4000);  
	     } catch (InterruptedException e) {  
	        // We've been interrupted: no more messages.  
	       return;  
	  }  
	  // Print a message  
	  System.out.println(importantInfo[i]);  
	 }  
```

许多会抛InterruptedException异常的方法(如sleep()），被设计成接收到中断后取消它们当前的操作，并在立即返回。

如果一个线程长时间运行而不调用会抛InterruptedException异常的方法会怎样？ 那它必须周期性地调用Thread.interrupted()方法，该方法在接收到中断请求后返回true。例如：

```java
	 for (int i = 0; i < inputs.length; i++) {  
	     heavyCrunch(inputs[i]);  
	     if (Thread.interrupted()) {  
	         // We've been interrupted: no more crunching.  
	         return;  
	     }  
	 }  
```

在这个简单的例子中，代码只是检测中断，并在收到中断后退出线程。在更复杂的应用中，抛出一个InterruptedException异常可能更有意义。

```java
	 if (Thread.interrupted()){  
	    throw new InterruptedException();  
	 }  
```

这使得中断处理代码能集中在catch语句中。

#### 中断状态标记

中断机制通过使用称为中断状态的内部标记来实现。调用Thread.interrupt()设置这个标记。当线程通过调用静态方法 Thread.interrupted()检测中断时，中断状态会被清除。非静态的isInterrupted()方法被线程用来检测其他线程的中断状 态，不改变中断状态标记。

按照惯例，任何通过抛出一个InterruptedException异常退出的方法，当抛该异常时会清除中断状态。不过，通过其他的线程调用interrupt()方法，中断状态总是有可能会立即被重新设置。

#### Joins

Join()方法可以让一个线程等待另一个线程执行完成。若t是一个正在执行的Thread对象，

```java
	 t.join();  
```

将会使当前线程暂停执行并等待t执行完成。重载的join()方法可以让开发者自定义等待周期。然而，和sleep()方法一样join()方法依赖于操作系统的时间处理机制，你不能假定join()方法将会精确的等待你所定义的时长。
如同sleep()方法，join()方法响应中断并在中断时抛出InterruptedException。

#### 一个简单的线程例子

下面这个简单的例子将会把这一节的一些概念放到一起演示。SimpleThreads程序有两个线程组成，第一个是主线程，它从创建了一个线程并等待它执行完成。如果MessageLoop线程执行了太长时间，主线程将会将其中断。
MessageLoop现场将会打印一系列的信息。如果中断在它打印完所有信息前发生，它将会打印一个特定的消息并退出。

```java
	 public class SimpleThreads {  
	   
	     // Display a message, preceded by  
	     // the name of the current thread  
	     static void threadMessage(String message) {  
	         String threadName =  
	             Thread.currentThread().getName();  
	         System.out.format("%s: %s%n",  
	                           threadName,  
	                           message);  
	     }  
	   
	     private static class MessageLoop  
	         implements Runnable {  
	         public void run() {  
	             String importantInfo[] = {  
	                 "Mares eat oats",  
	                 "Does eat oats",  
	                 "Little lambs eat ivy",  
	                 "A kid will eat ivy too"  
	             };  
	             try {  
	                 for (int i = 0;  
	                      i < importantInfo.length;  
	                      i++) {  
	                     // Pause for 4 seconds  
	                     Thread.sleep(4000);  
	                     // Print a message  
	                     threadMessage(importantInfo[i]);  
	                 }  
	             } catch (InterruptedException e) {  
	                 threadMessage("I wasn't done!");  
	             }  
	         }  
	     }  
	   
	     public static void main(String args[])  
	         throws InterruptedException {  
	   
	         // Delay, in milliseconds before  
	         // we interrupt MessageLoop  
	         // thread (default one hour).  
	         long patience = 1000 * 60 * 60;  
	   
	         // If command line argument  
	         // present, gives patience  
	         // in seconds.  
	         if (args.length > 0) {  
	             try {  
	                 patience = Long.parseLong(args[0]) * 1000;  
	             } catch (NumberFormatException e) {  
	                 System.err.println("Argument must be an integer.");  
	                 System.exit(1);  
	             }  
	         }  
	   
	         threadMessage("Starting MessageLoop thread");  
	         long startTime = System.currentTimeMillis();  
	         Thread t = new Thread(new MessageLoop());  
	         t.start();  
	   
	         threadMessage("Waiting for MessageLoop thread to finish");  
	         // loop until MessageLoop  
	         // thread exits  
	         while (t.isAlive()) {  
	             threadMessage("Still waiting...");  
	             // Wait maximum of 1 second  
	             // for MessageLoop thread  
	             // to finish.  
	             t.join(1000);  
	             if (((System.currentTimeMillis() - startTime) > patience)  
	                   && t.isAlive()) {  
	                 threadMessage("Tired of waiting!");  
	                 t.interrupt();  
	                 // Shouldn't be long now  
	                 // -- wait indefinitely  
	                 t.join();  
	             }  
	         }  
	         threadMessage("Finally!");  
	     }  
	 }  
```

## 同步 

线程间的通信主要是通过共享域和引用相同的对象。这种通信方式非常高效，不过可能会引发两种错误：线程干扰和内存一致性错误。防止这些错误发生的方法是同步。

不过，同步会引起线程竞争，当两个或多个线程试图同时访问相同的资源，随之就导致Java运行时环境执行其中一个或多个线程比原先慢很多，甚至执行被挂起，这就出现了线程竞争。线程饥饿和活锁都属于线程竞争的范畴。关于线程竞争的更多信息可参考活跃度一节。

本节内容包括以下这些主题：

	* 线程干扰讨论了当多个线程访问共享数据时错误是怎么发生的。
	* 内存一致性错误讨论了不一致的共享内存视图导致的错误。
	* 同步方法讨论了 一种能有效防止线程干扰和内存一致性错误的常见做法。
	* 内部锁和同步讨论了更通用的同步方法，以及同步是如何基于内部锁实现的。
	* 原子访问讨论了不能被其他线程干扰的操作的总体思路。

### 线程干扰

下面这个简单的Counter类：

```java
	 class Counter {  
	     private int c = 0;  
	     public void increment() {  
	         c++;  
	     }  
	     public void decrement() {  
	         c--;  
	     }  
	     public int value() {  
	         return c;  
	     }  
	 }  
```

Counter类被设计成：每次调用increment()方法，c的值加1；每次调用decrement()方法，c的值减1。如果当同一个Counter对象被多个线程引用，线程间的干扰可能会使结果同我们预期的不一致。

当两个运行在不同的线程中却作用在相同的数据上的操作交替执行时，就发生了线程干扰。这意味着这两个操作都由多个步骤组成，而步骤间的顺序产生了重叠。

Counter类实例的操作会交替执行，这看起来似乎不太可能，因为c上的这两个操作都是单一而简单的语句。然而，即使一个简单的语句也会被虚拟机转换成多个步骤。我们不去深究虚拟机内部的详细执行步骤——理解c++这个单一的语句会被分解成3个步骤就足够了：

	* 获取当前c的值；
	* 对获取到的值加1；
	* 把递增后的值写回到c；

语句c–也可以按同样的方式分解，除了第二步的操作是递减而不是递增。

假设线程A调用increment()的同时线程B调用decrement().如果c的初始值为0，线程A和B之间的交替执行顺序可能是下面这样：

	* 线程A：获取c；
	* 线程B：获取c；
	* 线程A：对获取的值加1，结果为1；
	* 线程B：对获取的值减1，结果为-1；
	* 线程A：结果写回到c,c现在是1；
	* 线程B：结果写回到c,c现在是-1；

线程A的结果因为被线程B覆盖而丢失了。这个交替执行的结果只是其中一种可能性。在不同的环境下，可能是线程B的结果丢失了，也可能是不会出任何问题。由于结果是不可预知的，所以线程干扰的bug很难检测和修复。

### 内存一致性错误

当不同的线程对相同的数据产生不一致的视图时会发生内存一致性错误。内存一致性错误的原因比较复杂，也超出了本教程的范围。不过幸运的是，一个程序员并不需要对这些原因有详细的了解。所需要的是避免它们的策略。

避免内存一致性错误的关键是理解happens-before关系。这种关系只是确保一个特定语句的写内存操作对另外一个特定的语句可见。要说明这个问题，请参考下面的例子。假设定义和初始化了一个简单int字段：

```java
	 int counter =0 ;  
```

这个counter字段被A，B两个线程共享。假设线程A对counter执行递增:

```java
	 counter++;  
```

然后，很快的，线程B输出counter:

```java
	 System.out.println(counter);  
```

如果这两个语句已经在同一个线程中被执行过，那么输出的值应该是“1”。不过如果这两个语句在不同的线程中分开执行，那输出的值很可能是“0”， 因为无法保证线程A对counter的改动对线程B是可见的——除非我们在这两个语句之间已经建立了happens-before关系。

有许多操作会建立happens-before关系。其中一个是同步，我们将在下面的章节中看到。
我们已经见过两个建立happens-before关系的操作。

当一条语句调用Thread.start方法时，和该语句有happens-before关系的每一条语句，跟新线程执行的每一条语句同样有happens-before关系。创建新线程之前的代码的执行结果对线新线程是可见的。

当一个线程终止并且当导致另一个线程中Thread.join返回时，被终止的线程执行的所有语句和在join返回成功之后的所有语句间有happens-before关系。线程中代码的执行结果对执行join操作的线程是可见的。

要查看建立happens-before关系的操作列表，请参阅java.util.concurrent包的摘要页面。

### 同步方法

Java编程语言提供两种同步方式：同步方法和同步语句。相对较复杂的同步语句将在下一节中介绍。本节主要关注同步方法。

要让一个方法成为同步方法，只需要在方法声明中加上synchronized关键字：

```java
	 public class SynchronizedCounter {  
	     private int c = 0;  
	   
	     public synchronized void increment() {  
	         c++;  
	     }  
	   
	     public synchronized void decrement() {  
	         c--;  
	     }  
	   
	     public synchronized int value() {  
	         return c;  
	     }  
	 }  
```

如果count是SynchronizedCounter类的实例，那么让这些方法成为同步方法有两个作用:
首先，相同对象上的同步方法的两次调用，它们要交替执行是不可能的。 当一个线程正在执行对象的同步方法时，所有其他调用该对象同步方法的线程会被阻塞（挂起执行）,直到第一个线程处理完该对象。

其次，当一个同步方法退出时，它会自动跟该对象同步方法的任意后续调用建立起一种happens-before关系。这确保对象状态的改变对所有线程是可见的。

注意构造方法不能是同步的——构造方法加synchronized关键字会报语法错误。同步的构造方法没有意义，因为当这个对象被创建的时候，只有创建对象的线程能访问它。

	警告：当创建的对象会被多个线程共享时必须非常小心，对象的引用不要过早“暴露”出去。比如，假设你要维护一个叫instances的List，它包含类的每一个实例对象。你可能会尝试在构造方法中加这样一行：

```java
	 instances.add(this);  
```

不过其他线程就能够在对象构造完成之前使用instances访问对象。

同步(synchronized)方法使用一种简单的策略来防止线程干扰和内存一致性错误：如果一个对象对多个线程可见，对象域上的所有读写操作 都是通过synchronized方法来完成的。（一个重要的例外：final域，在对象被创建后不可修改,能被非synchronized方法安全的读 取）。synchronized同步策略很有效，不过会引起活跃度问题，我们将在本节后面看到。

#### 内部锁与同步

同步机制的建立是基于其内部一个叫内部锁或者监视锁的实体。（在Java API规范中通常被称为监视器。）内部锁在同步机制中起到两方面的作用：对一个对象的排他性访问；建立一种happens-before关系，而这种关系正是可见性问题的关键所在。

每个对象都有一个与之关联的内部锁。通常当一个线程需要排他性的访问一个对象的域时，首先需要请求该对象的内部锁，当访问结束时释放内部锁。在线 程获得内部锁到释放内部锁的这段时间里，我们说线程拥有这个内部锁。那么当一个线程拥有一个内部锁时，其他线程将无法获得该内部锁。其他线程如果去尝试获 得该内部锁，则会被阻塞。

当线程释放一个内部锁时，该操作和对该锁的后续请求间将建立happens-before关系。

#### 同步方法中的锁

当线程调用一个同步方法时，它会自动请求该方法所在对象的内部锁。当方法返回结束时则自动释放该内部锁，即使退出是由于发生了未捕获的异常，内部锁也会被释放。

你可能会问调用一个静态的同步方法会如何，由于静态方法是和类（而不是对象）相关的，所以线程会请求类对象(Class Object)的内部锁。因此用来控制类的静态域访问的锁不同于控制对象访问的锁。

#### 同步块

另外一种同步的方法是使用同步块。和同步方法不同，同步块必须指定所请求的是哪个对象的内部锁：
```java
	 public void addName(String name) {  
	     synchronized(this) {  
	         lastName = name;  
	         nameCount++;  
	     }  
	     nameList.add(name);  
	 }  
```

在上面的例子中，addName方法需要使lastName和nameCount的更改保持同步，而且要避免同步调用该对象的其他方法。（在同步代码中调用其他方法会产生Liveness一节所描述的问题。）如果不使用同步块，那么必须要定义一个额外的非同步方法，而这个方法仅仅是用来调用nameList.add。

使用同步块对于更细粒度的同步很有帮助。例如类MsLunch有两个实例域c1和c2，他们并不会同时使用（译者注：即c1和c2是彼此无关的两 个域），所有对这两个域的更新都需要同步，但是完全不需要防止c1的修改和c2的修改相互之间干扰（这样做只会产生不必要的阻塞而降低了并发性）。这种情 况下不必使用同步方法，可以使用和this对象相关的锁。这里我们创建了两个“锁”对象（译者注：起到加锁效果的普通对象lock1和lock2）。

```java
	 public class MsLunch {  
	     private long c1 = 0;  
	     private long c2 = 0;  
	     private Object lock1 = new Object();  
	     private Object lock2 = new Object();  
	   
	     public void inc1() {  
	         synchronized(lock1) {  
	             c1++;  
	         }  
	     }  
	   
	     public void inc2() {  
	         synchronized(lock2) {  
	             c2++;  
	         }  
	     }  
	 }  
```

使用这种方法时要特别小心，需要十分确定c1和c2是彼此无关的域。

### 可重入同步

还记得吗，一个线程不能获得其他线程所拥有的锁。但是它可以获得自己已经拥有的锁。允许一个线程多次获得同一个锁实现了可重入同步。这里描述了一 种同步代码的场景，直接的或间接地，调用了一个也拥有同步代码的方法，且两边的代码使用的是同一把锁。如果没有这种可重入的同步机制，同步代码则需要采取 许多额外的预防措施以防止线程阻塞自己。

### 原子访问

在编程过程中，原子操作是指所有操作都同时发生。原子操作不能被中途打断：要么全做，要么不做。原子操作在完成前不会有看得见的副作用。

我们发现像c++这样的增量表达式，并没有描述原子操作。即使是非常简单的表达式也能够定义成能被分解为其他操作的复杂操作。然而，有些操作你可以定义为原子的：


	* 对引用变量和大部分基本类型变量（除long和double之外）的读写是原子的。
	* 对所有声明为volatile的变量（包括long和double变量）的读写是原子的。


原子操作不会交错，于是可以放心使用，不必担心线程干扰。然而，这并不能完全消除原子操作上的同步，因为内存一致性错误仍可能发生。使用 volatile变量可以降低内存一致性错误的风险，因为对volatile变量的任意写操作，对于后续在该变量上的读操作建立了happens- before关系。这意味着volatile变量的修改对于其他线程总是可见的。更重要的是，这同时也意味着当一个线程读取一个volatile变量时， 它不仅能看到该变量最新的修改，而且也能看到致使该改变发生的代码的副效应。

使用简单的原子变量访问比通过同步代码来访问更高效，但是需要程序员更加谨慎以避免内存一致性错误。至于这额外的付出是否值得，得看应用的大小和复杂度。

java.util.concurrent包中的一些类提供了一些不依赖同步机制的原子方法。我们将在高级并发对象这一节中讨论它们。

## 活跃度 

一个并发应用程序能及时执行的能力称为活跃性。本节将介绍最常见的活跃性问题：死锁（deadlock），以及另外两个活跃性问题：饥饿（starvation）和活锁（livelock）。

### 死锁

死锁描述了这样一种情景，两个或多个线程永久阻塞，互相等待对方释放资源。下面是一个例子。

Alphone和Gaston是朋友，都很讲究礼节。礼节有一个严格的规矩，当你向一个朋友鞠躬时，你必须保持鞠躬的姿势，直到你的朋友有机会回鞠给你。不幸的是，这个规矩没有算上两个朋友相互同时鞠躬的可能。

下面的应用例子，DeadLock，模拟了这个可能性。

```java
	    static class Friend {  
	         private final String name;  
	         public Friend(String name) {  
	             this.name = name;  
	         }  
	         public String getName() {  
	             return this.name;  
	         }  
	         public synchronized void bow(Friend bower) {  
	             System.out.format("%s: %s"  
	                 + "  has bowed to me!%n",  
	                 this.name, bower.getName());  
	             bower.bowBack(this);  
	         }  
	         public synchronized void bowBack(Friend bower) {  
	             System.out.format("%s: %s"  
	                 + " has bowed back to me!%n",  
	                 this.name, bower.getName());  
	         }  
	     }  
	   
	     public static void main(String[] args) {  
	         final Friend alphonse =  
	             new Friend("Alphonse");  
	         final Friend gaston =  
	             new Friend("Gaston");  
	         new Thread(new Runnable() {  
	             public void run() { alphonse.bow(gaston); }  
	         }).start();  
	         new Thread(new Runnable() {  
	             public void run() { gaston.bow(alphonse); }  
	         }).start();  
	     }  
	 }  
```

当DeadLock运行后，两个线程极有可能阻塞，当它们尝试调用bowBack方法时。没有哪个阻塞会结束，因为每个线程都在等待另一个线程退出bow方法。

### 饥饿和活锁

饥饿和活锁并不如死锁一般普遍，但它仍然是每个并发程序设计者可能会遇到的问题。

#### 饥饿

饥饿是指当一个线程不能正常的访问共享资源并且不能正常执行的情况。这通常在共享资源被其他“贪心”的线程长期时发生。举个例子，假设一个对象提 供了一个同步方法，这个方法通常需要执行很长一段时间才返回。如果一个线程经常调用这个方法，那么其他需要同步的访问这个对象的线程就经常会被阻塞。

#### 活锁

一个线程通常会有会响应其他线程的活动。如果其他线程也会响应另一个线程的活动，那么就有可能发生活锁。同死锁一样，发生活锁的线程无法继续执 行。然而线程并没有阻塞——他们在忙于响应对方无法恢复工作。这就相当于两个在走廊相遇的人：Alphonse向他自己的左边靠想让Gaston过去，而 Gaston向他的右边靠想让Alphonse过去。可见他们阻塞了对方。Alphonse向他的右边靠，而Gaston向他的左边靠，他们还是阻塞了对 方。

## 保护块（Guarded Blocks） 

多线程之间经常需要协同工作，最常见的方式是使用Guarded Blocks，它循环检查一个条件（通常初始值为true），直到条件发生变化才跳出循环继续执行。在使用Guarded Blocks时有以下几个步骤需要注意：

假设guardedJoy()方法必须要等待另一线程为共享变量joy设值才能继续执行。那么理论上可以用一个简单的条件循环来实现，但在等待过程中guardedJoy方法不停的检查循环条件实际上是一种资源浪费。

```java
	 public void guardedJoy() {  
	     // Simple loop guard. Wastes  
	     // processor time. Don't do this!  
	     while(!joy) {}  
	     System.out.println("Joy has been achieved!");  
	 }  
```

更加高效的方法是调用Object.wait将当前线程挂起，直到有另一线程发起事件通知（尽管通知的事件不一定是当前线程等待的事件）。

```java
	 public synchronized void guardedJoy() {  
	     // This guard only loops once for each special event, which may not  
	     // be the event we're waiting for.  
	     while(!joy) {  
	         try {  
	             wait();  
	         } catch (InterruptedException e) {}  
	     }  
	     System.out.println("Joy and efficiency have been achieved!");  
	 }  
```

	注意：一定要在循环里面调用wait方法，不要想当然的认为线程唤醒后循环条件一定发生了改变。

和其他可以暂停线程执行的方法一样，wait方法会抛出InterruptedException，在上面的例子中，因为我们关心的是joy的值，所以忽略了InterruptedException。

为什么guardedJoy是synchronized方法？假设d是用来调用wait的对象，当一个线程调用d.wait，它必须要拥有d的内部锁（否则会抛出异常），获得d的内部锁的最简单方法是在一个synchronized方法里面调用wait。

当一个线程调用wait方法时，它释放锁并挂起。然后另一个线程请求并获得这个锁并调用Object.notifyAll通知所有等待该锁的线程。

```java
	 public synchronized notifyJoy() {  
	     joy = true;  
	     notifyAll();  
	 }  
```

当第二个线程释放这个该锁后，第一个线程再次请求该锁，从wait方法返回并继续执行。

	注意：还有另外一个通知方法，notify()，它只会唤醒一个线程。但由于它并不允许指定哪一个线程被唤醒，所以一般只在大规模并发应用（即系统有大量相似任务的线程）中使用。因为对于大规模并发应用，我们其实并不关心哪一个线程被唤醒。

现在我们使用Guarded blocks创建一个生产者/消费者应用。这类应用需要在两个线程之间共享数据：生产者生产数据，消费者使用数据。两个线程通过共享对象通信。在这里，线 程协同工作的关键是：生产者发布数据之前，消费者不能够去读取数据；消费者没有读取旧数据前，生产者不能发布新数据。

在下面的例子中，数据通过Drop对象共享的一系列文本消息：

```java
	 public class Drop {  
	     // Message sent from producer  
	     // to consumer.  
	     private String message;  
	     // True if consumer should wait  
	     // for producer to send message,  
	     // false if producer should wait for  
	     // consumer to retrieve message.  
	     private boolean empty = true;  
	   
	     public synchronized String take() {  
	         // Wait until message is  
	         // available.  
	         while (empty) {  
	             try {  
	                 wait();  
	             } catch (InterruptedException e) {}  
	         }  
	         // Toggle status.  
	         empty = true;  
	         // Notify producer that  
	         // status has changed.  
	         notifyAll();  
	         return message;  
	     }  
	   
	     public synchronized void put(String message) {  
	         // Wait until message has  
	         // been retrieved.  
	         while (!empty) {  
	             try {  
	                 wait();  
	             } catch (InterruptedException e) {}  
	         }  
	         // Toggle status.  
	         empty = false;  
	         // Store message.  
	         this.message = message;  
	         // Notify consumer that status  
	         // has changed.  
	         notifyAll();  
	     }  
	 }  
```

Producer是生产者线程，发送一组消息，字符串DONE表示所有消息都已经发送完成。为了模拟现实情况，生产者线程还会在消息发送时随机的暂停。

```java
	 import java.util.Random;  
	   
	 public class Producer implements Runnable {  
	     private Drop drop;  
	   
	     public Producer(Drop drop) {  
	         this.drop = drop;  
	     }  
	   
	     public void run() {  
	         String importantInfo[] = {  
	             "Mares eat oats",  
	             "Does eat oats",  
	             "Little lambs eat ivy",  
	             "A kid will eat ivy too"  
	         };  
	         Random random = new Random();  
	   
	         for (int i = 0;  
	              i &lt; importantInfo.length;  
	              i++) {  
	             drop.put(importantInfo[i]);  
	             try {  
	                 Thread.sleep(random.nextInt(5000));  
	             } catch (InterruptedException e) {}  
	         }  
	         drop.put("DONE");  
	     }  
	 }  
```

Consumer是消费者线程，读取消息并打印出来，直到读取到字符串DONE为止。消费者线程在消息读取时也会随机的暂停。

```java
	import java.util.Random;  
	  
	public class Consumer implements Runnable {  
	    private Drop drop;  
	  
	    public Consumer(Drop drop) {  
	        this.drop = drop;  
	    }  
	  
	     public void run() {  
	         Random random = new Random();  
	         for (String message = drop.take();  
	              ! message.equals("DONE");  
	              message = drop.take()) {  
	             System.out.format("MESSAGE RECEIVED: %s%n", message);  
	             try {  
	                 Thread.sleep(random.nextInt(5000));  
	             } catch (InterruptedException e) {}  
	         }  
	     }  
	 }  
```

ProducerConsumerExample是主线程，它启动生产者线程和消费者线程。

```java
	public class ProducerConsumerExample {  
	    public static void main(String[] args) {  
	        Drop drop = new Drop();  
	        (new Thread(new Producer(drop))).start();  
	        (new Thread(new Consumer(drop))).start();  
	    }  
	}  
```

	注意：Drop类是用来演示Guarded Blocks如何工作的。为了避免重新发明轮子，当你尝试创建自己的共享数据对象时，请查看Java Collections Framework中已有的数据结构。如需更多信息，请参考Questions and Exercises。

## 不可变对象 

一个对象如果在创建后不能被修改，那么就称为不可变对象。在并发编程中，一种被普遍认可的原则就是：尽可能的使用不可变对象来创建简单、可靠的代码。

在并发编程中，不可变对象特别有用。由于创建后不能被修改，所以不会出现由于线程干扰产生的错误或是内存一致性错误。

但是程序员们通常并不热衷于使用不可变对象，因为他们担心每次创建新对象的开销。实际上这种开销常常被过分高估，而且使用不可变对象所带来的一些 效率提升也抵消了这种开销。例如：使用不可变对象降低了垃圾回收所产生的额外开销，也减少了用来确保使用可变对象不出现并发错误的一些额外代码。

接下来看一个可变对象的类，然后转化为一个不可变对象的类。通过这个例子说明转化的原则以及使用不可变对象的好处。

### 一个同步类的例子

SynchronizedRGB是表示颜色的类，每一个对象代表一种颜色，使用三个整形数表示颜色的三基色，字符串表示颜色名称。

```java
	public class SynchronizedRGB {  
	  
	    // Values must be between 0 and 255.  
	    private int red;  
	    private int green;  
	    private int blue;  
	    private String name;  
	  
	    private void check(int red,  
	                        int green,  
	                        int blue) {  
	         if (red < 0 || red > 255  
	             || green < 0 || green > 255  
	             || blue < 0 || blue > 255) {  
	             throw new IllegalArgumentException();  
	         }  
	     }  
	   
	     public SynchronizedRGB(int red,  
	                            int green,  
	                            int blue,  
	                            String name) {  
	         check(red, green, blue);  
	         this.red = red;  
	         this.green = green;  
	         this.blue = blue;  
	         this.name = name;  
	     }  
	   
	     public void set(int red,  
	                     int green,  
	                     int blue,  
	                     String name) {  
	         check(red, green, blue);  
	         synchronized (this) {  
	             this.red = red;  
	             this.green = green;  
	             this.blue = blue;  
	             this.name = name;  
	         }  
	     }  
	   
	     public synchronized int getRGB() {  
	         return ((red << 16) | (green << 8) | blue);  
	     }  
	   
	     public synchronized String getName() {  
	         return name;  
	     }  
	   
	     public synchronized void invert() {  
	         red = 255 - red;  
	         green = 255 - green;  
	         blue = 255 - blue;  
	         name = "Inverse of " + name;  
	     }  
	}  
```

使用SynchronizedRGB时需要小心，避免其处于不一致的状态。例如一个线程执行了以下代码：

```java
	SynchronizedRGB color =  
	    new SynchronizedRGB(0, 0, 0, "Pitch Black");  
	...  
	int myColorInt = color.getRGB();      //Statement 1  
	String myColorName = color.getName(); //Statement 2  
```

如果有另外一个线程在Statement 1之后、Statement 2之前调用了color.set方法，那么myColorInt的值和myColorName的值就会不匹配。为了避免出现这样的结果，必须要像下面这样把这两条语句绑定到一块执行：

```java
	synchronized (color) {  
	    int myColorInt = color.getRGB();  
	    String myColorName = color.getName();  
	}  
```

这种不一致的问题只可能发生在可变对象上。

### 定义不可变对象的策略

以下的一些规则是创建不可变对象的简单策略。并非所有不可变类都完全遵守这些规则，不过这不是编写这些类的程序员们粗心大意造成的，很可能的是他们有充分的理由确保这些对象在创建后不会被修改。但这需要非常复杂细致的分析，并不适用于初学者。


	1. 不要提供setter方法。（包括修改字段的方法和修改字段引用对象的方法）
	2. 将类的所有字段定义为final、private的。
	3. 不允许子类重写方法。简单的办法是将类声明为final，更好的方法是将构造函数声明为私有的，通过工厂方法创建对象。
	4. 如果类的字段是对可变对象的引用，不允许修改被引用对象。
	> 不提供修改可变对象的方法。
    > 不共享可变对象的引用。当一个引用被当做参数传递给构造函数，而这个引用指向的是一个外部的可变对象时，一定不要保存这个引用。如果必须要保存，那么创建可变对象的拷贝，然后保存拷贝对象的引用。同样如果需要返回内部的可变对象时，不要返回可变对象本身，而是返回其拷贝。

将这一策略应用到SynchronizedRGB有以下几步：

	1. SynchronizedRGB类有两个setter方法。第一个set方法只是简单的为字段设值（译者注：删掉即可），第二个invert方法修改为创建一个新对象，而不是在原有对象上修改。
	2. 所有的字段都已经是私有的，加上final即可。
	3. 将类声明为final的
	4. 只有一个字段是对象引用，并且被引用的对象也是不可变对象。


经过以上这些修改后，我们得到了ImmutableRGB：

```java
	final public class ImmutableRGB {  
	  
	    // Values must be between 0 and 255.  
	    final private int red;  
	    final private int green;  
	    final private int blue;  
	    final private String name;  
	  
	    private void check(int red,  
	                        int green,  
	                        int blue) {  
	         if (red < 0 || red > 255  
	             || green < 0 || green > 255  
	             || blue < 0 || blue > 255) {  
	             throw new IllegalArgumentException();  
	         }  
	     }  
	   
	     public ImmutableRGB(int red,  
	                         int green,  
	                         int blue,  
	                         String name) {  
	         check(red, green, blue);  
	         this.red = red;  
	         this.green = green;  
	         this.blue = blue;  
	         this.name = name;  
	     }  
	   
	     public int getRGB() {  
	         return ((red << 16) | (green << 8) | blue);  
	     }  
	   
	     public String getName() {  
	         return name;  
	     }  
	   
	     public ImmutableRGB invert() {  
	         return new ImmutableRGB(255 - red,  
	                        255 - green,  
	                        255 - blue,  
	                        "Inverse of " + name);  
	     }  
	}  
```

## 高级并发对象 

目前为止，该教程重点讲述了最初作为Java平台一部分的低级别API。这些API对于非常基本的任务来说已经足够，但是对于更高级的任务就需要 更高级的API。特别是针对充分利用了当今多处理器和多核系统的大规模并发应用程序。 本节，我们将着眼于Java 5.0新增的一些高级并发特征。大多数特征已经在新的java.util.concurrent包中实现。Java集合框架中也定义了新的并发数据结构。

	* 锁对象提供了可以简化许多并发应用的锁的惯用法。
	* Executors为加载和管理线程定义了高级API。Executors的实现由java.util.concurrent包提供，提供了适合大规模应用的线程池管理。
	* 并发集合简化了大型数据集合管理，且极大的减少了同步的需求。
	* 原子变量有减小同步粒度和避免内存一致性错误的特征。
	* 并发随机数（JDK7）提供了高效的多线程生成伪随机数的方法。

### 锁对象

同步代码依赖于一种简单的可重入锁。这种锁使用简单，但也有诸多限制。

java.util.concurrent.locks包提供了更复杂的锁。我们不会详细考察这个包，但会重点关注其最基本的接口，锁。  锁对象作用非常类似同步代码使用的隐式锁。如同隐式锁，每次只有一个线程可以获得锁对象。通过关联Condition对 象，锁对象也支持wait/notify机制。 锁对象之于隐式锁最大的优势在于，它们有能力收回获得锁的尝试。如果当前锁对象不可用，或者锁请求超时（如果超时时间已指定），tryLock方法会收回 获取锁的请求。如果在锁获取前，另一个线程发送了一个中断，lockInterruptibly方法也会收回获取锁的请求。 让我们使用锁对象来解决我们在活跃度中 见到的死锁问题。Alphonse和Gaston已经把自己训练成能注意到朋友何时要鞠躬。我们通过要求Friend对象在双方鞠躬前必须先获得锁来模拟 这次改善。下面是改善后模型的源代码，Safelock。为了展示其用途广泛，我们假设Alphonse和Gaston对于他们新发现的稳定鞠躬的能力是 如此入迷，以至于他们无法不相互鞠躬。

```java
	import java.util.concurrent.locks.Lock;  
	import java.util.concurrent.locks.ReentrantLock;  
	import java.util.Random;  
	  
	public class Safelock {  
	    static class Friend {  
	        private final String name;  
	        private final Lock lock = new ReentrantLock();  
	  
	         public Friend(String name) {  
	             this.name = name;  
	         }  
	   
	         public String getName() {  
	             return this.name;  
	         }  
	   
	         public boolean impendingBow(Friend bower) {  
	             Boolean myLock = false;  
	             Boolean yourLock = false;  
	             try {  
	                 myLock = lock.tryLock();  
	                 yourLock = bower.lock.tryLock();  
	             } finally {  
	                 if (! (myLock && yourLock)) {  
	                     if (myLock) {  
	                         lock.unlock();  
	                     }  
	                     if (yourLock) {  
	                         bower.lock.unlock();  
	                     }  
	                 }  
	             }  
	             return myLock && yourLock;  
	         }  
	   
	         public void bow(Friend bower) {  
	             if (impendingBow(bower)) {  
	                 try {  
	                     System.out.format("%s: %s has"  
	                         + " bowed to me!%n",  
	                         this.name, bower.getName());  
	                     bower.bowBack(this);  
	                 } finally {  
	                     lock.unlock();  
	                     bower.lock.unlock();  
	                 }  
	             } else {  
	                 System.out.format("%s: %s started"  
	                     + " to bow to me, but saw that"  
	                     + " I was already bowing to"  
	                     + " him.%n",  
	                     this.name, bower.getName());  
	             }  
	         }  
	   
	         public void bowBack(Friend bower) {  
	             System.out.format("%s: %s has" +  
	                 " bowed back to me!%n",  
	                 this.name, bower.getName());  
	         }  
	 }  
	   
	     static class BowLoop implements Runnable {  
	         private Friend bower;  
	         private Friend bowee;  
	   
	         public BowLoop(Friend bower, Friend bowee) {  
	             this.bower = bower;  
	             this.bowee = bowee;  
	         }  
	   
	         public void run() {  
	             Random random = new Random();  
	             for (;;) {  
	                 try {  
	                     Thread.sleep(random.nextInt(10));  
	                 } catch (InterruptedException e) {}  
	                 bowee.bow(bower);  
	             }  
	         }  
	     }  
	   
	     public static void main(String[] args) {  
	         final Friend alphonse =  
	             new Friend("Alphonse");  
	         final Friend gaston =  
	             new Friend("Gaston");  
	         new Thread(new BowLoop(alphonse, gaston)).start();  
	         new Thread(new BowLoop(gaston, alphonse)).start();  
	     }  
	 }  
```

### 执行器（Executors）

在之前所有的例子中，Thread对象表示的线程和Runnable对象表示的线程所执行的任务之间是紧耦合的。这对于小型应用程序来说没问题， 但对于大规模并发应用来说，合理的做法是将线程的创建与管理和程序的其他部分分离开。封装这些功能的对象就是执行器，接下来的部分将讲详细描述执行器。 

	* 执行器接口定义了三种类型的执行器对象。
	* 线程池是最常见的一种执行器的实现。
	* Fork/Join是JDK 7中引入的并发框架。

### Executor接口

java.util.concurrent中包括三个Executor接口：

	* Executor，一个运行新任务的简单接口。
	* ExecutorService，扩展了Executor接口。添加了一些用来管理执行器生命周期和任务生命周期的方法。
	* ScheduledExecutorService，扩展了ExecutorService。支持Future和定期执行任务。

通常来说，指向Executor对象的变量应被声明为以上三种接口之一，而不是具体的实现类。

#### Executor接口

Executor接口只有一个execute方法，用来替代通常创建（启动）线程的方法。例如：r是一个Runnable对象，e是一个Executor对象。可以使用

```java
	e.execute(r);  
```

来代替

```java
	(new Thread(r)).start();  
```

但execute方法没有定义具体的实现方式。对于不同的Executor实现，execute方法可能是创建一个新线程并立即启动，但更有可能是使用已有的工作线程运行r，或者将r放入到队列中等待可用的工作线程。（我们将在线程池一节中描述工作线程。）

#### ExecutorService接口

ExecutorService接 口在提供了execute方法的同时，新加了更加通用的submit方法。submit方法除了和execute方法一样可以接受Runnable对象作 为参数，还可以接受Callable对象作为参数。使用Callable对象可以能使任务返还执行的结果。通过submit方法返回的Future对象可 以读取Callable任务的执行结果，或是管理Callable任务和Runnable任务的状态。 ExecutorService也提供了批量运行Callable任务的方法。最后，ExecutorService还提供了一些关闭执行器的方法。如果 需要支持即时关闭，执行器所执行的任务需要正确处理中断。

#### ScheduledExecutorService接口

ScheduledExecutorService扩 展ExecutorService接口并添加了schedule方法。调用schedule方法可以在指定的延时后执行一个Runnable或者 Callable任务。ScheduledExecutorService接口还定义了按照指定时间间隔定期执行任务的 scheduleAtFixedRate方法和scheduleWithFixedDelay方法。

### 线程池

在java.util.concurrent包中多数的执行器实现都使用了由工作线程组成的线程池，工作线程独立于所它所执行的Runnable任务和Callable任务，并且常用来执行多个任务。 使用工作线程可以使创建线程的开销最小化。

在大规模并发应用中，创建大量的Thread对象会占用占用大量系统内存，分配和回收这些对象会产生很大的开销。一种最常见的线程池是固定大小的 线程池。这种线程池始终有一定数量的线程在运行，如果一个线程由于某种原因终止运行了，线程池会自动创建一个新的线程来代替它。需要执行的任务通过一个内 部队列提交给线程，当没有更多的工作线程可以用来执行任务时，队列保存额外的任务。 使用固定大小的线程池一个很重要的好处是可以实现优雅退化。例如一个Web服务器，每一个HTTP请求都是由一个单独的线程来处理的，如果为每一个 HTTP都创建一个新线程，那么当系统的开销超出其能力时，会突然地对所有请求都停止响应。如果限制Web服务器可以创建的线程数量，那么它就不必立即处 理所有收到的请求，而是在有能力处理请求时才处理。 创建一个使用线程池的执行器最简单的方法是调用java.util.concurrent.Executors的newFixedThreadPool方法。Executors类还提供了下列一下方法：

	* newCachedThreadPool方法创建了一个可扩展的线程池。适合用来启动很多短任务的应用程序。
	* newSingleThreadExecutor方法创建了每次执行一个任务的执行器。
	* 还有一些创建ScheduledExecutorService执行器的方法。

如果上面的方法都不满足需要，可以尝试java.util.concurrent.ThreadPoolExecutor或者java.util.concurrent.ScheduledThreadPoolExecutor。

### Fork/Joint

fork/join框架是ExecutorService接口的一种具体实现，目的是为了帮助你更好地利用多处理器带来的好处。它是为那些能够被 递归地拆解成子任务的工作类型量身设计的。其目的在于能够使用所有可用的运算能力来提升你的应用的性能。   类似于ExecutorService接口的其他实现，fork/join框架会将任务分发给线程池中的工作线程。fork/join框架的独特之处在与 它使用工作窃取(work-stealing)算法。完成自己的工作而处于空闲的工作线程能够从其他仍然处于忙碌(busy)状态的工作线程处窃取等待执 行的任务。 fork/join框架的核心是ForkJoinPool类，它是对AbstractExecutorService类的扩展。ForkJoinPool实现了工作偷取算法，并可以执行ForkJoinTask任务。

#### 基本使用方法

使用fork/join框架的第一步是编写执行一部分工作的代码。你的代码结构看起来应该与下面所示的伪代码类似：

```java
	if (当前这个任务工作量足够小)  
	    直接完成这个任务  
	else  
	    将这个任务或这部分工作分解成两个部分  
	    分别触发(invoke)这两个子任务的执行，并等待结果  
```

你需要将这段代码包裹在一个ForkJoinTask的子类中。不过，通常情况下会使用一种更为具体的的类型，或者是RecursiveTask(会返回一个结果)，或者是RecursiveAction。 当你的ForkJoinTask子类准备好了，创建一个代表所有需要完成工作的对象，然后将其作为参数传递给一个ForkJoinPool实例的invoke()方法即可。

#### 要清晰，先模糊

想要了解fork/join框架的基本工作原理，接下来的这个例子会有所帮助。假设你想要模糊一张图片。原始的source图片由一个整数的数组 表示，每个整数表示一个像素点的颜色数值。与source图片相同，模糊之后的destination图片也由一个整数数组表示。 对图片的模糊操作是通过对source数组中的每一个像素点进行处理完成的。处理的过程是这样的：将每个像素点的色值取出，与周围像素的色值（红、黄、蓝 三个组成部分）放在一起取平均值，得到的结果被放入destination数组。因为一张图片会由一个很大的数组来表示，这个流程会花费一段较长的时间。 如果使用fork/join框架来实现这个模糊算法，你就能够借助多处理器系统的并行处理能力。下面是上述算法结合fork/join框架的一种简单实 现：

```java
	public class ForkBlur extends RecursiveAction {  
	private int[] mSource;  
	private int mStart;  
	private int mLength;  
	private int[] mDestination;  
	  
	// Processing window size; should be odd.  
	private int mBlurWidth = 15;  
	  
	 public ForkBlur(int[] src, int start, int length, int[] dst) {  
	     mSource = src;  
	     mStart = start;  
	     mLength = length;  
	     mDestination = dst;  
	 }  
	   
	 protected void computeDirectly() {  
	     int sidePixels = (mBlurWidth - 1) / 2;  
	     for (int index = mStart; index &lt; mStart + mLength; index++) {  
	         // Calculate average.  
	         float rt = 0, gt = 0, bt = 0;  
	         for (int mi = -sidePixels; mi &lt;= sidePixels; mi++) {  
	             int mindex = Math.min(Math.max(mi + index, 0),  
	                                 mSource.length - 1);  
	             int pixel = mSource[mindex];  
	             rt += (float)((pixel &amp; 0x00ff0000) &gt;&gt; 16)  
	                   / mBlurWidth;  
	             gt += (float)((pixel &amp; 0x0000ff00) &gt;&gt;  8)  
	                   / mBlurWidth;  
	             bt += (float)((pixel &amp; 0x000000ff) &gt;&gt;  0)  
	                   / mBlurWidth;  
	         }  
	   
	         // Reassemble destination pixel.  
	         int dpixel = (0xff000000     ) |  
	                (((int)rt) &lt;&lt; 16) |  
	                (((int)gt) &lt;&lt;  8) |  
	                (((int)bt) &lt;&lt;  0);  
	         mDestination[index] = dpixel;  
	     }  
	 }  
```

接下来你需要实现父类中的compute()方法，它会直接执行模糊处理，或者将当前的工作拆分成两个更小的任务。数组的长度可以作为一个简单的阀值来判断任务是应该直接完成还是应该被拆分。

```java
	protected static int sThreshold = 100000;  
	  
	protected void compute() {  
	    if (mLength &lt; sThreshold) {  
	        computeDirectly();  
	        return;  
	    }  
	  
	    int split = mLength / 2;  
	   
	     invokeAll(new ForkBlur(mSource, mStart, split, mDestination),  
	               new ForkBlur(mSource, mStart + split, mLength - split,  
	                            mDestination));  
	 }  
```

如果前面这个方法是在一个RecursiveAction的子类中，那么设置任务在ForkJoinPool中执行就再直观不过了。通常会包含以下一些步骤：

(1) 创建一个表示所有需要完成工作的任务。

```java
	// source image pixels are in src  
	// destination image pixels are in dst  
	ForkBlur fb = new ForkBlur(src, 0, src.length, dst);  
```

(2) 创建将要用来执行任务的ForkJoinPool。

```java
	ForkJoinPool pool = new ForkJoinPool();  
```

(3) 执行任务。

```java
	pool.invoke(fb);  
```


想要浏览完成的源代码，请查看ForkBlur，其中还包含一些创建destination图片文件的额外代码。

#### 标准实现

除了能够使用fork/join框架来实现能够在多处理系统中被并行执行的定制化算法（如前文中的ForkBlur.java例子），在Java SE中一些比较常用的功能点也已经使用fork/join框架来实现了。在Java SE 8中，java.util.Arrays类的一系列parallelSort()方法就使用了fork/join来实现。这些方法与sort()系列方法 很类似，但是通过使用fork/join框架，借助了并发来完成相关工作。在多处理器系统中，对大数组的并行排序会比串行排序更快。这些方法究竟是如何运 用fork/join框架并不在本教程的讨论范围内。想要了解更多的信息，请参见Java API文档。 其他采用了fork/join框架的方法还包括java.util.streams包中的一些方法，此包是作为Java SE 8发行版中Project Lambda的一部分。想要了解更多信息，请参见Lambda Expressions一节。

### 并发集合

java.util.concurrent包囊括了Java集合框架的一些附加类。它们也最容易按照集合类所提供的接口来进行分类：

	* BlockingQueue定义了一个先进先出的数据结构，当你尝试往满队列中添加元素，或者从空队列中获取元素时，将会阻塞或者超时。
	* ConcurrentMap是java.util.Map的子接口，定义了一些有用的原子操作。移除或者替换键值对的操作只有当key存在时才能进行，而新增操作只有当key不存在时。使这些操作原子化，可以避免同步。ConcurrentMap的标准实现是ConcurrentHashMap，它是HashMap的并发模式。
	* ConcurrentNavigableMap是ConcurrentMap的子接口，支持近似匹配。ConcurrentNavigableMap的标准实现是ConcurrentSkipListMap，它是TreeMap的并发模式。
	* 所有这些集合，通过 在集合里新增对象和访问或移除对象的操作之间，定义一个happens-before的关系，来帮助程序员避免内存一致性错误。


### 原子变量

java.util.concurrent.atomic包 定义了对单一变量进行原子操作的类。所有的类都提供了get和set方法，可以使用它们像读写volatile变量一样读写原子类。就是说，同一变量上的 一个set操作对于任意后续的get操作存在happens-before关系。原子的compareAndSet方法也有内存一致性特点，就像应用到整 型原子变量中的简单原子算法。   为了看看这个包如何使用，让我们返回到最初用于演示线程干扰的Counter类：

```java
	class Counter {  
	    private int c = 0;  
	    public void increment() {  
	        c++;  
	    }  
	  
	    public void decrement() {  
	        c--;  
	    }  
	   
	     public int value() {  
	         return c;  
	     }  
	 }  
```

使用同步是一种使Counter类变得线程安全的方法，如SynchronizedCounter：

```java
	class SynchronizedCounter {  
		private int c = 0;  
		public synchronized void increment() {  
			c++;  
		}  
		public synchronized void decrement() {  
			c--;  
		}  
		public synchronized int value() {  
			return c;  
		}  
	}  
```

对于这个简单的类，同步是一种可接受的解决方案。但是对于更复杂的类，我们可能想要避免不必要同步所带来的活跃度影响。将int替换为AtomicInteger允许我们在不进行同步的情况下阻止线程干扰，如AtomicCounter：

```java
	import java.util.concurrent.atomic.AtomicInteger;  
	class AtomicCounter {  
		private AtomicInteger c = new AtomicInteger(0);  
		public void increment() {  
		c.incrementAndGet();  
		}  
		  
		public void decrement() {  
			c.decrementAndGet();  
		}  
		   
		public int value() {  
			return c.get();
		}			
	}  
```

### 并发随机数

在JDK7中，java.util.concurrent包含了一个相当便利的类，ThreadLocalRandom，当应用程序期望在多个线程或ForkJoinTasks中使用随机数时。

对于并发访问，使用TheadLocalRandom代替Math.random()可以减少竞争，从而获得更好的性能。

你只需调用ThreadLocalRandom.current()， 然后调用它的其中一个方法去获取一个随机数即可。下面是一个例子：

```java
	int r = ThreadLocalRandom.current().nextInt(4,77);  
```

## 参考
1. ifeve: http://ifeve.com/oracle-java-concurrency-tutorial/
