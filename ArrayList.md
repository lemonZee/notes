## ArrayList

ArrayList 是一个动态数组，实现了 List 接口以及 list相关的所有方法，它允许所有元素的插入，包括 null。

**ArrayList 是非线程安全的。**

通过无参构造方法来创建 ArrayList 时，它的大小其实是为 0 的，只有在使用到的时候，才会通过 grow 方法去创建一个大小为 10 的数组。 

**时间复杂度**

第一个 **add** 方法的复杂度为 O(1)， 如果使用的是带指定下标的 add方法，则复杂度为 O(n)

**set** 方法 时间复杂度度为 O(1)。 

**remove** 方法 时间复杂度为 O(n)。 

**grow** 方法是在数组进行扩容的时候用到的，从中我们可以看见， ArrayList 每次扩容都是**扩 1.5 倍**，然后调用 Arrays 类的 copyOf 方法，把元素重新拷贝到一个新的数组中去  

**size** 方法时间复杂度为 O(1)，它是直接返回数组中元素的个数，并不是数组的实际大小 

**indexOf** 方法的作用是返回第一个等于给定元素的值的下标。它是通过遍历比较数组中每个元素的值来查找的，所以它的时间复杂度是 O(n)。 

**lastIndexOf** 的原理跟 indexOf 一样，而它仅仅是从后往前找起罢了。 



1、 ArrayList 创建时的大小为 0；当加入第一个元素时，进行第一次扩容时，默认容
量大小为 10。
2、 ArrayList 每次扩容都以当前数组大小的 1.5 倍去扩容。 



##### 和Array的区别

**如何实现数组和 List 之间的转换？**
List转换成为数组：调用ArrayList的toArray方法。
数组转换成为List：调用Arrays的asList方法。

**Array 和 ArrayList 有何区别？**
**Array可以容纳基本类型和对象，而ArrayList只能容纳对象。**
Array是指定大小的，而ArrayList初始大小是固定的。
Array没有提供ArrayList那么多功能，比如addAll、removeAll和iterator等。

##### 

## Vector

很多方法都跟 ArrayList 一样，只是多加了个 **synchronized** 来保证线程安全 。

3、 Vector 创建时的默认大小为 10。 

4、 Vector 每次扩容都以当前数组大小的 2 倍去扩容。当指定了 capacityIncrement 之
后，每次扩容仅在原先基础上增加 capacityIncrement 个单位空间。 



## LinkedList

LinkedList 是通过一个双向链表来实现的，它允许插入所有元素，包括 null，同时，它是**非线程安全**的。 

1、 LinkedList 的底层结构是一个带头/尾指针的双向链表，可以快速的对头/尾节点
进行操作。
2、相比数组，链表的特点就是在指定位置插入和删除元素的效率较高，但是查找的
效率就不如数组那么高了。 











## HashSet

##### 传统 HashMap 的缺点 

(1)JDK 1.8 以前 HashMap 的实现是 数组+链表，即使哈希函数取得再好，也很难达到元素
百分百均匀分布。
(2)当 HashMap 中有大量的元素都存放到同一个桶中时，这个桶下有一条长长的链表，这个
时候 HashMap 就相当于一个单链表，假如单链表有 n 个元素，遍历的时间复杂度就是 O(n)，
完全失去了它的优势。
(3)针对这种情况， JDK 1.8 中引入了红黑树（查找时间复杂度为 O(logn)）来优化这个问题 



**hashset特点**

1.存取无序的

2.键和值位置都可以是null，但是键位置只能是一个null

**属性**：

默认的初始容量为 16 

最大的容量上限为 2^30 

默认的负载因子为 0.75 

变成树型结构的临界值为 8 

恢复链式结构的临界值为 6 

threshold 是通过 capacity*load factor 计算出来的，当 size 到达这个值时，就会进行扩容操作 



**当哈希表的大小超过64，才会把链式结构转化成树型结构，否则仅采取扩容来尝试减少冲突 。**

