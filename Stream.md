Stream Java8引入的新特性，能够对集合和数组进行操作

流的分类

1. 顺序流

   是一种单线程的流，按照数据流的顺序来处理数据，每个元素的处理都必须等待上一个元素处理完才能进行处理

2. 并行流

   是一种多线程的流，可以将数据分成多个部分并行处理，每个线程处理一个部分，提高效率

顺序流适用于数据量不大的时候，能够保证处理顺序和数据一致性。而并行流速度快，适用于数据量较大的情况，但并行流需要注意线程安全和一致性的问题。



流的使用

1. 获取顺序流

   集合类的stream()方法，Arrays的stream()方法，Stream的of()方法或iterate()方法或generate()方法

2. 获取并行流

   可以使用集合类的parallelStream()方法、Stream类的of()方法的parallel()方法、Stream类的iterate()方法的parallel()方法、Stream类的generate()方法的parallel()方法等来获取并行流。

获取流

```java
List<String> list = new ArrayList<>();
        list.add("avv");
        list.add("avv");
        list.add("aqq");
        list.add("b");
        list.add("c");
        list.add("asd");
		//集合类的stream()方法
        list.stream().filter(x->x.startsWith("a")).forEach(x-> System.out.println(x));
		//Arrays的stream()方法
        Stream<Object> stream = Arrays.stream(list.toArray());
        stream.forEach(a->System.out.println(a));
        System.out.println("----------------");
		//Stream的of()方法
        Stream<List<String>> list1 = Stream.of(list);
        list1.forEach(a->System.out.println(a));
```



流的常用方法

> filter(Predicate<T> predicate)：过滤流中的元素，只保留符合条件的元素。
> map(Function<T, R> mapper)：将流中的元素按照指定的方式进行映射，返回一个新的流。
> flatMap(Function<T, Stream<R>> mapper)：将流中的元素按照指定的方式进行映射，然后将所有映射结果合并成一个流。
> limit(long maxSize)：截取流中的前maxSize个元素。
> skip(long n)：跳过流中的前n个元素。
> sorted()：对流中的元素进行排序。
> distinct()：去重，去除流中重复的元素。
> forEach(Consumer<T> action)：对流中的每个元素执行指定的操作。
> reduce(T identity, BinaryOperator<T> accumulator)：将流中的元素按照指定的方式进行累加，返回一个Optional对象。
> collect(Collector<T, A, R> collector)：将流中的元素收集到一个集合中。
> concat 合并a和b两个流为一个流。