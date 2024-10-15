GitHub：

### 集合

Collection定义了一些集合类长用的方法

```java
public interface Collection<E> extends Iterable<E> {
    //-------这些是查询相关的操作----------

   	//获取当前集合中的元素数量
    int size();

    //查看当前集合是否为空
    boolean isEmpty();

    //查询当前集合中是否包含某个元素
    boolean contains(Object o);

    //返回当前集合的迭代器，我们会在后面介绍
    Iterator<E> iterator();

    //将集合转换为数组的形式
    Object[] toArray();

    //支持泛型的数组转换，同上
    <T> T[] toArray(T[] a);

    //-------这些是修改相关的操作----------

    //向集合中添加元素，不同的集合类具体实现可能会对插入的元素有要求，
  	//这个操作并不是一定会添加成功，所以添加成功返回true，否则返回false
    boolean add(E e);

    //从集合中移除某个元素，同样的，移除成功返回true，否则false
    boolean remove(Object o);


    //-------这些是批量执行的操作----------

    //查询当前集合是否包含给定集合中所有的元素
  	//从数学角度来说，就是看给定集合是不是当前集合的子集
    boolean containsAll(Collection<?> c);

    //添加给定集合中所有的元素
  	//从数学角度来说，就是将当前集合变成当前集合与给定集合的并集
  	//添加成功返回true，否则返回false
    boolean addAll(Collection<? extends E> c);

    //移除给定集合中出现的所有元素，如果某个元素在当前集合中不存在，那么忽略这个元素
  	//从数学角度来说，就是求当前集合与给定集合的差集
  	//移除成功返回true，否则false
    boolean removeAll(Collection<?> c);

    //Java8新增方法，根据给定的Predicate条件进行元素移除操作
    default boolean removeIf(Predicate<? super E> filter) {
        Objects.requireNonNull(filter);
        boolean removed = false;
        final Iterator<E> each = iterator();   //这里用到了迭代器，我们会在后面进行介绍
        while (each.hasNext()) {
            if (filter.test(each.next())) {
                each.remove();
                removed = true;
            }
        }
        return removed;
    }

    //只保留当前集合中在给定集合中出现的元素，其他元素一律移除
  	//从数学角度来说，就是求当前集合与给定集合的交集
  	//移除成功返回true，否则false
    boolean retainAll(Collection<?> c);

    //清空整个集合，删除所有元素
    void clear();


    //-------这些是比较以及哈希计算相关的操作----------

    //判断两个集合是否相等
    boolean equals(Object o);

    //计算当前整个集合对象的哈希值
    int hashCode();

    //与迭代器作用相同，但是是并行执行的，我们会在下一章多线程部分中进行介绍
    @Override
    default Spliterator<E> spliterator() {
        return Spliterators.spliterator(this, 0);
    }

    //生成当前集合的流，我们会在后面进行讲解
    default Stream<E> stream() {
        return StreamSupport.stream(spliterator(), false);
    }

    //生成当前集合的并行流，我们会在下一章多线程部分中进行介绍
    default Stream<E> parallelStream() {
        return StreamSupport.stream(spliterator(), true);
    }
}
```



java中集合有 ：List、Set和Map

三个集合的特点：

List：有序可重复

Set：无序，不可重复（set集合是基于map实现的，但其value域存的是一个对象类型，元素存在key域

Map:K-V结构，键唯一，值不唯一

List的实现类有：ArrayList、LinkedList、Vector、ArrayList

Set：HashSet、TreeSet

Map：常见的有HasMap、TreeMap，LinkedHashMap

HashMap重点：HashMap扩容机制，1.7和1.8的区别，扰动函数等

#### List集合

