title: 读书笔记之深入理解Java虚拟机：JVM高级特性与最佳实践（周志明著）（一）  
tags: [JVM,java]  
date: 2017-10-10  

---  

### Java内存区域 ###  

Java虚拟机管理的内存包括以下几个运行时数据区域：  
方法区、堆、虚拟机栈、本地方法栈、程序计数器  
其中方法区和堆是所有线程共享的数据区，虚拟机栈、本地方法栈和程序计数器是线程隔离的数据区。  

#### 程序计数器 ####  

程序计数器可以看作当前线程所执行的字节码的行号指示器。  

#### Java虚拟机栈 ####  

线程私有，生命周期与线程相同，Java虚拟机栈描述的是Java方法执行的内存模型：每个方法在执行的同时都会创建一个栈帧用于存储局部变量表、操作数栈、动态链接、方法出口等信息。  
局部变量表存放基本数据类型，所需的空间在编译期间完成分配。long和double类型的数据会占用2个局部变量空间。  

#### 本地方法栈 ####  

本地方法栈为虚拟机使用到的native方法服务

#### 虚拟机栈和本地方法栈溢出 ####

关于虚拟机栈和本地方法栈，在Java虚拟机规范中描述了两种异常：  
- 如果线程请求的栈深度大于虚拟机所允许的最大深度，将抛出StackOverflowError异常  
- 如果虚拟机在扩展栈时无法申请到足够的内存空间，则抛出OutOfMemoryError异常  

栈容量由-Xss参数设定

#### Java堆 ####   

此内存区域的唯一目的是存放对象实例，几乎所有的对象实例都在这里分配内存。Java堆中可以细分为：新生代和老年代  

堆的扩展可通过-Xms和-Xmx控制，将-Xms和-Xmx设置为一样既可避免堆自动扩展。如果堆中没有内存完成实例分配，并且堆也无法再扩展时，将会抛出OutOfMemoryError异常  
通过参数-XX:+HeapDumpOnOutOfMemoryError可以让虚拟机在出现内存溢出异常时Dump出当前的内存堆转储快照以便事后进行分析

#### 方法区（HotSpot开发者更愿意称它为永久代，永久代有-XX:MaxPermSize上限） ####    

方法区与Java堆一样，是各个线程共享的内存区域，它用于存储已被虚拟机加载的类信息、常量、静态变量、即时编译器编译后的代码等数据。运行时常量池是方法区的一部分。当常量池无法再申请到内存时会抛出OutOfMemoryError异常  

#### 方法区和运行时常量池溢出 ####  

在JDK1.6及之前的版本中，由于常量池分配在永久代内，我们可以通过-XX:PermSize和-XX:MaxPermSize限制方法区大小  

String.intern()是一个Native方法，它的作用是：如果字符串常量池中已经包含此String对象的字符串，则返回代表池中这个字符串的String对象；否则，将此String对象包含的字符串添加到常量池中，并且返回此String对象的引用。
在JDK1.7的intern()实现不会再复制实例，只是在常量池中记录首次出现的实例引用。  

#### 本机直接内存溢出 ####  

DirectMemory容量可通过-XX:MaxDirectMemorySize指定，如果不指定，则默认与Java最大堆内存一样。  

### 怎样判断对象已死? ###   

1. 引用计数算法：引用就加一不再引用就减一，为0的是不再引用的（很难解决对象之间相互循环引用的问题）  
2. 可达性分析来判断对象是否存活：通过一系列的称为“GC Roots”的对象作为起始点，从这些节点开始向下搜索，搜索所走过的路径成为引用链，当一个对象到GC Roots没有任何引用链相连时，则证明此对象是不可用的。  

一个对象真正死亡需要经过两次标记，经过可达性分析标记之后还要判断该对象是否有必要执行finalize()方法。当对象没有覆盖finalize()方法或者虚拟机已经执行过finalize()方法，均为“没有必要执行”。

如果该对象有必要执行finalize()方法，该对象将被放入F-Queue队列中，等待虚拟机开启的Finalizer线程（低优先级）进行回收。finalize()方法是对象逃脱死亡的最后一次机会，因为一个对象的finalize()方法额最多只会被系统自动调用一次。  

#### 回收方法区 ####  

在堆中，尤其是新生代中，常规进行一次垃圾回收可以回收70%~95%的空间，而永久代的垃圾收集效率远低于此。  
永久代的垃圾回收主要由两部分内容：废弃常量和无用的类  
类需要同时满足下面3个条件才能算是无用的类：  
1. 该类所有的实例都已经被回收，也就是Java堆中不存在该类的任何实例  
2. 加载该类的ClassLoader已经被回收  
3. 该类对应的java.lang.Class对象没有在任何地方被引用，无法在任何地方通过反射访问该类的方法  
