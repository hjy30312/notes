### 1.Java中常用的容器有哪些？

​	常见容器主要包括Collection和Map两种，Collection存储着对象的集合，而Map存储着键值对的映射表。

### 2.ArrayList和LinkedList的区别？

​	ArrayList: 底层是基于数组实现的，查找快，增删比较慢；

​	LinkedList: 底层是基于链表实现的。确切的说是循环双向链表，查找慢、增删快。

### 3. Array和ArrayList有何区别？什么时候更适合用Array?

	1. Array可以容纳基本类型和对象，而ArrayList只能容纳对象。
 	2. Array是指定大小的，而ArrayList大小是固定的。

### 4.HashMap的实现原理/底层数据结构？JDK1.7和JDK1.8

​	JDK1.7：Entry数组 + 链表

​	JDK1.8：Node数组 + 链表/红黑树，当链表上的元素个数超过8个并且数组长度》=64是自动转化成红黑树，节点变成树节点，以提高搜索效率和插入效率到O（logN）。Entry和Node都包含Key、value、hash、next属性。

### 5.HashMap的put方法的执行过程？

​	当我们想往一个HashMap中添加一对key-value时，系统首先会计算key的hash值，然后根据hash值确认在table中存储的位置。若该位置没有元素，则直接插入。否则迭代该处元素链表并以此比较其key的hash值。如果两个hash值相等且key值相等，则用新的Entry的value覆盖原来节点的value。如果两个hash值相等但key值不等，则将该节点插入该链表的链头。

	### 6.HashMap的get方法的执行过程？

​	通过key的hash值找到在table数组中的索引处的Entry，然后返回该key对应的value即可。

​	在这里能够根据key快速的取到value除了和HashMap的数据结构密不可分外，还和Entry有莫大的关系。HahsMap在存储过程中并没有将key,value分开来存储，而是当做一个整体key-value来处理的，这个整体就是Entry对象。同时value也只相当于key的附属而已。在存储的过程找那个，系统根据key的HashCode来决定Entry在table数组中的存储位置，在取的过程中同样根据key的HashCode取出相对应的Entry对象。

### 7.HashMap的resize方法的执行过程？ //todo

### 8.HashMap的size为什么必须是2的整数次方？

	1. 这样做总是能够保证HashMap的底层数组长度为2的n次方。当length为2的n次方时，h & (length - 1)就相当于对length取模，而且速度比直接去模快得多，这是HashMap在速度上的一个优化。并且每次扩容时都是翻倍。   h：为插入元素的hashcode      length：为map的容量大小

​	2.  