```java
//List是一个有序的集合类，每个元素都有一个自己的下标位置
//List中可插入重复元素
//针对于这些特性，扩展了Collection接口中一些额外的操作
public interface List<E> extends Collection<E> {
    ...
   	
    //将给定集合中所有元素插入到当前结合的给定位置上（后面的元素就被挤到后面去了，跟我们之前顺序表的插入是一样的）
    boolean addAll(int index, Collection<? extends E> c);

    ...

   	//Java 8新增方法，可以对列表中每个元素都进行处理，并将元素替换为处理之后的结果
    default void replaceAll(UnaryOperator<E> operator) {
        Objects.requireNonNull(operator);
        final ListIterator<E> li = this.listIterator();  //这里同样用到了迭代器
        while (li.hasNext()) {
            li.set(operator.apply(li.next()));
        }
    }

    //对当前集合按照给定的规则进行排序操作，这里同样只需要一个Comparator就行了
    @SuppressWarnings({"unchecked", "rawtypes"})
    default void sort(Comparator<? super E> c) {
        Object[] a = this.toArray();
        Arrays.sort(a, (Comparator) c);
        ListIterator<E> i = this.listIterator();
        for (Object e : a) {
            i.next();
            i.set((E) e);
        }
    }
    ...
    //-------- 这些是List中独特的位置直接访问操作 --------

   	//获取对应下标位置上的元素
    E get(int index);

    //直接将对应位置上的元素替换为给定元素
    E set(int index, E element);

    //在指定位置上插入元素，就跟我们之前的顺序表插入是一样的
    void add(int index, E element);

    //移除指定位置上的元素
    E remove(int index);


    //------- 这些是List中独特的搜索操作 -------

    //查询某个元素在当前列表中的第一次出现的下标位置
    int indexOf(Object o);

    //查询某个元素在当前列表中的最后一次出现的下标位置
    int lastIndexOf(Object o);


    //------- 这些是List的专用迭代器 -------

    //迭代器我们会在下一个部分讲解
    ListIterator<E> listIterator();

    //迭代器我们会在下一个部分讲解
    ListIterator<E> listIterator(int index);

    //------- 这些是List的特殊转换 -------

    //返回当前集合在指定范围内的子集
    List<E> subList(int fromIndex, int toIndex);

    ...
}
```

##### ArrayList

ArrayList底层是基于数组实现的，有序且允许重复，当删除有重复的元素时，只删除下标最靠前的那一个

```java
public class ArrayList<E> extends AbstractList<E>
        implements List<E>, RandomAccess, Cloneable, java.io.Serializable
{
		
    //默认的数组容量
    private static final int DEFAULT_CAPACITY = 10;

    ...

    //存放数据的底层数组，这里的transient关键字我们会在后面I/O中介绍用途
    transient Object[] elementData;

    //记录当前数组元素数的
    private int size;

   	//这是ArrayList的其中一个构造方法
    public ArrayList(int initialCapacity) {
        if (initialCapacity > 0) {
            this.elementData = new Object[initialCapacity];   //根据初始化大小，创建当前列表
        } else if (initialCapacity == 0) {
            this.elementData = EMPTY_ELEMENTDATA;
        } else {
            throw new IllegalArgumentException("Illegal Capacity: "+
                                               initialCapacity);
        }
    }
  
  	...
      
   	public boolean add(E e) {
        ensureCapacityInternal(size + 1);  // 这里会判断容量是否充足，不充足需要扩容
        elementData[size++] = e;
        return true;
    }
  	
  	...
    
    //默认的列表最大长度为Integer.MAX_VALUE - 8
    //JVM的C++实现中，在数组的对象头中有一个_length字段，用于记录数组的长
    //度，所以这个8就是存了数组_length字段（这个只做了解就行）
		private static final int MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8;
  	
  	private void grow(int minCapacity) {
        int oldCapacity = elementData.length;
        int newCapacity = oldCapacity + (oldCapacity >> 1);   //扩容规则跟我们之前的是一样的，也是1.5倍
        if (newCapacity - minCapacity < 0)    //要是扩容之后的大小还没最小的大小大，那么直接扩容到最小的大小
            newCapacity = minCapacity;
        if (newCapacity - MAX_ARRAY_SIZE > 0)   //要是扩容之后比最大的大小还大，需要进行大小限制
            newCapacity = hugeCapacity(minCapacity);  //调整为限制的大小
        elementData = Arrays.copyOf(elementData, newCapacity);   //使用copyOf快速将内容拷贝到扩容后的新数组中并设定为新的elementData底层数组
    }
}
```

