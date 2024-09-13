hashMap回顾笔记

#### 集合

java中集合有 ：List、Set和Map

三个集合的特点：

List：有序可重复

Set：无序，不可重复（set集合是基于map实现的，但其value域存的是一个对象类型，元素存在key域

Map:K-V结构，键唯一，值不唯一

List的实现类有：ArrayList、LinkedList、Vector、ArrayList

Set：HashSet、TreeSet

Map：常见的有HasMap、TreeMap，LinkedHashMap

HashMap重点：HashMap扩容机制，1.7和1.8的区别，扰动函数等

​	插入过程：

> 1、先初始化
>
> 2、计算hash值，查找结点位置
>
> 3、判断该位置是否有元素，判断是否出现hash碰撞，遍历链表，若key值相同，则直接覆盖原值，若key值不同则在链表尾部添加元素（1.8以后都是尾插法）
>
> 4、添加完元素后判断链表是否需要转为红黑树（链表长度大于8，节点个数大于64），判断是否需要扩容（节点数是否大于阈值，此处要扯到负载因子，hashmap的负载因子默认是0.75f，阈值=负载因子*hashMap的容量，此处HashMap的容量可以自己设置，但是真实容量为大于设置值的最小2的指数次方）

红黑树转化、退化成链表、扩容的条件需要记忆。

线程安全的map：

- HashTable， 基于synchronized同步锁实现，效率低

- ConcurretHashMap：基于CAS 和分段锁

  1.7及以前都是采用分段锁机制来提高并发性能，每个segment加一个同步锁，不同segment之间不受影响

  1.8以后采用了更细的锁粒度，链表的每个bucket桶加一把锁（即数组的每一个节点都有一把锁，当对不同节点的链表进行写操作时互不影响。）

#### 常用日期时间类 

Calendar 日期类

 Calendar是一个日期抽象类，可以进行日期运算，由于其是一个抽象类，所以在使用时我们可以使用他的子类GregorianCalendar类，可以通过直接new GregorianCalendar() 的方式，也可以使用Calendar的静态方法getInstance来获取GregorianCalendar类对象。

常用方法：

```java
int get(int field); //该方法可以获取到指定字段的值，例如年份月份，日期，和时、分、秒，星期
void set(int field,int value)//设置某个字段的值
void add(int field,int amount)//给某个值增加或减少指定的值
//field 参数
Calendar.YEAR:获取年份
Calendar.MONTH:获取月份 //Calendar提供的月份范围为0-11，使用时需要+1
Calendar.DAY_OF_MONTH:月中的日期
Calendar.HOUR:小时
Calendar.MINUTE:分钟
Calendar.SECOND:秒
Calendar.DAY_OF_WEEK:星期 // 提供的值为1-7，但1-7分别代表的是周日到周六，使用时需要注意
```

SimpleDateFormat类

可以将Date类型按照指定格式转为String类型，也可以将String类型的时间解析为Date类型

![image-20240730192808905](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240730192808905.png)

常用方法

> String format(Date date);  // 将Date类型转换为字符串类型
>
> Date parse(String date);  //将字符串解析为Date类型

```java
//日期转字符串
SimpleDateFormate sdf=new SimpleDateFormate();
Date date=new Date(0L);
String strDate=sdf.format(date);
//也可以自定义日期格式
SimpleDateFormate sdf=new SimpleDateFormate(" yyyy-MM-dd HH:mm:ss");
Date date=new Date(0L);
String strDate=sdf.format(date);

//日期字符串转Date
String date1="2024-06-06";
//此处的日期格式要和字符串的格式一样，如果date1=202406-06 09:22:22,则格式化日期应为"yyyy-MM-dd HH:mm:ss"
SimpleDateFormate sdf=new SimpleDateFormate("yyyy-MM-dd");
Date date=sdf.parse(date1);

```

![image-20240730193501394](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240730193501394.png)

#### Java对象：

由三部分组成，对象头、对象实例数据和对齐填充

对象头：又包括 Mark word和Klass word

