title: 读书笔记之Java并发编程的艺术(二)  
tags: [Java,并发]  
date: 2017-10-16  

---

**Synchronized原理**

Java中的每一个对象都可以作为锁。具体表现为以下3种形式。  
对于普通同步方法，锁是当前实例对象  
对于静态同步方法，锁是当前类的Class对象  
对于同步方法块，锁是Synchronized括号里配置的对象  

从JVM规范中可以看到Synchronized在JVM里的实现原理。JVM基于进入和退出Monitor对象来实现方法同步和代码块同步，
但两者的实现细节不一样  
代码块同步是使用monitorenter和monitorexit指令实现的，而方法同步是使用另外一种方式实现的  

monitorenter指令是在编译后插入到同步代码块的开始位置，而monitorexit是插入到方法结
束处和异常处，JVM要保证每个monitorenter必须有对应的monitorexit与之配对。任何对象都有
一个monitor与之关联，当且一个monitor被持有后，它将处于锁定状态。线程执行到monitorenter
指令时，将会尝试获取对象所对应的monitor的所有权，即尝试获得对象的锁