##### LinkedList

LinkedList是基于双向链表实现的，其基本操作和ArrayList差不多，只是在插入和删除以及查找的效率上存在差异，在使用中需要根据自己的实际需求来选择使用哪一个。不过LinkedList不仅可以当作list来用，也可以当作双端队列来使用。

```java
public class LinkedList<E>
    extends AbstractSequentialList<E>
    implements List<E>, Deque<E>, Cloneable, java.io.Serializable
{
    transient int size = 0;

    //引用首结点
    transient Node<E> first;

    //引用尾结点
    transient Node<E> last;

    //构造方法，很简单，直接创建就行了
    public LinkedList() {
    }
  
  	...
      
    private static class Node<E> {   //内部使用的结点类
        E item;
        Node<E> next;   //不仅保存指向下一个结点的引用，还保存指向上一个结点的引用
        Node<E> prev;

        Node(Node<E> prev, E element, Node<E> next) {
            this.item = element;
            this.next = next;
            this.prev = prev;
        }
    }
    ...
}
```

#### Map集合

Map集合是一种K-V键值对的集合，每一个Key 对应一个Value。例如每一个学生都有一个学号ID，那么我们就可以把这个学号ID作为Map集合的key，学生的信息作为value，这样每一个ID都对应着唯一的一个学生信息。

![image-20241010150518919](D:\TXT\图片文件\image-20241010150518919.png)

Map集合的顶层接口Interface Map定义了一些Map集合常用的操作：

```java
//Map并不是Collection体系下的接口，而是单独的一个体系，因为操作特殊
//这里需要填写两个泛型参数，其中K就是键的类型，V就是值的类型，比如上面的学生信息，ID一般是int，那么键就是Integer类型的，而值就是学生信息，所以说值是学生对象类型的
public interface Map<K,V> {
    //-------- 查询相关操作 --------
  
  	//获取当前存储的键值对数量
    int size();

    //是否为空
    boolean isEmpty();

    //查看Map中是否包含指定的键
    boolean containsKey(Object key);

    //查看Map中是否包含指定的值
    boolean containsValue(Object value);

    //通过给定的键，返回其映射的值
    V get(Object key);
    //通过给定的键，获取值，但如果不存在的话则返回给定的Value默认值
    V getOrDefault(K key,V value);

    //-------- 修改相关操作 --------

    //向Map中添加新的映射关系，也就是新的键值对
    V put(K key, V value);
    //向Map中添加新元素，如果被添加的元素的key在原来的集合中不存在的话就加入成功，否则不添加新元素
    V putIfAbsent(K key,V value);

    //根据给定的键，移除其映射关系，也就是移除对应的键值对
    V remove(Object key);


    //-------- 批量操作 --------

    //将另一个Map中的所有键值对添加到当前Map中
    void putAll(Map<? extends K, ? extends V> m);

    //清空整个Map
    void clear();


    //-------- 其他视图操作 --------

    //返回Map中存放的所有键，以Set形式返回
    Set<K> keySet();

    //返回Map中存放的所有值
    Collection<V> values();

    //返回所有的键值对，这里用的是内部类Entry在表示
    Set<Map.Entry<K, V>> entrySet();

    //这个是内部接口Entry，表示一个键值对
    interface Entry<K,V> {
        //获取键值对的键
        K getKey();

        //获取键值对的值
        V getValue();

        //修改键值对的值
        V setValue(V value);

        //判断两个键值对是否相等
        boolean equals(Object o);

        //返回当前键值对的哈希值
        int hashCode();

        ...
    }

    ...
}
```

在Map集合中，key是唯一的，即使value值不同，如何添加两个相同的key，那么后添加的key的Value会覆盖掉第一次添加的Value值。

##### HashMap

HashMap 的底层采用Hash表实现，我们在向HashMap 中添加元素的顺序并不一定是元素在集合内的实际顺序。HashMap在map的基础上做了扩充，支持自动扩容，而且还在jdk1.8以后引入了红黑树，提高了查询效率

