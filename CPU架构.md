![image-20240827105909593](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240827105909593.png)

#### I/O

I/O是计算机系统与外部设备进行通信的过程

UNIX 系统下， IO 模型一共有 5 种：**同步阻塞 I/O**、**同步非阻塞 I/O**、**I/O 多路复用**、**信号驱动 I/O** 和**异步 I/O**。

Java中常见的3种I/O模型：BIO、NIO、AIO

BIO：同步阻塞模型

在调用read后会产生阻塞，等待数据全部就绪后返回

![image-20240902133534824](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240902133534824.png)

在客户端连接数量不高的情况下，是没问题的。但是，当面对十万甚至百万级连接的时候，传统的 BIO 模型是无能为力的。因此，我们需要一种更高效的 I/O 处理模型来应对更高的并发量。

NIO：同步非阻塞

同步非阻塞 IO 模型中，应用程序会一直发起 read 调用，等待数据从内核空间拷贝到用户空间的这段时间里，线程依然是阻塞的，直到在内核把数据拷贝到用户空间。

相比于同步阻塞 IO 模型，同步非阻塞 IO 模型确实有了很大改进。通过轮询操作，避免了一直阻塞。

但是，这种 IO 模型同样存在问题：应用程序不断进行 I/O 系统调用轮询数据是否已经准备好的过程是十分消耗 CPU 资源的。

![image-20240902133828361](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240902133828361.png)

多路复用

![image-20240902134244413](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240902134244413.png)

IO 多路复用模型中，线程首先发起 select 调用，询问内核数据是否准备就绪，等内核把数据准备好了，用户线程再发起 read 调用。read 调用的过程（数据从内核空间 -> 用户空间）还是阻塞的。

> 目前支持 IO 多路复用的系统调用，有 select，epoll 等等。select 系统调用，目前几乎在所有的操作系统上都有支持。
>
> - **select 调用**：内核提供的系统调用，它支持一次查询多个系统调用的可用状态。几乎所有的操作系统都支持。
> - **epoll 调用**：linux 2.6 内核，属于 select 调用的增强版本，优化了 IO 的执行效率。

**IO 多路复用模型，通过减少无效的系统调用，减少了对 CPU 资源的消耗。**

Java 中的 NIO ，有一个非常重要的**选择器 ( Selector )** 的概念，也可以被称为 **多路复用器**。通过它，只需要一个线程便可以管理多个客户端连接。当客户端数据到了之后，才会为其服务。

![image-20240902134331239](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240902134331239.png)

AIO：异步非阻塞

在操作后会直接返回，等待调用结束后才通知线程进行后续操作

![image-20240902134717765](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240902134717765.png)

总结：

![image-20240902134813142](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240902134813142.png)

#### NIO详解

NIO的三个核心组件

1、Buffer缓冲区：NIO读和写都是通过缓冲区进行操作，读的时候将数据从Channel中读到缓冲区，写的时候是将数据从缓冲区中写到Channel中。

2、Channel通道：Channel是一个双向的，可读可写的数据通道，

3、Selector选择器：用来选择线程处理哪一个通道

##### Buffer

```java
public abstract class Buffer{
    // Invariants: mark <= position <= limit <= capacity
    private int mark = -1;
    private int position = 0;
    private int limit;
    private int capacity;
}
```

（容量）capacity:缓冲区大小，初始化的时候指定，且不可改变

（界限）limit: Buffer可读/写的边界，写模式下limit等于capacity，表示可写入数据的大小。读模式下，limit

应该等于实际写入数据的大小。

（位置）position：下一个可以被读写数据的位置（类似下标吧），

（标记）mark：可以直接定位到标记位置

上述变量满足如下的关系：**0 <= mark <= position <= limit <= capacity** 。

怎么切换Buffer读写模式：创建Buffer时默认模式是写模式。调用flip()方法切换读模式，如果想要再切换回写模式可以调用compact和clear方法。

Buffer不能通过new 的放式来创建实例，只能通过其提供的静态方法来初始化

```java
//分配直接内存
public static ByteBuffer allocateDirect(int capacity) {
        return new DirectByteBuffer(capacity);
    }
//分配堆内存
 public static ByteBuffer allocate(int capacity) {
        if (capacity < 0)
            throw new IllegalArgumentException();
        return new HeapByteBuffer(capacity, capacity);
    }

```

Buffer中两个核心方法：

get：从缓冲区中读取数据

put：向缓存区中写入数据

其他重要方法：

flip():将缓冲区从写模式切换到读模式，它会将 `limit` 的值设置为当前 `position` 的值，将 `position` 的值设置为 0。

clear(): 将缓冲区从读模式恢复到写模式，将limit设置为capacity的值position值为零

Channel通道：Channel是一个通道，建立了和数据源之间的链接可以利用通道来读取和写入数据，可以通过Channel在Buffer中读取和写入数据

![image-20240902162412944](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240902162412944.png)

另外，因为 Channel 是全双工的，所以它可以比流更好地映射底层操作系统的 API。特别是在 UNIX 网络编程模型中，底层操作系统的通道都是全双工的，同时支持读写操作。

Channel中常用的方法

- `FileChannel`：文件访问通道；
- `SocketChannel`、`ServerSocketChannel`：TCP 通信通道；
- `DatagramChannel`：UDP 通信通道；

![image-20240902162523560](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240902162523560.png)

Channel中核心的两个方法：

read():写入到缓冲区

write()：从缓冲区读取数据到Channel

Selector选择器：

允许一个线程操作多个Channel，selector会轮询所有注册在该选择器上的Channel，当事件发生时，Selector会将这个channel轮询出来，加入就绪集合中。通过 SelectionKey 可以获取就绪 Channel 的集合，然后对这些就绪的 Channel 进行相应的 I/O 操作

![image-20240902165945197](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240902165945197.png)

一个多路复用器 Selector 可以同时轮询多个 Channel，由于 JDK 使用了 `epoll()` 代替传统的 `select` 实现，所以它并没有最大连接句柄 `1024/2048` 的限制。这也就意味着只需要一个线程负责 Selector 的轮询，就可以接入成千上万的客户端。

Selector 可以监听以下四种事件类型：

1. `SelectionKey.OP_ACCEPT`：表示通道接受连接的事件，这通常用于 `ServerSocketChannel`。
2. `SelectionKey.OP_CONNECT`：表示通道完成连接的事件，这通常用于 `SocketChannel`。
3. `SelectionKey.OP_READ`：表示通道准备好进行读取的事件，即有数据可读。
4. `SelectionKey.OP_WRITE`：表示通道准备好进行写入的事件，即可以写入数据。

**`Selector`是抽象类，可以通过调用此类的 `open()` 静态方法来创建 Selector 实例。Selector 可以同时监控多个 `SelectableChannel` 的 `IO` 状况，是非阻塞 `IO` 的核心。**

Selector 还提供了一系列和 `select()` 相关的方法：

- `int select()`：监控所有注册的 `Channel`，当它们中间有需要处理的 `IO` 操作时，该方法返回，并将对应的 `SelectionKey` 加入被选择的 `SelectionKey` 集合中，该方法返回这些 `Channel` 的数量。
- `int select(long timeout)`：可以设置超时时长的 `select()` 操作。
- `int selectNow()`：执行一个立即返回的 `select()` 操作，相对于无参数的 `select()` 方法而言，该方法不会阻塞线程。
- `Selector wakeup()`：使一个还未返回的 `select()` 方法立刻返回。

