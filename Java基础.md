[TOC]



### 1.解释下什么是面向对象？面向对象和面向过程的区别？

  “万物皆对象”的编程思想。在现实生活中的任何物体都可以归为一类事物，每一个个体都是一类事物的实例。面向对象的编程是以对象为中心，以消息为驱动。

区别：

 	1.   编程思路不同：面向过程以实现功能的函数开发为主，而面向对象要首先抽象出类、属性及其方法，然后通过实例化类、执行方法来完成功能。
 	2.   封装性：都具有封装性，但面向过程是封装功能，而面向对象封装的是数据和功能。
 	3.   具有继承和多态性

### 2.面向对象的三大特性？

1. 封装：把数据和操作数据的方法封装起来，对数据的访问只能通过已定义的接口。
2. 继承： 继承是从已有类得到继承信息创建新类的过程，父类的私有属性和构造方法并不能被继承。
3. 多态：分为编译时多态（方法重载）和运行时多态（方法重写）。
   - 重写：子类中把父类本身有的方法重新写一遍。方法名，参数列表，返回类型都相同的情况
   - 重载：同名的方法如果有不同的参数列表（参数类型不同、参数个数不同甚至参数顺序不同）则视为重载。

### 3.JDK、JRE、JVM三者之间的关系？

​	JDK（Java Development Kit）：是Java开发工具包，包括JRE、Java工具和Java基础类库

​	JRE（Java Runtime Environment）: 是Java的运行环境，包括JVM及Java核心类库	

​	JVM（Java Virtual Machine）: 是Java虚拟机，是整个Java实现跨平台的最核心部分。所有的Java程序会首先被编译为.class的类文件，这种类文件可以在虚拟机上执行。

### 4.Java中是否可以重写一个private或者static方法？

​	Java中static方法不能被覆盖，因为方法覆盖是基于运行时动态绑定的，而static方法是编译时静态绑定的。static方法跟类的任何实例都不相关，所以概念上不适用。可以被继承，但不能够被重写。子类存在同样名称和参数的静态方法，是隐藏了父类的方法，而不是重写。

​	Java中也不可以覆盖private的方法，因为private修饰的变量和方法只能在当前类中使用，如果是其他的类继承当前类时不能访问到private变量或方法的，当然也不能覆盖。

### 5.构造器是否可以被重写？

​	由继承就知 父类的私有属性和构造方法并不能被继承，所以不可被重写，可以重载。

### 6.构造方法有哪些特性？

 	1.  名字与类名相同
 	2.  没有返回值，但不能用void声明构造函数
 	3.  成类的对象时自动执行，无需调用

### 7.在Java中定义一个不做事且没有参数的构造方法有什么作用？

​	Java程序在执行子类的构造方法之前，如果没有用super（）来调用父类特定的构造方法，则会调用父类中“没有参数的构造方法”。

### 8. Java中创建对象的几种方式？

 1. 使用new关键字

 2. 使用Class类的newInstance方法，该方法调用无参的构造器创建对象（反射）:Class.forName.newInstance();

 3. 使用clone()；

 4. 反序列化

### 9.抽象类和接口有什么区别？

 	1.  抽象（abstract）类中可以定义构造函数，接口不能定义构造函数；
 	2.  抽象类中可以有抽象方法和具体方法，而接口只能有抽象方法（JDK1.8后允许在接口中包含带有具体实现的方法）；
 	3.  抽象类中的成员权限可以是 public、默认、protected（抽象类中抽象方法就是为了重写，所以不能被 private 修饰），而接口中的成员只可以是 public（方法默认：public abstrat、成员变量默认：public static final）；
 	4.  抽象类中可以包含静态方法，而接口中不可以包含静态方法；
 	5.  抽象类是对概念的抽象，接口则是对功能的抽象。

### 10. 静态变量和实例变量的区别？

​	静态变量：是被static修饰的变量，也被称为类变量，它属于类，因此不管创建多少个对象，静态变量在内存中有且仅有一个拷贝；静态变量可以实现让多个对象共享内存。

​	实例变量：属于某一实例 ，需要先创建对象，然后通过对象才能访问到它。

### 11.short s1 = 1；s1 = s1 + 1；有什么错？那么 short s1 = 1; s1 += 1；呢？有没有错误？

​	对于 short s1 = 1; s1 = s1 + 1; 来说，在 s1 + 1 运算时会自动提升表达式的类型为 int ，那么将 int 型值赋值给 short 型变量，s1 会出现类型转换错误。

​	对于 short s1 = 1; s1 += 1; 来说，+= 是 Java 语言规定的运算符，Java 编译器会对它进行特殊处理，因此可以正确编译。