```java
public class HashMap<K,V> extends AbstractMap<K,V>
    implements Map<K,V>, Cloneable, Serializable {
  
  	...
    
  	static class Node<K,V> implements Map.Entry<K,V> {   //内部使用结点，实际上就是存放的映射关系
        final int hash;
        final K key;   //跟我们之前不一样，我们之前一个结点只有键，而这里的结点既存放键也存放值，当然计算哈希还是使用键
        V value;
        Node<K,V> next;
				...
    }
  	
  	...
  
  	transient Node<K,V>[] table;   //这个就是哈希表本体了，可以看到跟我们之前的写法是一样的，也是头结点数组，只不过HashMap中没有设计头结点（相当于没有头结点的链表）
  
  	final float loadFactor;   //负载因子，这个东西决定了HashMap的扩容效果
  
  	public HashMap() {
        this.loadFactor = DEFAULT_LOAD_FACTOR; //当我们创建对象时，会使用默认的负载因子，值为0.75
    }
  
  	...     
}
```

​	插入过程：

> 1、先初始化
>
> 2、计算hash值，查找结点位置
>
> 3、判断该位置是否有元素，判断是否出现hash碰撞，遍历链表，若key值相同，则直接覆盖原值，若key值不同则在链表尾部添加元素（1.8以后都是尾插法，因为在多线程的环境下头插法容易出现回路）
>
> 4、添加完元素后判断链表是否需要转为红黑树（链表长度大于8，节点个数大于64），判断是否需要扩容（节点数是否大于阈值，此处要扯到负载因子，hashmap的负载因子默认是0.75f，阈值=负载因子*hashMap的容量，此处HashMap的容量可以自己设置，但是真实容量为大于设置值的最小2的指数次方）

> [!NOTE]
>
> 红黑树转化、退化成链表、扩容的条件需要记忆。

因为HashMap的元素是无序的，所以如果对插入顺序有需求的话可以使用LinkedHashMap，其是基于双向链表实现的Map集合，其他特点和HashMap无太大区别。

HashMap的put方法：

```java
//这块代码还是比较重要的哈哈哈哈
public V put(K key, V value) {
  	//这里计算完键的哈希值之后，调用的另一个方法进行映射关系存放
    return putVal(hash(key), key, value, false, true);
}

final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
               boolean evict) {
    Node<K,V>[] tab; Node<K,V> p; int n, i;
    if ((tab = table) == null || (n = tab.length) == 0)  //如果底层哈希表没初始化，先初始化
        n = (tab = resize()).length;   //通过resize方法初始化底层哈希表，初始容量为16，后续会根据情况扩容，底层哈希表的长度永远是2的n次方 
  	//因为传入的哈希值可能会很大，这里同样是进行取余操作
  	//(n - 1) & hash 等价于 hash % n 这里的i就是最终得到的下标位置了
    if ((p = tab[i = (n - 1) & hash]) == null)
        tab[i] = newNode(hash, key, value, null);   //如果这个位置上什么都没有，那就直接放一个新的结点
    else {   //这种情况就是哈希冲突了
        Node<K,V> e; K k;
        if (p.hash == hash &&   //如果上来第一个结点的键的哈希值跟当前插入的键的哈希值相同，键也相同，说明已经存放了相同键的键值对了，那就执行覆盖操作
            ((k = p.key) == key || (key != null && key.equals(k))))
            e = p;   //这里直接将待插入结点等于原本冲突的结点，一会直接覆盖
        else if (p instanceof TreeNode)   //如果第一个结点是TreeNode类型的，说明这个链表已经升级为红黑树了
            e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);  //在红黑树中插入新的结点
        else {
            for (int binCount = 0; ; ++binCount) {  //普通链表就直接在链表尾部插入
                if ((e = p.next) == null) {
                    p.next = newNode(hash, key, value, null);  //找到尾部，直接创建新的结点连在后面
                    if (binCount >= TREEIFY_THRESHOLD - 1) //如果当前链表的长度已经很长了，达到了阈值
                        treeifyBin(tab, hash);			//那么就转换为红黑树来存放
                    break;   //直接结束
                }
                if (e.hash == hash &&
                    ((k = e.key) == key || (key != null && key.equals(k))))  //同样的，如果在向下找的过程中发现已经存在相同键的键值对了，直接结束，让p等于e一会覆盖就行了
                    break;
                p = e;
            }
        }
        if (e != null) { // 如果e不为空，只有可能是前面出现了相同键的情况，其他情况e都是null，所有直接覆盖就行
            V oldValue = e.value;
            if (!onlyIfAbsent || oldValue == null)
                e.value = value;
            afterNodeAccess(e);
            return oldValue;   //覆盖之后，会返回原本的被覆盖值
        }
    }
    ++modCount;
    if (++size > threshold)   //键值对size计数自增，如果超过阈值，会对底层哈希表数组进行扩容
        resize();   //调用resize进行扩容
    afterNodeInsertion(evict);
    return null;  //正常插入键值对返回值为null
}
```

