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
