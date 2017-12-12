title: Prepare For Interview  
tags: [Interview]  
date: 2017-11-11  

---
### Prepare For Interview ###

### Java Basic ###  




### Java 并发面试题收集整理 ###  

1.谈谈对volatile的理解  
volitale是轻量级的synchronized,它保证了多处理器中共享变量的内存可见性，它不会引起线程的上下文切换和调度,禁止指令重排序

2.volatile是如何保证共享变量的“可见性”的（volatiled的底层实现机制）  
因为使用volatile修饰的变量在编译时会出现一个Lock前缀指令，这个指令有两个作用：
a. 将当前处理器缓存行的数据回写到系统内存  
b. 这个写回内存的操作会使其他CPU里缓存该内存地址的数据无效，数据无效了程序就会直接访问系统内存，获取该变量  

3.什么是内存可见性，什么是重排序  
提到内存可见性就要提到多线程之间的通信是如何进行的，线程之间的交互有两种方式：共享内存和消息传递  
在共享内存的并发模型中，线程之间共享程序的公共状态，线程之间通过读写内存中的公共状态来隐式进行通信，典型的共享内存通信方式就是通过共享对象进行通信  
在消息传递的并发模型里，线程之间没有公共状态，线程之间必须通过明确的发送消息来显式进行通信，在java中典型的消息传递方式就是wait()和notify()  

Java的并发采用的是共享内存模型  
Java虚拟机规范定义了一种Java内存模型来达到住各种平台上都能达到一致的内存访问效果  
Java内存模型规定了线程之间的共享变量都存在主内存中，每个线程都一个私有的本地内存，本地内存中存储了该线程使用的共享变量的副本,线程之间的通信过程必须要经过主内存，JMM通过  
控制主内存与每个线程的本地内存之间的交互，来为java程序员提供内存可见性的保证  

原子性，可见性，有序性  

JMM是允许编译器和处理器对指令进行重新排序的，重排序是指编译器和处理器为了优化程序性能对指令序列进行重新排序的一种手段  

volatile关键字可以禁止重排序，或者使用synchronized和lock对代码块或方法加锁来保证有序性  

4.volatile的运用  
单例模式的实现，双重检查锁定

##### 多线程 #####  
1.java中有几种方法可以实现一个线程  
两种方式：继承Thread类和实现Runable接口  

2.如何停止一个正在运行的线程  
1）使用退出标志，使线程正常退出，就是当run方法完成后终止  
2）使用stop强行终止，不推荐，已过时  
3）使用interrupt方法中断线程（interrupt方法只是打了一个停止标记，并不是真的停止线程，想要真的停止需要加判断，interrupted方法清除状态标记，isInterrrupted方法不清除状态标记）  

3.sleep()和wait()有什么区别？  
sleep为休眠状态，wait等待状态，区别在于是否释放锁，sleep不释放，wait释放  

4.什么是守护线程，它有什么意义？  
可以理解为保姆，陪伴的意思。守护线程的作用：为其他线程的运行提供便利服务，最典型的守护线程就是垃圾回收器GC  

5.java如何实现多线程之间的通讯和协作  
共享内存的方式实现  

6.什么是可重入锁（ReentrantLock）?  

### 设计模式 ###  

##### 单例模式 #####  

单例类只能有一个实例  
单例类必须自己创建自己的唯一实例  
单例类必须给所有其他对象提供这一实例  

保证一个类仅有一个实例，并提供一个访问他的全局访问点  

单例模式的实现：  
1.懒汉式，线程不安全  

```
public class Singleton{  
    private static Singleton instance;  
    private Singleton(){

    }
    public static Singleton getinstance(){
        if(instance == null){
            instance = new Singleton();
        }
        return instance;
    }
}
```
2.懒汉式，线程安全  
缺点：必须加锁synchronized才能保证单例，但加锁会影响效率  

```
public class Singleton{
    private static Singleton instance;
    private Singleton(){

    }
    public static synchronized Singleton getInstance(){
        if(instance == null){
            instance = new Singleton();
        }
        return instance;
    }

}
```
3.饿汉式  
缺点：类加载时就初始化，浪费内存  
优点：没有加锁，执行效率会提高  
```
public class Singleton{
    private static Singleton instance = new Singleton();
    private Singleton(){

    }
    public static Singleton getInstance(){
        return instance;
    }
}
```  
4.双重检查锁定  
```
public class Singleton{
    private volatile static Singleton singleton;
    private Singleton(){

    }
    public static Singleton getInstance(){
        if(singleton == null){
            synchronized (Singleton.class){
                if(singleton == null){
                    singleton = new Singleton();
                }
            }
        }
        return singleton;
    }
}
```