当节点数超过规定的阈值时会通过revise()方法进行扩容操作

```java
final Node<K,V>[] resize() {
    Node<K,V>[] oldTab = table;   //先把下面这几个旧的东西保存一下
    int oldCap = (oldTab == null) ? 0 : oldTab.length;
    int oldThr = threshold;
    int newCap, newThr = 0;  //这些是新的容量和扩容阈值
    if (oldCap > 0) {  //如果旧容量大于0，那么就开始扩容
        if (oldCap >= MAXIMUM_CAPACITY) {  //如果旧的容量已经大于最大限制了，那么直接给到 Integer.MAX_VALUE
            threshold = Integer.MAX_VALUE;
            return oldTab;  //这种情况不用扩了
        }
        else if ((newCap = oldCap << 1) < MAXIMUM_CAPACITY &&
                 oldCap >= DEFAULT_INITIAL_CAPACITY)   //新的容量等于旧容量的2倍，同样不能超过最大值
            newThr = oldThr << 1; //新的阈值也提升到原来的两倍
    }
    else if (oldThr > 0) // 旧容量不大于0只可能是还没初始化，这个时候如果阈值大于0，直接将新的容量变成旧的阈值
        newCap = oldThr;
    else {               // 默认情况下阈值也是0，也就是我们刚刚无参new出来的时候
        newCap = DEFAULT_INITIAL_CAPACITY;   //新的容量直接等于默认容量16
        newThr = (int)(DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY); //阈值为负载因子乘以默认容量，负载因子默认为0.75，也就是说只要整个哈希表用了75%的容量，那么就进行扩容，至于为什么默认是0.75，原因很多，这里就不解释了，反正作为新手，这些都是大佬写出来的，我们用就完事。
    }
    ...
    threshold = newThr;
    @SuppressWarnings({"rawtypes","unchecked"})
    Node<K,V>[] newTab = (Node<K,V>[])new Node[newCap];
    table = newTab;   //将底层数组变成新的扩容之后的数组
    if (oldTab != null) {  //如果旧的数组不为空，那么还需要将旧的数组中所有元素全部搬到新的里面去
      	...   //详细过程就不介绍了
    }
}
```

##### TreeMap

TreeMap是一种能够按照某种规则对插入元素进行排序的一种Map集合，在创建集合时给其一定的排序规则，这样插入进去的元素都是按规则排好序的。

```java
public static void main(String[] args) {
    Map<Integer , String> map = new TreeMap<>((a, b) -> b - a);
    map.put(0, "单走");
    map.put(1, "一个六");
    map.put(3, "**");
    System.out.println(map);
}
```

线程安全的map：

- HashTable， 基于synchronized同步锁实现，效率低

- ConcurretHashMap：基于CAS 和分段锁

  1.7及以前都是采用分段锁机制来提高并发性能，每个segment加一个同步锁，不同segment之间不受影响

  1.8以后采用了更细的锁粒度，链表的每个bucket桶加一把锁（即数组的每一个节点都有一把锁，当对不同节点的链表进行写操作时互不影响。）

#### Set集合

Set集合的顶层接口也是Collection，下面看一下其中怎么定义的set相关的方法：

