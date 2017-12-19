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

ThreadLocal类用来提供线程内部的局部变量。这些变量在多线程环境下访问(通过get或set方法访问)时能保证各个线程里的变量相对独立于其他线程内的变量，ThreadLocal实例通常来说都是private static类型  
ThreadLocal类中有ThreadLocalMap静态类，ThreadLocalMap中有Entry静态类，Entry静态类继承了WeakReference  

##### InheritableThreadLocal的使用 #####  

使用类 InheritableThreadLocal可以在子线程中取得父线程继承下来的值  

#### Lock的使用 ####  

##### ReentrantLock类 #####  

可重入锁（待学习）

##### 公平锁与非公平锁 #####  

公平锁与非公平锁：锁Lock分为“公平锁”和“非公平锁”，公平锁表示线程获取锁的顺序是按照线程加锁大的顺序来分配的，即先来先得的FIFO先进先出顺序。而非公平锁就是一种获取所得抢占机制，是随机获取锁的，和公平锁不一样的就是先来的不一定先得到锁，这个方式可能造成某些线程一直拿不到锁，结果也就是不公平的了。  