- Mark Word 存储对象的hashCode 、锁信息或分代信息或GC标志等
- Klass Word：存储对象指向所属类的指针，JVM以此来判别对象所属的类

对象实例数据：对象成员变量的数据

![image-20240801095407116](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240801095407116.png)

synchronized锁



#### 线程

线程状态：

新建态、运行态、等待态、阻塞态、终态

![image-20240823153308452](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240823153308452.png)

创建线程的三种方式：

1. 继承Thread类
2. 实现Runnable接口
3. 实现callable类

上下文切换：

每个线程都有自己的运行条件和状态（称为上下文），而当线程退出cpu时 ， cpu会保存当前线程的状态，等线程再次获取到时间片运行时会继续从上次结束的状态开始运行，这就叫上下文切换。

当线程时间片用完、线程让出cpu执行时间（例如调用sleep、wait方法）、线程发生阻塞时都会进行上下文切换，而当线程终止或者执行完毕退出时不会上下文切换



sleep方法和wait方法的区别

1. sleep()是Thread类下的方法，wait()是Object类下的方法
2. 调用wait让获得对象锁的线程实现等待会释放锁，sleep不会
3. sleep方法是暂停执行，能自己恢复，而wait需要被notify或者notifyall方法唤醒

多线程

并发和并行的区别

1. 并发：两个或两个以上线程在同一时间段内运行
2. 并发：两个或两个以上线程在同一时间点运行

同步和异步的区别

1、同步是线程发起一个调用时，若结果不返回，则一直等待。

2、异步是发起一个调用时，不用等返回值，可以直接返回

为什么要使用多线程？ 

因为并发量大

什么是线程安全、什么是线程不安全？

线程安全是指多个线程对同一份资源进行操作访问时能够保持数据的一致性和安全性。反之线程不安全就是多个线程操作同一份资源事不能保证数据的一致性和安全性

什么是死锁？

死锁是多个线程被同时阻塞，在等待某一个线程释放资源

死锁的四个必要条件？

互斥条件：线程在任何时候都只能持有一个资源

请求与保持：在请求其他资源的时候，不释放已持有的资源

不可剥夺：线程已持有的资源在没有使用完之前不可强制剥夺，只能等线程使用完主动释放

循环等待：多个线程间资源互相申请，形成环路

#### volatile

volatile的作用是保证变量的可见性，在多线程编程时要保证每一个线程都能获取到资源的最新状态（即当共享资源在被改变时，其他线程都能拿到该资源最新的值）	

并发编程中的三个特性：可见性、原子性、有序性

volatile除了能保证变量的可见性之外，还能保证有序性，即禁止指令重排序

volatile应用例子：单例模式的双重检查锁

悲观锁和乐观锁

悲观锁是默认发生最坏的情况，默认每次访问共享资源的时候都会出现问题，所以每次访问共享资源时都会加锁，其他线程想要访问该共享资源时必须等到占有该资源的线程释放锁

乐观锁是默认最好的情况，认为每次访问共享资源时不会发生问题，线程可以不停的执行，不需要加锁也不需要等待，只是再提交修改的时候去验证对应的资源是否被其他线程修改（cas算法或者版本号机制）

版本号机制：在数据库中增加version字段，每次操作数据提交时，先与version字段的值比较，当修改时读取到的version和当前的version值相等时提交修改，然后更新version+1，

如果版本号不一致则提交修改失败，说明该值在提交之前已被其他线程修改

CAS算法：

cas算法中有三个值 ：V(要更新的变量值（原值)）、E（预期原值）、N（将要写入更新的值）

进行更新操作时，只有V=E时才能完成更新操作，如果V和E不相等则说明值已经被其他线程修改

synchronized锁

作用在代码块（获取指定对象或类的锁）、静态方法（获取类的锁）、普通方法上（获取类对象的锁）。

volatile和synchronized的区别

1、volatile是轻量级的，性能比synchronized高，但是volatile只能修饰变量，synchronized能修饰同步代码块和静态方法、非静态方法。