这样做的目的是因为数组比较小，尽量避开红黑树结构，这种情况下变为红黑树结构，反而会降低效率，因为红黑树需要进行左旋，右旋，变色这些操作来保持平衡 。同时数组长度小于64时，搜索时间相对要快些。所以综上所述为了提高性能和减少搜索时间，底层在阈值大于8并且数组长度大于64时，链表才转换为红黑树。具体可以参考 `treeifyBin`方法。

##### 为什么链表长度大于8时转化为红黑树？

当hashCode离散性很好的时候，树型bin用到的概率非常小，因为数据均匀分布在每个bin中，几乎不会有bin中链表长度会达到阈值。但是在随机hashCode下，离散性可能会变差，然而JDK又不能阻止用户实现这种不好的hash算法，因此就可能导致不均匀的数据分布。不过理想情况下随机hashCode算法下所有bin中节点的分布频率会遵循泊松分布，我们可以看到，一个bin中链表长度达到8个元素的概率为0.00000006，几乎是不可能事件。所以，之所以选择8，不是随便决定的，而是根据概率统计决定的。由此可见，发展将近30年的Java每一项改动和优化都是非常严谨和科学的。 

也就是说：选择8因为符合泊松分布，超过8的时候，概率已经非常小了，所以我们选择8这个数字。

**get方法实现步骤大致如下：**
1、通过 hash 值获取该 key 映射到的桶。
2、桶上的 key 就是要查找的 key，则直接命中。
3、桶上的 key 不是要查找的 key，则查看后续节点：
（1）如果后续节点是树节点，通过调用树的方法查找该 key。
（2）如果后续节点是链式节点，则通过循环遍历链查找该 key。 

**put 方法实现步骤大致如下：**
1、先通过 hash 值计算出 key 映射到哪个桶。
2、如果桶上没有碰撞冲突，则直接插入。
3、如果出现碰撞冲突了，则需要处理冲突：
（1）如果该桶使用红黑树处理冲突，则调用红黑树的方法插入。
（2）否则采用传统的链式方法插入。如果链的长度到达临界值，则把链转变为红
黑树。
4、如果桶中存在重复的键，则为该键替换新值。
5、如果 size 大于阈值，则进行扩容。 

在 get 方法和 put 方法中都需要先计算 **key 映射到哪个桶上**，然后才进行之后的操作，
计算的主要代码如下： **(n - 1) & hash** 

—— n 指的是哈希表的大小，

—— hash 指的是 key 的哈希值，每个key的hash值是不会变的， hash 是采用了二次哈希的方式，其中 key 的 hashCode 方法是一个native 方法 

```java
static final int hash(Object key) {
int h;
return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>>16);
```



![1607564034248](.\images\1607564034248.png)

##### 为什么要(h = key.hashCode()) ^ (h >>>16)？

因为如果当 n 很小，假设为 64 的话，那么 n-1即为 63（0x111111），这样的值跟 hashCode()直接做与操作，实际上只使用了哈希值的后 6 位。如果当哈希值的高位变化很大，低位变化很小，这样就很容易造成冲
突了，所以这里把高低位都利用起来，从而解决了这个问题。 

正是因为与的这个操作，决定了 HashMap 的大小只能是 2 的幂次方，即使你在创建 HashMap 的时候指定了初始大小，HashMap 在构建的时候也会调用下面这个方法来调整大小：例如， 16 还是 16， 13 就会调整为 16， 17 就会调整为 32。  

HashMap 容量为2次幂的原因，就是为了数据的的均匀分布，减少hash冲突，毕竟hash冲突越大，代表数组中一个链的长度越大，这样的话会降低hashmap的性能



##### 扩容

HashMap 在进行扩容时，使用的 **rehash**方式非常巧妙，因为每次扩容都是翻倍，与原来计算（n-1） &hash 的结果相比，只是多了一个 bit 位，所以**节点要么就在原来的位置，要么就被分配到“原位置+旧容量”这个位置**。

