title: 读书笔记之Java多线程编程核心技术（高洪岩著）（二）  
tags: [java,多线程]  
date:2017-10-11  

---  
#### 线程间通信 ####  

##### wait/notify机制 #####  
wait使线程停止运行，而notify使停止的线程继续运行  
wait方法是Object类的方法，该方法用来将当前线程置入“预执行队列”，并且在wait所在的代码行处停止执行，直到接到通知或被中断为止。在带哦用wait之前，线程必须获得该对象的对象级别锁，即只能在同步方法或同步代码块中调用wait方法，在执行wait方法后，当前线程释放锁。如果调用wait时没有调用适当的锁，则抛出IllegalMonitorStateException,它是RuntimeException的一个字类，不需要try catch进行捕捉异常   
notify也要在同步方法或同步代码块中调用，即在调用前，线程也必须获得该对象的对象级别锁。如果调用notify时没有持有适当的锁，也会抛出IllegalMonitorStateException异常  
在notify方法执行后，当前线程不会马上释放该对象锁，呈wait状态的线程也不能马上获取该对象锁，要等到执行notify方法的线程将程序执行完，也就是退出synchronized代码块后，当前线程才会释放锁  

##### 生产者/消费者模式 #####  

join()方法的作用是等待线程对象的销毁  
方法join具有使线程排队运行的作用，有些类似同步的运行效果。join与synchronized的区别是：join在内部使用wait()方法进行等待，而synchronized关键字使用的事“对象监视器”原理作为同步  
方法join(long)的功能在内部是使用wait(long)方法来实现的，所以join(long)方法具有释放锁的特点  


##### ThreadLocal #####  

ThreadLocal使得每一个线程都有自己的共享变量  