2、volatile能保证变量的可见性，不能保证原子性，synchronized都能保证。

3、volatile主要用于线程间共享变量的可见性，synchronized用于保证线程的同步性

reentrandLock

![image-20240828125125328](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240828125125328.png)

公平锁和非公平锁

什么是公平锁：公平锁是线程在请求获取锁时按照等待队列顺序来获取锁，是一个有序的过程

非公平锁：线程会先申请锁资源，如果能拿到则直接获取该锁，如果拿不到在进去等待队列，可能导致有些线程永远也获取不到锁

可重入锁：当一个线程获取到一个对象锁 但还没释放锁 再次想获取到该对象锁时还是可以获取的。

共享锁和独占锁

共享锁可以被多个线程同时持有

独占锁只能被一个线程持有



ThreadLocal

线程私有的一块存储区域

​		Thread部分源码

```java
public class Thread implements Runnable {
    //......
    //与此线程有关的ThreadLocal值。由ThreadLocal类维护
    ThreadLocal.ThreadLocalMap threadLocals = null;

    //与此线程有关的InheritableThreadLocal值。由InheritableThreadLocal类维护
    ThreadLocal.ThreadLocalMap inheritableThreadLocals = null;
    //......
}
```

ThreadLocal的set方法

```java
public void set(T value) {
    //获取当前请求的线程
    Thread t = Thread.currentThread();
    //取出 Thread 类内部的 threadLocals 变量(哈希表结构)
    ThreadLocalMap map = getMap(t);
    if (map != null)
        // 将需要存储的值放入到这个哈希表中
        map.set(this, value);
    else
        createMap(t, value);
}
ThreadLocalMap getMap(Thread t) {
    return t.threadLocals;
}
```

![image-20240828150857000](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240828150857000.png)

![image-20240828150916065](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240828150916065.png)

ThreadLocal内存泄漏问题

讲这个问题先来看看Java中几种不同的引用：强引用、弱引用、软引用、虚引用

强引用：是一种直接的引用，直接指向对象，通常使用new 关键字生成的对象 （ Object o=new Object()方式），只要有变量指向堆中的对象，那么该对象具有强引用，JVM不会清理该对象，即使内存空间满了也不会清理。

软引用：允许Java在内存不足时回收这些软引用对象，

软引用是通过一个软引用的类来创建的：创建软引用对象的方式为

```java
java SoftReference<?> m = new SoftReference<?>(new ?)
```

弱引用：（ WeakReference类 ）弱引用的对象，如没有任何强引用的对象指向他，则将被垃圾回收器回收。弱引用可以与引用队列（`java.lang.ref.ReferenceQueue`）联合使用，当弱引用所指向的对象被垃圾回收时，这个弱引用会被加入到与之关联的引用队列中

虚引用：

。。。。



在ThreadLocalMap中 value是强引用的，而key为ThreadLocal是弱引用，当ThreadLocal没有被强引用时，会被垃圾回收器回收掉，而value不被回收，导致内存泄漏。



线程池

优点：1、提高运行效率，不需要再创建线程

​	    2、提高线程的可管理性

​	    3、降低资源消耗：不用重复的创建线程

如何创建线程；

1、使用ThreadPoolExecutor创建

2、使用Executors创建

​	线程池种类

​		固定线程数线程池

​		单线程线程池

​		周期线程池

​		可缓存线程池

线程池参数

corePoolSize:核心线程数，核心线程不会被销毁，为了接收任务减少创建线程的开销

maxmuimSize：线程池可创建的最大线程数

keepAliveTime：非核心线程未执行任务时的存活时间

unit：时间单位

workQueue：等待队列

ThreadFactory：线程工厂

handler：拒绝策略

