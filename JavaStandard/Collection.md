# 集合的处理

1. 关于 hashCode 和 equals 的处理，遵循如下规则：

  * 只要重写 equals，就必须重写 hashCode。
  * 因为 Set 存储的是不重复的对象，依据 hashCode 和 equals 进行判断，所以 Set 存储的对象必须重写这两个方法。
  * 如果自定义对象作为 Map 的键，那么必须重写 hashCode 和 equals。

2. ArrayList的subList结果不可强转成ArrayList，否则会抛出ClassCastException异常，即 java.util.RandomAccessSubList cannot be cast to java.util.ArrayList.
3. 在 subList 场景中，高度注意对原集合元素个数的修改，会导致子列表的遍历、增加、删除均会产生ConcurrentModificationException 异常。
4. 使用集合转数组的方法，必须使用集合的 toArray(T[] array)，传入的是类型完全一样的数组，大小就是 list.size()。
5. 使用工具类 Arrays.asList()把数组转换成集合时，不能使用其修改集合相关的方法，它的 add/remove/clear 方法会抛出 UnsupportedOperationException 异常。
6. 泛型通配符<? extends T>来接收返回的数据，此写法的泛型集合不能使用 add 方法，而<? super T>不能使用 get 方法，作为接口调用赋值时易出错。
7. 不要在 foreach 循环里进行元素的 remove/add 操作。remove 元素请使用 Iterator方式，如果并发操作，需要对 Iterator 对象加锁。
8. Comparator 要满足如下三个条件,必须要满足三个条件

  * x，y 的比较结果和 y，x 的比较结果相反。
  * x>y，y>z，则 x>z。
  * x=y，则 x，z 比较结果和 y，z 比较结果相同。

9. 集合初始化时，指定集合初始值大小
10. 使用 entrySet 遍历 Map 类集合 KV，而不是 keySet 方式进行遍历，如果是 JDK8，使用 Map.foreach 方法。
11. 合理利用好集合的有序性(sort)和稳定性(order)，避免集合的无序性(unsort)和不稳定性(unorder)带来的负面影响。
12. 利用 Set 元素唯一的特性，可以快速对一个集合进行去重操作，避免使用 List 的contains 方法进行遍历、对比、去重操作。
    ```
    有序性是指遍历的结果是按某种比较规则依次排列的。稳定性指集合每次遍历的元素次序是一定的。如：ArrayList 是 order/unsort；HashMap 是 unorder/unsort；TreeSet 是order/sort。
    ```

