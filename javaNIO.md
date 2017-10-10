title: java并发编程之NIO  
tags: [并发编程,NIO,java]  
date: 2017-09-20

---  

### Java NIO核心技术 ###  
核心部分：Selectors(选择器)，Channels(通道)，Buffers(缓存)  
Java NIO的通道类似于流，但有些不同：  
- 通道是双向的，流的读写时单向的  
- 通道可以异步地读写  
- 通道中的数据总是先要读到一个Buffer,或者总是从一个Buffer中写入  

Channel的实现有FileChannel,DatagramChannel,SocketChannel,ServerSocketChannel  
FileChannel从文件中读写数据  
DatagramChannel能通过UDP读写网络中的数据  
SocketChannel能通过TCP读写网络中的数据  
ServerSocketChannel可以监听新进来的TCP连接，像Web服务器那样。对每一个新进来的连接都会创建一个SocketChannel  

Buffer的基本用法：  
使用Buffer读写数据一般遵循以下四个步骤：  
1. 写入数据到Buffer  
2. 调用flip()方法  
3. 从Buffer中读取数据  
4. 调用clear()方法或者compact()方法  

当向Buffer写入数据时，Buffer会记录下写了多少数据。一旦要读取数据，需要通过flip()方法将Buffer从写模式切换到读模式。在读模式下，可以获取之前写入到Buffer的所有数据  
一旦读完了所有的数据，就需要清空缓冲区，让它可以再次被写入。有两种方式能清空缓冲区：调用clear()和compact()方法。clear()方法会清空整个缓冲区。compact()方法只会清除已经读过的数据。任何未读的数据都被移到缓冲区的起始处，新写入的数据将放到缓冲区未读数据的后面。  

Buffer有三个属性：capacity,position,limit  
通过调用Buffer.mark()方法，可以标记Buffer中的一个特定position。之后可以通过调用Buffer.reset()方法恢复到这个position。  

仅使用单个线程来处理多个Channels的好处是，只需要更少的线程来处理通道。对于操作系统来说，线程之间上下文切换的开销很大，而且每个线程都要占用系统的一些资源（如内存）。因此，使用的线程越少越好。  

Channel与Selector一起使用时，必须将Channel注册到Selector上。而Channel与Selector一起使用时，Channel必须处于非阻塞模式下，。这意味着不能将FileChannel与Selector一起使用，因为FileChannel不能切换到非阻塞模式。而套接字通道可以  

Channel注册到Selector还要传入Channel感兴趣的事件（SelectionKey.OP_CONNECT，SelectionKey.OP_ACCEPT
，SelectionKey.OP_READ，SelectionKey.OP_WRITE等）  

SelectionKey  
当想Selector注册Channel时，register()方法会返回一个SelectionKey对象，这个对象包含了一些属性：interest集合，ready集合，Channel，Selector，附加的对象  

通过Selector选择通道  
一旦向Selector注册了一或多个Channel,就可以调用几个重载的select()方法，这些方法返回你所感兴趣的时间已经准备就绪的那些通道。
### Java NIO与IO区别 ###  
| IO | NIO |  
|:----:|:-----:|  
|面向流|面向缓冲|  
|阻塞|非阻塞|  
|无|选择器|  

Java IO是面向流的，意味着每次从流中读一个或多个字节，直至读取所有字节。线程阻塞时无法对流中的数据进行操作，如果想要对流中的数据进行操作可以将流中的数据缓存到缓冲区中。  

Java NIO是面向缓冲的，数据读取到一个它稍后处理的缓冲区，需要时可在缓冲区中前后移动。增加了处理过程中的灵活性。但是需要检查缓冲区中是否包含您需要处理的数据，为了保证更多的数据读入缓冲区，不要覆盖缓冲区里尚未处理的数据。当线程从通道中读取数据时，如果通道中没有数据需要处理，该线程可以做其他事情。