```java
    /**
     * 用给定的初始参数创建一个新的ThreadPoolExecutor。
     */
    public ThreadPoolExecutor(int corePoolSize,//线程池的核心线程数量
                              int maximumPoolSize,//线程池的最大线程数
                              long keepAliveTime,//当线程数大于核心线程数时，多余的空闲线程存活的最长时间
                              TimeUnit unit,//时间单位
                              BlockingQueue<Runnable> workQueue,//任务队列，用来储存等待执行任务的队列
                              ThreadFactory threadFactory,//线程工厂，用来创建线程，一般默认即可
                              RejectedExecutionHandler handler//拒绝策略，当提交的任务过多而不能及时处理时，我们可以定制策略来处理任务
                               ) {
        if (corePoolSize < 0 ||
            maximumPoolSize <= 0 ||
            maximumPoolSize < corePoolSize ||
            keepAliveTime < 0)
            throw new IllegalArgumentException();
        if (workQueue == null || threadFactory == null || handler == null)
            throw new NullPointerException();
        this.corePoolSize = corePoolSize;
        this.maximumPoolSize = maximumPoolSize;
        this.workQueue = workQueue;
        this.keepAliveTime = unit.toNanos(keepAliveTime);
        this.threadFactory = threadFactory;
        this.handler = handler;
    }
```

![image-20240829131153967](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240829131153967.png)

拒绝策略：

针对等待队列满以及达到最大线程数的拒绝策略

AbortPolicy：抛出RejectedExecutionException来拒绝新任务

DiscardPolicy：不接受新任务的策略

DiscardOldestPolicy：抛弃最早的未处理的任务请求

CallerRunsPolicy：不会丢弃新的任务，当队列满以及达到最大线程数的时候，会返回给任务调用者，既通过主线程执行这个任务，如果任务时间较长的话可能会导致阻塞。

线程池任务执行流程：

1、任务接入时会先判断是否达到核心线程数，如果没达到则建一个线程执行任务

2、若达到核心线程数则判断等待队列是否满，若没满就加入等待队列，

3、若满了则判断是否达到最大线程数，没达到就建一个新线程来执行任务

4、若等待队列满且达到最大线程数则触发拒绝策略

线程池中线程异常后，销毁还是复用

1、如果是通过execute执行线程，出现异常时会直接销毁该线程，并且再重新新建一个线程，来保证原本的线程数量。

2、如果是通过submit执行线程，出现异常不会销毁，可以通过Future.get()来获取线程异常，线程继续复用。

等待队列：

1、无界阻塞队列

。。。

Future类

future用于获取异步执行任务的线程的执行结果，有了future就不用再继续等待线程任务执行完成，可以等任务异步完成后通过future获取。

future中的五个方法

```java
public interface Future<V> {

    /**
     * Attempts to cancel execution of this task.  This attempt will
     * fail if the task has already completed, has already been cancelled,
     * or could not be cancelled for some other reason. If successful,
     * and this task has not started when {@code cancel} is called,
     * this task should never run.  If the task has already started,
     * then the {@code mayInterruptIfRunning} parameter determines
     * whether the thread executing this task should be interrupted in
     * an attempt to stop the task.
     *
     * <p>After this method returns, subsequent calls to {@link #isDone} will
     * always return {@code true}.  Subsequent calls to {@link #isCancelled}
     * will always return {@code true} if this method returned {@code true}.
     *
     * @param mayInterruptIfRunning {@code true} if the thread executing this
     * task should be interrupted; otherwise, in-progress tasks are allowed
     * to complete
     * @return {@code false} if the task could not be cancelled,
     * typically because it has already completed normally;
     * {@code true} otherwise
     */
    boolean cancel(boolean mayInterruptIfRunning);

    /**
     * Returns {@code true} if this task was cancelled before it completed
     * normally.
     *
     * @return {@code true} if this task was cancelled before it completed
     */
    boolean isCancelled();

    /**
     * Returns {@code true} if this task completed.
     *
     * Completion may be due to normal termination, an exception, or
     * cancellation -- in all of these cases, this method will return
     * {@code true}.
     *
     * @return {@code true} if this task completed
     */
    boolean isDone();

    /**
     * Waits if necessary for the computation to complete, and then
     * retrieves its result.
     *
     * @return the computed result
     * @throws CancellationException if the computation was cancelled
     * @throws ExecutionException if the computation threw an
     * exception
     * @throws InterruptedException if the current thread was interrupted
     * while waiting
     */
    V get() throws InterruptedException, ExecutionException;

    /**
     * Waits if necessary for at most the given time for the computation
     * to complete, and then retrieves its result, if available.
     *
     * @param timeout the maximum time to wait
     * @param unit the time unit of the timeout argument
     * @return the computed result
     * @throws CancellationException if the computation was cancelled
     * @throws ExecutionException if the computation threw an
     * exception
     * @throws InterruptedException if the current thread was interrupted
     * while waiting
     * @throws TimeoutException if the wait timed out
     */
    V get(long timeout, TimeUnit unit)
        throws InterruptedException, ExecutionException, TimeoutException;
}
```