### 12.Integer和int的区别?

 1. int是Java的八种基本属性类型之一，而Integer是Java为int类型提供的封装类；

 2. int型变量的默认值是0,Integer变量的默认值是null，这一点说明Integer可以区分出未赋值和值为0的区别。

 3. Integer变量必须实例化。

 4. 对于两个非 new 生成的 Integer 对象进行比较时，如果两个变量的值在区间 [-128, 127] 之间，则比较结果为 true，否则为 false。Java 在编译 Integer i = 100 时，会编译成 Integer i = Integer.valueOf(100)，而 Integer 类型的 valueOf 的源码如下所示：

    ```java
    public static Integer valueOf(int var0) {
        return var0 >= -128 && var0 <= Integer.IntegerCache.high ? Integer.IntegerCache.cache[var0 + 128] : new Integer(var0);
    }
    ```

    由此可见Java 对于 [-128, 127] 之间的数会进行缓存。

### 13.装箱和拆箱

​	自动装箱四Java编译器在基本数据类型和对应包装类之间做的一个转化，比如：把int转化成Integer,double转化成Double等等。反之就是自动拆箱。

### 14.switch语句能否作用在byte上，能否作用在long上，能否作用在String上？

 在 switch(expr 1) 中，expr1 只能是一个整数表达式或者枚举常量。可以是int基本数据类型或者Integer包装类型以及以下的。long不可以。可以作用在String上。

### 15.字节和字符的区别？

字节是存储容量的基本单位；

字符是数字、字母、汉字以及其他语言的各种符号；

1字节 = 8个二进制单位，一个字符由一个字节或者多个字节的二进制单位组成

### 16.String为什么要设计为不可变类？

 	1.  字符串常量池的需要：字符串常量池是Java堆内存中一个特殊的存储区域，当创建一个String对象时，假如此字符串值已经存在于常量池中，则不会创建一个新的对象，而是引用已经存在的对象；
 	2.  允许String对象缓存HashCode:Java中String对象的哈希吗被频繁地使用，比如在HashMap等容器中。字符串不变形保证了hash吗的唯一性，因此可以放心地进行缓存。不必每次都计算新的哈希
 	3.  String被许多的Java类用来当做参数。例如：网络连接地址URL等。

### 17.String、StringBuilder、StringBuffer的区别？

​	String:用于字符串操作，属于不可变类；【引用类型，底层用char数组实现】

```java
	private final char value[];
```

​	StringBuilder:与StringBuffer类似，都是字符串缓冲区，但线程不安全

​	StringBuffer：也用于字符串操作，不同之处是StringBuffer属于可变类，对方法加了同步锁，线程安全。

```java
	char[] value;
```

### 18.String 字符串修改实现的原理//TODO

​	当用String类型来对字符串进行修改时，首先创建一个StringBuffer,其次调用StringBuffer的append()方法，最后调用StringBuffer的toString()方法返回。反汇编可见：



### 19. String str = "i" 与String str = new String("i")一样吗？

​	不一样。因为内存的分配方式不一样。String str = "i"的方式，Java虚拟机会将其分配到常量池中；而String str = new String("i")则会被分到堆内存中。
​	

```java
public class StringTest {
    public static void main(String[] args) {
        String str1 = "abc";
        String str2 = "abc";
        String str3 = new String("abc");
        String str4 = new String("abc");
        
        System.out.println(str1 == str2); //true
        System.out.println(str1 == str3); //false
        System.out.println(str3 == str4); //false
        System.out.println(str3.equals(str4)); //true
    }
}
```

​	在执行String str1 = "abc"的时候，JVM会首先检查字符串常量池中是否已经存在该字符串对象，如果已经存在，那么就不会再创建了，直接返回该字符串在字符串常量池中的内存地址。如果不存在，则在字符串常量池创建返回。所以String str2 = "abc"直接返回内存地址。 栈内存中str1与str2的内存地址都指向“abc”在字符串常量池中的位置，所以为true。

​	而在执行 String  str3 = new  String("abc") 的时候，JVM 会首先检查字符串常量池中是否已经存在“abc”字符串，如果已经存在，则不会在字符串常量池中再创建了；如果不存在，则就会在字符串常量池中创建 "abc" 字符串对象，然后再到堆内存中再创建一份字符串对象，把字符串常量池中的 "abc" 字符串内容拷贝到内存中的字符串对象中，然后返回堆内存中该字符串的内存地址，即栈内存中存储的地址是堆内存中对象的内存地址。在内存中的存储状况如图所示：

​	![](C:\Users\何大洋\Desktop\微信图片_20191103180616.jpg)

### 20.String类的常用方法都有那些？

indexOf():返回指定字符的索引。

charAt()：返回指定索引处的字符。

replace()：字符串替换。

trim()：去除字符串两端空白。

split()：分割字符串，返回一个分割后的字符串数组。