**例如，**原来的容量为 32，那么应该拿 hash 跟 31（0x11111）做与操作；在扩容扩到了 64 的容量之后，应该拿 hash 跟 63（0x111111）做与操作。新容量跟原来相比只是多了一个 bit 位，假设原来的位置在 23，那么当新增的那个 bit 位的计算结果为 0时，那么该节点还是在 23；相反，计算结果为 1 时，则该节点会被分配到 23+31 的
桶上。
正是因为这样巧妙的 rehash 方式，保证了 rehash 之后每个桶上的节点数必定小于等于原来桶上的节点数，即保证了 rehash 之后不会出现更严重的冲突。 

哈希表中的键值对个数超过阈值时，才进行扩容的 

按照原来的拉链法来解决冲突，如果一个桶上的冲突很严重的话，是会导致哈希表的效率降低至 O（n），而通过红黑树的方式，可以把效率改进至 O（logn）。相比链式结构的节点，树型结构的节点会占用比较多的空间，所以这是一种以**空间换时间**的改进方式。 



##### HashMap中容量的初始化

当我们使用HashMap(int initialCapacity)来初始化容量的时候，jdk会默认帮我们计算一个相对合理的值当做初始容量。那么，是不是我们只需要把已知的HashMap中即将存放的元素个数直接传给initialCapacity就可以了呢？

关于这个值的设置，在《阿里巴巴Java开发手册》有以下建议：

##### ![image-20191117165438726](F:/Java%E6%95%99%E7%A8%8B/Java8%E9%9B%86%E5%90%88%E6%A1%86%E6%9E%B6/Java8%E9%9B%86%E5%90%88%E6%A1%86%E6%9E%B6/hashmap/%E7%AC%94%E8%AE%B0/img/image-20191117165438726.png)

也就是说，如果我们设置的默认值是7，经过Jdk处理之后，会被设置成8，但是，这个HashMap在元素个数达到 8*0.75 = 6的时候就会进行一次扩容，这明显是我们不希望见到的。我们应该尽量减少扩容。原因也已经分析过。

如果我们通过**initialCapacity/ 0.75F + 1.0F**计算，7/0.75 + 1 = 10 ,10经过Jdk处理之后，会被设置成16，这就大大的减少了扩容的几率。

当HashMap内部维护的哈希表的容量达到75%时（默认情况下），会触发rehash，而rehash的过程是比较耗费时间的。所以初始化容量要设置成**initialCapacity/0.75 + 1**的话，可以有效的减少冲突也可以减小误差。

所以，我可以认为，当我们明确知道HashMap中元素的个数的时候，把默认容量设置成**initialCapacity/ 0.75F + 1.0F**是一个在性能上相对好的选择，但是，同时也会牺牲些内存。

我们想要在代码中创建一个HashMap的时候，如果我们已知这个Map中即将存放的元素个数，给HashMap设置初始容量可以在一定程度上提升效率。

但是，JDK并不会直接拿用户传进来的数字当做默认容量，而是会进行一番运算，最终得到一个2的幂。原因也已经分析过。

但是，为了最大程度的避免扩容带来的性能消耗，我们建议可以把默认容量的数字设置成**initialCapacity/ 0.75F + 1.0F**。











## LinkedHashMap

LinkedHashMap继承了HashMap



LinkedHashMap**有自己的静态内部类Entry**，它继承了HashMap.Entry，定义如下

```java
/**
	这是HashMap Entry
*/
static class Entry<K,V> implements Map.Entry<K,V> {
        final K key;
        V value;
        Entry<K,V> next;
        int hash;



/**
     * LinkedHashMap entry.
     */
    private static class Entry<K,V> extends HashMap.Entry<K,V> {
        // These fields comprise the doubly linked list used for iteration.
        Entry<K,V> before, after;

        Entry(int hash, K key, V value, HashMap.Entry<K,V> next) {
            super(hash, key, value, next);
        }
```

所以LinkedHashMap构造函数，主要就是调用HashMap构造函数初始化了一个Entry[] table，然后调用自身的init初始化了一个只有头结点的双向链表

##### put方法

当put元素时，不但要把它加入到HashMap中去，还要加入到双向链表中，所以可以看出LinkedHashMap就是HashMap+双向链表，双向连表的header是一个Entry类型的双向链表表头，本身不存储数据。

##### 扩容

