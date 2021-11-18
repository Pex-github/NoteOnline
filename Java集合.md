![image-20210524151119781](C:\Users\PEX\AppData\Roaming\Typora\typora-user-images\image-20210524151119781.png)



### List，Set，Map

List (对付顺序的好帮手)： 存储的元素是有序的、可重复的。

Set (注重独一无二的性质): 存储的元素是无序的、不可重复的。

Map (用 Key 来搜索的专家): 使用键值对（kye-value）存储，Key 是无序的、不可重复的，value是无序的、可重复的，每个键最多映射到一个值。

List , set 都是继承自collection接口，map不是

Set和List对比	Set检索元素效率低下，删除和插入效率高，插入和删除不会引起元素位置改变。List:和数组类似，List可以动态增长，查找元素效率高，插入删除元素效率低，因为会引起其他元素位置改变

set 特点∶元素无放入顺序，元素不可重复，**重复元素会覆盖掉**，(元素虽然无放入顺序，但是元素在set中的**位置是有该元素的 Hashcode决定**的，其位置其实是固定的，加入Set的object**必须定义equals ()方法**，另外list支持for循环，也就是通过**下标来遍历**，也可以用**迭代器**，但是set**只能用迭代**，因为他无序，无法用下标来取得想要的值。）



### Collection 和 Collections 的区别？

Collection 是一个接口，它是 Set、List 等容器的父接口；Collections 是个一个 工具类，提供了一系列的静态方法来辅助容器操作，这些方法包括对容器的搜索、 排序、线程安全化等等。

### ArrayList与LinkedList区别

都不同步，都不保存线程安全

Arraylist 底层使用的是 Object 数组； LinkedList 底层使用的是 双向链表 数据结构		ArrayList查找较快，插⼊、删除较慢，LinkedList查找较慢，插⼊、删除较快。

arraylist插入删除会影响其他元素的位置，LinkedList不会影响

ArrayList支持通过数组 下标快速获取元素对象，LinkedList不支持快速随机访问。前者有RandomAccess标识

ArrayList空间浪费体现在列表结尾会有空内存，LinkedList体现在每个元素都消耗更多的空间

ArrayList：利⽤数组的下标进⾏元素的访问，因此对元素的随机访问速度⾮常快。因为是 数组，所以ArrayList在初始化的时候，有初始⼤⼩10，插⼊新元素的时候，会判断是否需要扩容，扩容的步⻓是0.5倍原容量， 扩容⽅式是利⽤数组的复制，因此有⼀定的开销。

LinkedList：有⼀个内部类node作为存放元素的单元，⾥⾯有三个属性，⽤来存放 元素本身以及前后2个单元的引⽤	1.6是双向循环链表，有一个header属性，头和尾都会指向header，1.7之后不是了，删除header

### ArrayList 和 Array 有什么区别?

Array 可以容纳基本类型和对象，而 ArrayList 只能容纳对象。 

Array 是指定大小的，而 ArrayList 大小是固定的，ArrayLsit默认大小是10个元素

### 双向链表的指针

包含两个指针，一个 prev 指向前一个节点，一个 next 指向后一个节点

### 知道RandomAccess接口吗？

一个标识，标识实现这个接口的类具有随机访问功能

在binarySearch（二分查找）方法中，数组列表和双向链表调用的方法不同，前者实现了Randdoom接口，因为数组支持随机访问功能，而链表需要遍历到特定的位置才能访问元素

### ArrayList和Vector（向量）的区别，为什么数组可以取代向量

ArrayList相比于Vector，虽然线程不安全，但效率高，适用于频繁的查找工作，是List的主要实现类，而Vector是老版本遗留实现类，底层都是Object数组存储

### 说一说ArrayList的扩容机制吧

ArrayList支持根据需求而动态增长数组

Explicit(明确的)，Capacity( 容量)

calculate(计算)

①ArrayList扩容发生在add()方法调用的时候， 调用ensureCapacityInternal()来扩容的，这个方法里有判断是否需要扩容和计算扩容的长度

//通过方法calculateCapacity()获取需要扩容的长度:

//②ensureExplicitCapacity方法可以判断是否需要扩容：

③ArrayList扩容的关键方法grow():获取到ArrayList中elementData数组的内存空间长度 扩容至原来的1.5倍，超出数组hugeCapacity判断，就抛出内存溢出错误

④调用Arrays.copyOf方法将elementData数组指向新的内存空间时newCapacity的连续空间

本质就是计算出新的扩容数组的size，然后实例化，并将原有数组内容复制到新数组中去。**最好在 add 大量元素之前用** `ensureCapacity` **方法，以减少增量重新分配的次数，加快效率**

### HashMap和HashTable的区别

Hashmap非线程安全，HashTable线程安全（内部都是同步方法）或者要求线程安全的话，使用ConcurrentHashMap，因为HashTable基本被淘汰（效率低下且继承抽象类Dictionary被废弃），而且HashMap效率高（实现了AbstractMap）；

HashMap只允许一个空值键而且空键总是存储在table数组的第一个节点上，空值value可以允许多个，HashTable不允许；

HashTable初始默认大小为11，扩充倍率为2n+1；HashMap初始默认大小为16，扩充为原来的2倍（所以HashMap的大小总是2的幂次方，用了或和无符号右移）

Hashtable计算hash是直接使用key的hashcode对数组的长度直接进行取模，HashMap计算hash对key的hashcode进行了二次hash，然后对table数组长度取摸

### HashMap和HashSet区别

HashSet底层是基于HashMap实现的，实现了Set接口，有别于HashMap实现的Map接口，Set只能存储对象，调用方法是add而不是put，Set使用成员对象计算hashcode，当code相等时，使用equals方法判断对象是否相等

### HashSet是如何检查重复的

当有对象加入到Set中，会先计算hashcode判断加入的位置，code不存在，则加入，存在则equals检查对象是否相等，相等则不加入，其中add方法使用 HashMap的add方法，由于HashMap的key唯一，添加的是HM的key，所以不会重复

### HashMap的底层是如何实现的

| 1.8之前，底层是链表加数组，hashmap通过key的hashcode进行扰动运算得到hash值，与数组长度进行取余，若存放的位置有元素，就判断hash和key是否相同，若冲突就采用拉链法解决冲突，也就是链表数组，数组的每一格都是链表，将冲突的元素存放在链表中。 |
| ------------------------------------------------------------ |
| **1.8之后，底层是数组链表+红黑树，当链表长度大于8时，会判断数组的长度是否小于64，若是就数组扩容，若不是就链表转换为红黑树。来减少搜索时间** |

结构 是数组+链表，大小是2的幂次方的数组【默认16】每个位置存储一个Entry，Entry是静态内部类，Entry指向下一个Entry形成链表，每个键值对包装在一个Entry类中，Entry类还包含了hash值和next指针，连接下一个Entry实体，在0到15的数组中，存在哪一行位置取决于元素Key的哈希值对数组长度的取模，链表要插入多个元素的时候，判断hash和key是否相同，不同采用拉链法解决冲突，数组中存储的是最后插入的元素，这叫首插，1.8之后是尾插，应对红黑树带来的变化

拉链法：创建一个链表数组，数组中每一格就是一个链表。若遇到哈希冲突，则将冲突的值加到链表中即可

1.8版本带来的改变：【HashMap采用数组 + 链表 + 红黑树】当链表长度达到阈值8时（如果数组长度小于64，会优先选择数组扩容，而不是转换为红黑树），链表转化成红黑树，提升性能，减少搜索时间。

### 为什么HashMap的长度总是2的幂次方

| 因为长度是2的幂次方可以增加确定位置的运算效率，当我们想要将数据均匀分配时，散列值不能太大，所以要对数组的长度进行取模运算，得到的余数来决定位置的下标，有一个公式可以提升取模运算的效率，那就是按位与，但使用按位与的条件就必须要求长度是2的幂次方 |
| ------------------------------------------------------------ |

为了让HashMap存取高效，使数据均匀分配。散列值的范围太大不能直接使用，所以数组的下标要对数组长度取模运算，得到的余数决定存放位置的下标。计算公式：(length-1)&hash 这个公式可以等价于hash%length

### HashMap是线程安全的吗，为什么不是，画图说明

 多线程情况下，线程A在判断table[i]==null，确定可以插入的时候阻塞了。后面一个线程 恰好哈希吗相同，在线程A判断的位置插入了一条 数据。线程A就会将前面 的数据覆盖。

在链表上，若两个线程都遍历到最后一个节点，都要添加一个数据，后面添加的线程就会把前面添加的数据覆盖。

| 在版本1.7的时候链表插入元素采用的头插法会将链表顺序翻转造成死循环和数据丢失，1.8版本在PUT操作的时候会发生数据覆盖【在数组上判断为空时，有其他线程插进来，插进来的数据会被覆盖】【在链表的最后一个节点，都要插入数据，也会发生覆盖】 |
| ------------------------------------------------------------ |

![hashmap插入方法](C:\Users\PEX\Desktop\image\hashmap插入方法.png)

### HashMap的扩容过程了解吗

​		当向容器添加元素的时候，会判断当前容器的元素个数，如果大于等于阈值（数组的长度乘以负载因子的值【0.75】），就要自动扩容啦。
​		扩容( resize )就是重新计算容量，**本质是使用一个新的数组代替已有的容量小的数组。**如果数组中的一个格子不超过8，使用的是链表，若超过8，就会调用treeifbin函数，将链表转换为红黑树。

​		扩容 是一个特别消耗性能的操作，最好估算map的大小，初始化一个大致的值，避免频繁扩容。红黑树大程度优化了HashMap，生产中的key一般String，注意key的唯一性

### ConcurrentHashMap和Hashtable的区别

前者的底层数据结构和HashMap一样都是数组+链表/红黑二叉树

后者的底层数据结构是数组+链表，链表主要是解决哈希冲突而存在的

（**重要**）实现线程安全的方式：

①在 JDK1.7 的时候，ConcurrentHashMap （分段锁）对整个桶数组进行了分割分段( Segment )，每段有多个HashEntry，**每⼀把锁只锁容器其中⼀部分数据**，多线程访问容器里不同数据段的数据，就不会存在锁竞争，提高并发访问率

②1.8之后，摒弃了 Segment 的概念，而是直接用 Node数组+单向链表+红黑树的数据结构来实现，node<k,v>保存键值对数据，不过node只用于链表情况 ，红黑树是TreeNode，并发控制使用 synchronized 和 CAS 来操作	【静态内部类实现了Entry】

Node只能用于链表情况，(链表达到一定程度时转换)红黑树需要使用TreeNode【静态final类继承了Node】

transfer是扩容方法

TreeBin是封装TreeNode的容器，提供了转换红黑树的条件和锁的控制。

treeifyBin方法尝试将链表转换为树

**存放数据（put）**

没有初始化，首先初始化inittable，

计算hash确定位置。hash没有冲突，就CAS插入，hash冲突且没有在扩容 则通过synchronized加锁添加操作。

判断节点位置是链表还是树，若是链表，则 遍历链表，取出key比较，相同且 key的hash相同则覆盖，不同则尾插。若是树则调用putTreeVal方法添加到树中

添加完成，调用addCount，统计该数组位置节点大小，若达8个以上调用treeifyBin尝试转化为树，或扩容

链表长度达8，优先会判断数组 长度 是否大于64，小于则扩容数组，大于则转换为树。

注意：转换为树后，调用remove操作使得size小于8，树也不会逆转为链表

**取出**数据，通过计算hash确定在数组 的哪个位置，然后通过遍历链表或树判断key和hash，取出value值

![concurrentHashMap结构](C:\Users\PEX\Desktop\image\concurrentHashMap结构.png)

③Hashtable使用的是同一把锁，synchronized保证线程安全，效率非常低下。

### ConcurrentHashMap线程安全的具体实现方式/底层具体实现

1.7版本，将数据分成一段一段存储，每段配把锁，由Segment和 HashEntry组成，一个Segment包含一个HashEntry数组，对HashEntry修改时，必须获得对应Segment的锁

1.8版本，数组结构类似HashMap，超过链表的阈值转换为红黑树 ，CAS和synchronized保证并发安全，同步锁 只锁定当前链表或 红黑二叉树的首节点

读操作（get）没有使用同步机制，也没有使用unsafe，所以是并发操作

扩容时可以对数组进行读写操作

### 比较HashSet、LinkedHashSet和TreeSet三者的异同

HashSet是Set的主要实现类，HashSet的底层是HashMap，线程不安全，可以存储null

LinkedHashSet 是 HashSet 的⼦类，能够按照添加的顺序遍历； TreeSet 底层使⽤红⿊树，能够按照添加元素的顺序进⾏遍历，排序的⽅式有⾃然排序和定制排序。

### 如何选择集合 

存储键值对选用HashMap，需要排序选择TreeMap，保证线程安全可以选用ConcurrentHashMap

只存放元素值，就选择实现了Collection接口的集合，保证元素唯一的话，选择实现Set接口的集合，比如TreeSet或 HashSet，不需要就选择实现List接口的集合，比如ArrayList或LinkedList，然后根据实现需要和各集合的特点选择



### 集合删除细节

 List底层为数组

,删除时数组元素下标会被改变 

1. 跌代器调⽤.next()⽅法时,会检测是否有被修改过 
2. 如果要删除集合中的元素⼀定要⽤跌代器的remove()⽅法.



### Comparator 和 Comparable 的区别? 

Comparable 接口用于定义对象的自然顺序，而 comparator 通常用于定义用户定制的顺序。 Comparable 总是只有一个，但是可以有多个 comparator 来定义对象的顺序。

### 如何实现集合排序?

你可以使用有序集合，如 TreeSet 或 TreeMap，你也可以使用有顺序的的集合，如 list，然 后通过 Collections.sort() 来排序

### 集合中的快速失败机制 

一种错误检测机制，多线程情况下，，某个线程对集合的结构上进行改变操作，就有可能发生fail-fast

线程 1用迭代器遍历集合A的元素 ，这时线程2修改了集合结构，不是修改了集合内的元素，这时就会触发异常，这就是快速失败机制

原因：迭代器在遍历中，有一个属性modCount，当集合在遍历时被修改，就会改变modCount值。遍历方法next遍历到下一个元素前，就会检测modCount变量是否为期望值 ，是就遍历，不是就抛出异常

解决：遍历所有涉及modCount值得地方都加上synchronized，或者用CopyOnWriteArrayList替换ArrayList

Java一般不允许一个线程在遍历的时候，另一个线程修改它

还有**安全失败机制**

concurrent包下的类都是 安全失败，迭代器不会抛出异常，因为：他是基于底层集合做拷贝，不会使源集合受到印象。util包下的集合都是快速失败，迭代器会抛出异常

### 迭代器相关面试题

##### Iterator和ListIterator区别

前者可以 遍历set和list集合，后者 只能遍历list

前者单向遍历，后者双向遍历

后者实现了前者的接口，添加了额外的功能，比如添加，替换元素 ，获取索引位置



### 生产细节：

##### **创建只读集合方法**

![image-20210822135757793](C:\Users\PEX\AppData\Roaming\Typora\typora-user-images\image-20210822135757793.png)

##### 如何边遍历边删除

删除数组元素 会 改变数组下标，易发生异常

唯一操作 就是在iterator.remove();时使用iterator.next();

```java
for(iterator.hasNext()){
    iterator.remove();
}
```

##### array和list的互相转换

array转list，使用Arrays.asList(array)

list转array，list.toArray();