getBeytes()：返回字符串的byte类型数组。

length()：返回字符串长度。	

toLowerCase()：将字符串转成小写字母。

toUpperCase()：将字符串转成大写字符。

substring()：截取字符串。

equals()：字符串比较。

### 21.final修饰StringBuffer后还可以append吗？

​	可以。final修饰的是一个引用变量，那么这个引用始终只能指向这个对象，但是这个对象内部的属性是可以变化的。

### 22.为什么wait/notify方法放在Object类中而不是Thread类中？

notify 方法：唤醒一个在此对象监视器上等待的线程。如果有多个线程在等待只会唤醒一个。

notifyAll 方法：作用跟 notify() 一样，只不过会唤醒在此对象监视器上等待的所有线程，而不是一个线程。

一个很明显的原因是 Java 提供的锁是对象级的而不是线程级的，每个对象都有锁，通过线程获得。

### 23.final、finally、finalize的区别？

final：用于声明属性、方法和类，分别表示属性不可变、方法不可覆盖、被其修饰的类不可基础；

finally：异常处理语句结构的一部分，表示总是执行。

finallize：Object类的一个方法，在垃圾回收时会调用被回收对象的finalize。

### 24.finally块中的代码什么时候被执行？

finally块里的代码在return之前执行的。如果try-finally中都有return，最终调用finally中return的值。

### 25.==和equals的区别？

==：用来比较的对象是基本数据类型，则比较值；如果是引用数据类型，则比较地址。

equals：用来比较两个对象的内容是否相等。没重写比较地址。不可比较基本数据类型。

### 26.为什么重写 equals() 就一定要重写 hashCode() 方法？

​	字段属性值完全相同的两个对象因为hashCode不同，所以在hashmap中的table数组的下标不同，从而这两个对象就会同时存在于集合中，所以重写equals()就一定要重写hashCode()方法。同理 如果仅仅重写了hashCode()，而没有重写equals（）方法 在hashmap中的table数组的下标相同  但是没有重写equals()方法导致认为不是一个key不会覆盖,将会形成一个链表。

Set为例子：

```java
public class A {
    @Override
    public boolean equals(Object obj) {
        return true;
    }
}
public class B {
    @Override
    public int hashCode() {
        return 1;
    }
}
public class C {
    @Override
    public int hashCode() {
        return 2;
    }
 
    @Override
    public boolean equals(Object obj) {
        return true;
    }
}
public class HashSetTest {
    public static void main(String[] args) {
        HashSet hashSet = new HashSet();
        hashSet.add(new A());
        hashSet.add(new A());
        hashSet.add(new B());
        hashSet.add(new B());
        hashSet.add(new C());
        hashSet.add(new C());
        for (Object hs : hashSet) {
            System.out.println(hs);
        }
        //HashSet重写了toString()方法
//        System.out.println(hashSet);
    }
}
cn.edu.uestc.collection.B@1
cn.edu.uestc.collection.B@1
cn.edu.uestc.collection.C@2
cn.edu.uestc.collection.A@3f84246a
cn.edu.uestc.collection.A@18a9fa9c
Process finished with exit code 0
```



### 27.泛型类型擦除

​	泛型是通过类型擦除来实现的，编译器在编译时擦除了所有类型相关的信息，所以在运行时不存在任何类型相关的信息。例如：List<String> 在运行时仅用一个 List 来表示。这样做的目的，是确保能和 Java 5 之前的版本开发二进制类库进行兼容。

​	List<String> list = new ArrayList<String>();

​	1、两个 String 其实只有第一个起作用，后面一个没什么用，只不过 JDK7 才开始支持 List<String>list = new ArrayList<> 这种写法。

​	2、第一个 String 就是告诉编译器，List 中存储的是 String 对象，也就是起类型检查的作用，之后编译器会擦除泛型占位符，以保证兼容以前的代码。

### 28.深克隆和浅克隆的区别？

（1） 浅克隆：拷贝对象和原始对象的引用类型引用同一个对象。浅克隆只是复制了对象的引用地址，两个对象指向同一个内存地址，所以修改其中任意的值，另一个值都会随之变化。

（2） 深克隆：拷贝对象和原始对象的引用类型引用不同对象。深拷贝是将对象及值复制过来，两个对象修改其中任意的值另一个值不会改变。

### 29.Java中的动态代理是什么？有哪些应用？

​	动态代理：当想要给实现了某个接口的类中的方法，加一些额外的处理。比如说加日志，加事务等。可以给这个类创建一个代理，顾名思义就是创建一个新的类，这个类不仅包含原来类方法的功能，而且还在原有的基础上添加了额外处理的新功能。这个代理类并不是定义好的，是动态生成的。具有解耦意义，灵活，扩展性强。

​	应用：AOP、加事务、加权限、加日志。