LinkedHashMap重写了transfer方法

LinkedHashMap扩容时，数据的再散列和HashMap是不一样的。

HashMap是先遍历旧table，再遍历旧table中每个元素的单向链表，取得Entry以后，重新计算hash值，然后存放到新table的对应位置。

LinkedHashMap是**遍历的双向链表**，取得每一个Entry，然后重新计算hash值，然后存放到新table的对应位置。

从遍历的效率来说，遍历双向链表的效率要高于遍历table，因为遍历双向链表是N次（N为元素个数）；而遍历table是N+table的空余个数（N为元素个数）。

##### 双向链表的重排序

在LinkedHashMap中，**只有accessOrder为true**，即是访问顺序模式，才会**put或get时对更新的Entry进行重新排序**，而如果是插入顺序模式时，不会重新排序，这里的排序跟在HashMap中存储没有关系，只是指在双向链表中的顺序。

举个栗子：开始时，HashMap中有Entry1、Entry2、Entry3，并设置LinkedHashMap为访问顺序，则更新Entry1时，**会先把Entry1从双向链表中删除，然后再把Entry1加入到双向链表的表尾**，而Entry1在HashMap结构中的存储位置没有变化，

##### remove方法

LinkedHashMap没有提供remove方法，所以调用的是HashMap的remove方法，实现如下：

```csharp
    public V remove(Object key) {
        Entry<K,V> e = removeEntryForKey(key);
        return (e == null ? null : e.value);
    }

    final Entry<K,V> removeEntryForKey(Object key) {
        int hash = (key == null) ? 0 : hash(key);
        int i = indexFor(hash, table.length);
        Entry<K,V> prev = table[i];
        Entry<K,V> e = prev;

        while (e != null) {
            Entry<K,V> next = e.next;
            Object k;
            if (e.hash == hash &&
                ((k = e.key) == key || (key != null && key.equals(k)))) {
                modCount++;
                size--;
                if (prev == e)
                    table[i] = next;
                else
                    prev.next = next;
                // LinkedHashMap.Entry重写了该方法
                e.recordRemoval(this);
                return e;
            }
            prev = e;
            e = next;
        }

        return e;
    }
```

在上一篇HashMap中就分析了remove过程，其实就是断开其他对象对自己的引用。比如被删除Entry是在单向链表的表头，则让它的next放到表头，这样它就没有被引用了；如果不是在表头，它是被别的Entry的next引用着，这时候就让上一个Entry的next指向它自己的next，这样，它也就没被引用了。

在HashMap.Entry中recordRemoval方法是空实现，但是LinkedHashMap.Entry对其进行了重写，如下：

```csharp
        void recordRemoval(HashMap<K,V> m) {
            remove();
        }

        private void remove() {
            before.after = after;
            after.before = before;
        }
```

易知，这是要把双向链表中的Entry删除，也就是要断开当前要被删除的Entry被其他对象通过after和before的方式引用。

**所以，LinkedHashMap的remove操作。首先把它从table中删除，即断开table或者其他对象通过next对其引用，然后也要把它从双向链表中删除，断开其他对应通过after和before对其引用。**



##### 总结

1. LinkedHashMap是继承于HashMap，是基于HashMap和双向链表来实现的。
2. HashMap无序；LinkedHashMap有序，可分为插入顺序和访问顺序两种。如果是访问顺序，那put和get操作已存在的Entry时，都会把Entry移动到双向链表的表尾(其实是先删除再插入)。
3. LinkedHashMap存取数据，还是跟HashMap一样使用的Entry[]的方式，双向链表只是为了保证顺序。
4. LinkedHashMap是线程不安全的。





## HashTable

Hashtable 可以说已经具有一定的历史了，现在也很少使用到 Hashtable 了，更多的是使
用 HashMap 或 ConcurrentHashMap。 HashTable 是一个线程安全的哈希表，它通过使用
synchronized 关键字来对方法进行加锁，从而保证了线程安全。但这也导致了在单线程
环境中效率低下等问题。

 Hashtable 与 HashMap 不同，它不允许插入 null 值和 null 键。 