```java
public interface Set<E> extends Collection<E> {
    // Set集合中基本都是从Collection直接继承过来的方法，只不过对这些方法有更加特殊的定义
    int size();
    boolean isEmpty();
    boolean contains(Object o);
    Iterator<E> iterator();
    Object[] toArray();
    <T> T[] toArray(T[] a);

    //添加元素只有在当前Set集合中不存在此元素时才会成功，如果插入重复元素，那么会失败
    boolean add(E e);

    //这个同样是删除指定元素
    boolean remove(Object o);
    //判断是否包含参数集合内的所有元素
    boolean containsAll(Collection<?> c);

    //同样是只能插入那些不重复的元素
    boolean addAll(Collection<? extends E> c);
  
    boolean retainAll(Collection<?> c);
    boolean removeAll(Collection<?> c);
    void clear();
    boolean equals(Object o);
    int hashCode();

    //这个方法我们同样会放到多线程中进行介绍
    @Override
    default Spliterator<E> spliterator() {
        return Spliterators.spliterator(this, Spliterator.DISTINCT);
    }
}
```

可以看出，set集合不允许有重复的元素出现，没有下标，不支持随机访问。

##### HashSet

在看HashSet之前建议先去看Map集合（HashMap），因为在HashSet主要就是依靠HashMap来实现，在HashSet中维护了一个HashMap集合，相当于我们往HashSet中存储的元素都是存在HashMap的key上，而Value会默认存一个Object对象，相当于一个占位符，并没有其他作用。(通过下面的代码可以看出，其实大部分操作还是调用的其内部HashMap的方法，HashSet就是套壳HashMap)

```java
public class HashSet<E>
    extends AbstractSet<E>
    implements Set<E>, Cloneable, java.io.Serializable
{

    private transient HashMap<E,Object> map;   //对，你没看错，底层直接用map来做事

    // 因为Set只需要存储Key就行了，所以说这个对象当做每一个键值对的共享Value
    private static final Object PRESENT = new Object();

    //直接构造一个默认大小为16负载因子0.75的HashMap
    public HashSet() {
        map = new HashMap<>();
    }
		
  	...
      
    //你会发现所有的方法全是替身攻击
    public Iterator<E> iterator() {
        return map.keySet().iterator();
    }

    public int size() {
        return map.size();
    }

    public boolean isEmpty() {
        return map.isEmpty();
    }
}
```



### 常用日期时间类

##### Calendar 日期类

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

##### SimpleDateFormat类

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

### Java对象

由三部分组成，对象头、对象实例数据和对齐填充

对象头：又包括 Mark word和Klass word

- Mark Word 存储对象的hashCode 、锁信息或分代信息或GC标志等
- Klass Word：存储对象指向所属类的指针，JVM以此来判别对象所属的类

对象实例数据：对象成员变量的数据

![image-20240801095407116](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240801095407116.png)

synchronized锁



### 线程

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

##### 多线程

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

###### CAS算法：

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



#### 线程池

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

### JVM

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

### 泛型

泛型在数据类型不确定时有重要作用，例如一个商品类Goods中定义了一个value属性（假设这个value有时候是String类型，有时候是int类型，如果我们直接给value定义成int类型则当下一个String类型的值传来时就会报错，之前为了解决这种问题，一般会将value定义成Object类，Object是所有类的父类所以任何类型都能存储）：

```java
//未改动之前写法
class Goods{
    private String name;
    private int value;
}
//改为Object类型的写法
//但是这种写法在使用时，每次取value值时都需要进行类型转换，
class Goods{
    private String name;
    private Object value;
}
Goods good=new Goods();
good.setValue("热销产品");
String value=(String)good.getValue();
// 在进行类型转换时就可能会出现类型转换异常，所以在jdk5的时候就推出了泛型
```

JDK5以后出现的泛型能够很好的解决上述代码中的每次取值都需要进行类型转换的问题，因为在编译时期就会对类型进行检查。

#### 如何使用泛型

泛型就是在定义时不指定固定的类型，在使用时再去根据需要来指定类型，如下代码是一个使用泛型的例子