1、取消任务

2、判断任务是否被取消

3、判断任务是否执行完成

4、获取任务执行结果

IO流

输入流：inputStream/Reader	

输出流:outputStream/Writer

#### JVM

jvm区域划分

线程私有：虚拟机栈，本地方法栈，程序计数器

线程共有：元空间、堆



##### 虚拟机栈

和数据结构中的栈一样，都是先进先出，不过虚拟机栈中的入栈元素是栈帧，每次调用一个方法的时候都会有往栈中压入一个栈帧，方法调用结束后会弹出一个栈帧，栈帧包括方法的局部变量表、操作数栈、动态链接、方法返回地址。

**局部变量表** 主要存放了编译期可知的各种数据类型（boolean、byte、char、short、int、float、long、double）、对象引用（reference 类型，它不同于对象本身，可能是一个指向对象起始地址的引用指针，也可能是指向一个代表对象的句柄或其他与此对象相关的位置）。

**操作数栈** 主要作为方法调用的中转站使用，用于存放方法执行过程中产生的中间计算结果。另外，计算过程中产生的临时变量也会放在操作数栈中。

**动态链接** 主要是用于方法内调用其他方法。Class 文件的常量池里保存有大量的符号引用比如方法引用的符号引用。当一个方法要调用其他方法，需要将常量池中指向方法的符号引用转化为其在内存地址中的直接引用。动态链接的作用就是为了将符号引用转换为调用方法的直接引用，这个过程也被称为 **动态连接** 。

##### 本地方法栈

本地方法栈和虚拟机栈类似，但是虚拟机栈存放的是java方法，而本地方法栈是用来执行native方法。也是有局部变量表，操作数栈、动态链接和方法返回地址

##### **程序计数器**

程序计数器是一块较小的内存空间，可以看作是当前线程所执行的字节码的行号指示器。字节码解释器工作时通过改变这个计数器的值来选取下一条需要执行的字节码指令，分支、循环、跳转、异常处理、线程恢复等功能都需要依赖这个计数器来完成。

另外，为了线程切换后能恢复到正确的执行位置，每条线程都需要有一个独立的程序计数器，各线程之间计数器互不影响，独立存储，我们称这类内存区域为“线程私有”的内存。

从上面的介绍中我们知道了程序计数器主要有两个作用：

- 字节码解释器通过改变程序计数器来依次读取指令，从而实现代码的流程控制，如：顺序执行、选择、循环、异常处理。
- 在多线程的情况下，程序计数器用于记录当前线程执行的位置，从而当线程被切换回来的时候能够知道该线程上次运行到哪儿了。

⚠️ 注意：程序计数器是唯一一个不会出现 `OutOfMemoryError` 的内存区域，它的生命周期随着线程的创建而创建，随着线程的结束而死亡。

##### 堆

堆的唯一目的就是用来存对象实例，几乎所有的对象实例和数组都在堆中分配内存，是虚拟机中最大的一块区域。

jdk1.7以后好像有一个叫什么逃逸啥的。就是说一个对象的引用要是没被返回的话且只在这一个方法中使用时，也可以在栈中创建实例。

元空间（方法区）
