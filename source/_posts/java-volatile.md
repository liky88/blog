---
title: Java Keyword - volatile/synchronized 
date: 2016-08-20 00:06:14  
categories: Java Core
tags: [Java,synchronized,volatile]

---

# Java Keyword - synchronized
使用此关键字，来进行同步和加锁。当多线程访问的时候，同一时刻只能允许一个线程使用。

# Java Keyword - volatile
使用此关键字来修饰的变量，线程在每次使用此变量的时候，都会读取变量修改后的值，从而保证原子性。但是注意，这样并不能保证整体模块的原子性。  

volatile关键字要求线程在使用变量的时候，必须马上从主内存读取，在修改完成之后，马上将数据写入主内存。  
这个过程无法对线程进行同步。  

比如：  
线程1从主内存读取到count的值为2，还没有操作的时候，线程2从主内存也把count读取到线程内部，这个时候依然是2.  
然后线程1把count自加1设为3，立即刷新到主内存里面。线程2也把counter自加1设置为3，刷新到主内存里面  
这样，线程2的操作就丢失了。  
<!--more-->
  
因此可以使用加锁或者AtomicInteger关键字来处理  
  
代码中的count++，不是原子性操作，这个会编译成count = count +1；（默认会被操作成3个步骤，一个是读取，修改，写入，在任何一步一个线程的介入都会产生脏数据） 所以会出现多线程访问冲突问题。  

**原始代码：**  

	public class Counter {
	
    public static int count = 0;
 
    public static void inc() {
 
        //这里延迟1毫秒，使得结果明显
        try {
            Thread.sleep(1);
        } catch (InterruptedException e) {
        }
 
        count++;
    }
 
    public static void main(String[] args) {
 
        //同时启动1000个线程，去进行i++计算，看看实际结果
 
        for (int i = 0; i < 1000; i++) {
            new Thread(new Runnable() {
                @Override
                public void run() {
                    Counter.inc();
                }
            }).start();
        }
 
        //这里每次运行的值都有可能不同,可能为1000
        System.out.println("运行结果:Counter.count=" + Counter.count);
    }
	}

	运行结果:Counter.count=995
	实际运算结果每次可能都不一样，本机的结果为：运行结果:Counter.count=995，可以看出，在多线程的环境下，Counter.count并没有期望结果是1000
**更改过的代码示例：**  

	package oom;
	import java.util.concurrent.ExecutorService;
	import java.util.concurrent.Executors;
	import java.util.concurrent.TimeUnit;
	import java.util.concurrent.atomic.AtomicInteger;

	public class Counter {

	//	public volatile static int count = 0;
	public static AtomicInteger count = new AtomicInteger(0); 


	public static void inc() {

	// 这里延迟1毫秒，使得结果明显
	try {
	Thread.sleep(1);
	} catch (InterruptedException e) {
	}

	//count++;//如果这里使用count++的话，无法保证原子性
	count.incrementAndGet();
	}

	public static void main(String[] args) throws 	InterruptedException {

	// 同时启动1000个线程，去进行i++计算，看看实际结果
	for (int i = 0; i < 10000; i++) {
	  new Thread(new Runnable() {
	  @Override
	  public void run() {
	  Counter.inc();	
	}
	}).start();
	
	}

	while (Thread.activeCount() > 1) 
	Thread.yield(); 
    //如果不完全结束全部线程，统计Counter.count也是没有意义的
    //因为多线程的时候，main方法是一个主线程，有可能其他线程还没有完成的时候，main可能就已经运行了以下的println
    //所以可以加一个大延迟或者thread join或者计算thread count
	System.out.println("运行结果:Counter.count=" + Counter.count);
	System.out.println(Thread.activeCount());
	
	}
	}

Reference Website:  
1. http://www.cnblogs.com/aigongsi/archive/2012/04/01/2429166.html#!comments