##### 泛型类

```java
public class Score<T> {   //泛型类需要使用<>，我们需要在里面添加1 - N个类型变量
    String name;
    String id;
    T value;   //T会根据使用时提供的类型自动变成对应类型

    public Score(String name, String id, T value) {   //这里T可以是任何类型，但是一旦确定，那么就不能修改了
        this.name = name;
        this.id = id;
        this.value = value;
    }
}
```

使用时指定类型

```java
Score score=new Score<String>("张三","22","指定类型");
//假设在类中已经写过了get方法
//不需要再进行类型转换，取出的值直接就是我们定义时指定的类型
String value=score.getValue();
```

在使用泛型时不仅仅可以使用一个泛型，而是可以使用多个，例如：

```java
Class Test<A,B,C>{
    private A name;
    private B age;
    private C address;
}
//使用时指定类型
Test test=new Test<String,Integer,String>();
```

> 使用泛型时需要注意，指定泛型的类型时，只能用对象类型，不能用基本数据类型，例如Integer，Long，Double等。

##### 泛型方法

出了类上可以使用泛型外，方法也可以使用泛型

```java
public static <T> T test(T param);
private <T> void add(T[] arr, T t){
    arr[0] = t;
}
public static void main(String[] args) {
    String[] strings = new String[1];
    Main main = new Main();
    main.add(strings, "Hello");
    System.out.println(Arrays.toString(strings));
}
```

##### 泛型的界限

泛型的界限分为上界和下界，简而言之，上界就是泛型最大不能超过的类型，下界就是泛型最低不能小于的类型，通常用来对类型做一定的限制，extends关键字用来设定上限，super用来设定下限。

###### 上界

```java
public class Score<T extends Number> {   //设定类型参数上界，必须是Number或是Number的子类
    private final String name;
    private final String id;
    private final T value;

    public Score(String name, String id, T value) {
        this.name = name;
        this.id = id;
        this.value = value;
    }

    public T getValue() {
        return value;
    }
}
```

可以通过如下树的形式来做理解：

![image-20241009124755531](D:\TXT\图片文件\image-20241009124755531.png)

###### 下界

通过super关键字设定，但是如果我们设定了下界，那么他的上界最大还是能到达Object类型的，所以还是会出现最初类型转换的问题

```java
public class Score<T super Number> {   //设定类型参数下界，
    private final String name;
    private final String id;
    private final T value;

    public Score(String name, String id, T value) {
        this.name = name;
        this.id = id;
        this.value = value;
    }

    public T getValue() {
        return value;
    }
}
```

![image-20241009125137036](D:\TXT\图片文件\image-20241009125137036.png)

##### 类型擦除

[^https://blog.csdn.net/qq_43546676/article/details/128790980]:参考文献



意思是在我们定义泛型的地方，在代码编译的时候会将我们定义的泛型全都改为Object（如果给泛型指定了上界，那么在编译时会改为该上界的类型）

![image-20241009133531198](D:\TXT\图片文件\image-20241009133531198.png)



带上界的类型擦除

![image-20241009133606121](D:\TXT\图片文件\image-20241009133606121.png)

###### 类型擦除容易引发的问题

> [!NOTE]
>
> 先写这么些吧，等看完再捋捋再来写



### 函数式接口

### Optional

判空包装类，能够有效的处理空指针问题

首先Optional有三个静态方法：ifNullable(T value)、of(T value)、empty();下面来简单说下这三个方法

- ifNullable(T value)：创建一个可能为空的Optional对象，当value值不为null时，创建一个非空Optional对象。当value值为null时，会创建一个空Optional对象。
- of(T value):创建一个非空的Optional对象，如果value为空则会报错
- empty()：创建一个空Optional对象

Optional的其他API

get():获取Optional包装的值

orElse(T value):如果值存在则返回Optional值，如果Optional的值不存在则获取OrElse的默认值

orElseGet()：和OrElse类似

isPresent(): 返回布尔类型的值，有值就为true，否则为false

ifPresent(Consumer<? super T> consumer):如果Optional有值，则进行括号里的操作否则不进行

