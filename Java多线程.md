## Java多线程

### 1. Java线程实现/创建方式

#### 1.1 继承Thread类

​	Thread类本质上是实现了Runnable接口的一个实例，代表一个线程的实例。启动线程的唯一方法就是通过Thread类的start()实例方法。 **start()方法是一个native方法**，它将启动一个新线程，并执行run方法。

```java
/**
 * 1. 继承Thread类并重写run方法
 * 2. 创建线程对象
 * 3. 调用该线程对象的start()方法来启动线程
 */
public class MyThread extends Thread {

    @Override
    public void run() {
        System.out.println("MyThread.run()");
    }

    public static void main(String[] args) {
        MyThread myThread = new MyThread();
        myThread.start();
    }
}

```



#### 1.2 实现Runnable接口

如果自己的类已经extends另一个类，就无法直接extends Thread，此时可以实现一个Runnable接口。

```java
/**
 * 1. 定义一个类实现Runnable接口，并重写该接口的run()方法
 * 2. 创建 Runnable实现类的对象，作为创建Thread对象的target参数，此Thread对象才是真正的线程对象
 * 3. 调用该线程对象的start()方法来启动线程
 */
public class MyThread extends Object implements Runnable {

    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName() + ".run()");
    }
    
    public static void main(String[] args) {
        MyThread myThread = new MyThread();

        // 实现多个线程
        Thread t1 = new Thread(myThread, "张三");
        Thread t2 = new Thread(myThread, "李四");
        // 启动线程
        t1.start();
        t2.start();
    }
}
```

#### 1.3 使用Callable和Future创建线程

​	 Callable接口提供了一个call()方法作为线程执行体，call()方法比run()方法功能要强大：call()方法可以有返回值，可以声明抛出异常。 

```java
public interface Callable<V> {
    /**
     * Computes a result, or throws an exception if unable to do so.
     *
     * @return computed result
     * @throws Exception if unable to compute a result
     */
    V call() throws Exception;
}
```

实例：

```java

/**
 * 1. 定义一个类实现Callable接口，并重写call()方法，该call()方法将作为线程执行体，并且有返回值
 * 2. 创建Callable实现类的实例，使用FutureTask类来包装Callable对象
 * 3. 使用FutureTask对象作为Thread对象的target创建并启动线程
 * 4. 调用FutureTask对象的get()方法来获得子线程执行结束后的返回值
 */
public class MyCallable extends Object implements Callable {
    @Override
    public Object call() throws Exception {
        int sum = 0;
        for (int i = 0; i < 100; i++) {
            sum += i;
        }
        System.out.println(Thread.currentThread().getName() + ":sum = " + sum);
        return sum;
    }

    public static void main(String[] args) {
        MyCallable myCallable = new MyCallable();
        FutureTask<Integer> futureTask = new FutureTask(myCallable);
        new Thread(futureTask).start();
        try {
            System.out.println("子线程的返回值: " + futureTask.get());
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (ExecutionException e) {
            e.printStackTrace();
        }
    }
}
```

#### 1.4 基于线程池的方式

​	线程和数据库连接都是很宝贵的资源。 每次需要的时候创建，不需要的时候销毁，是非常浪费资源的。通过缓存的策略即使用线程池。

```java
/**
 * 1. 定义一个类实现Callable接口，并重写call()方法，该call()方法将作为线程执行体，并且有返回值
 * 2. 创建Callable实现类的实例，使用FutureTask类来包装Callable对象
 * 3. 使用FutureTask对象作为Thread对象的target创建并启动线程
 * 4. 调用FutureTask对象的get()方法来获得子线程执行结束后的返回值
 */
public class MyCallable extends Object implements Callable {
    @Override
    public Object call() throws Exception {
        int sum = 0;
        for (int i = 0; i < 100; i++) {
            sum += i;
        }
        System.out.println(Thread.currentThread().getName() + ":sum = " + sum);
        return sum;
    }

    public static void main(String[] args) {
        ExecutorService threadPoll = Executors.newFixedThreadPool(10);
        while (true) {
            threadPoll.execute(new Runnable() {
                @Override
                public void run() {
                    System.out.println(Thread.currentThread().getId() + "is running");
                    try {
                        Thread.sleep(1000);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
            });
        }
    }
}

```

### 2. 4种线程池

​	Java里面线程池的顶级接口是**Executor**，但是严格意义上讲Executor并不是一个线程池，而只是一个执行线程的工具。真正的线程池接口是**ExecutorService**。

​	 Java通过Executors提供了四种线程池， 这四种线程池都基于一个类（ThreadPoolExecutor）的构造函数实现的：

```java
public ThreadPoolExecutor(int corePoolSize,
                              int maximumPoolSize,
                              long keepAliveTime,
                              TimeUnit unit,
                              BlockingQueue<Runnable> workQueue,
                              ThreadFactory threadFactory,
                              RejectedExecutionHandler handler) {
        // ...初始化
    }
```
其参数列表如下：

| 名称            | 类型                     | 含义                                                         |
| --------------- | ------------------------ | ------------------------------------------------------------ |
| corePoolSize    | int                      | 核心线程池数量                                               |
| maximumPoolSize | int                      | 最大线程池数量                                               |
| keepAliveTime   | long                     | 当前线程池数量超过 corePoolSize 时，多余的空闲线程的存活时间。 |
| unit            | TimeUnit                 | 时间单位                                                     |
| workQueue       | BlockingQueue<Runnable>  | 任务队列，被提交但尚未被执行的任务。                         |
| threadFactory   | ThreadFactory            | 线程创建工厂                                                 |
| handler         | RejectedExecutionHandler | 拒绝策略，当任务太多来不及处理，如何拒绝任务。               |

![]()

​													             线程任务处理流程



#### 2.1 CachedThreadPool()

可缓存线程池，优点如下：

  1. 线程池无限制

  2.  有空闲线程则复用空闲线程，若无空闲线程则新建线程 

  3.  一定程序减少频繁创建/销毁线程，减少系统开销 

     ```java
     	/**
          * Creates a thread pool that creates new threads as needed, but
          * will reuse previously constructed threads when they are
          * available.
          * 创建一个根据需要创建新线程的线程池 
          */
         public static ExecutorService newCachedThreadPool() {
             return new ThreadPoolExecutor(0, Integer.MAX_VALUE,
                                           60L, TimeUnit.SECONDS,
                                           new SynchronousQueue<Runnable>());
         }
     ```

     SynchronousQueue： 是无缓冲等待队列，是一个不存储元素的阻塞队列，会直接将任务交给消费者，必须等队列中的添加元素被消费后才能继续添加新的元素。 

#### 2.2 FixedThreadPool（）

定长线程池，优点如下：

   1. 可控制线程的最大并发数

   2. 超出的线程会在队列中等待

      ```java
      /**
        * Creates a thread pool that reuses a fixed number of threads
        * operating off a shared unbounded queue.  
        */
        public static ExecutorService newFixedThreadPool(int nThreads) {
             return new ThreadPoolExecutor(nThreads, nThreads,
                                         0L, TimeUnit.MILLISECONDS,
                                          new LinkedBlockingQueue<Runnable>());
        }
      ```

      LinkedBlockingQueue：基于链表的先进先出队列，无界。



#### 2.3 newScheduledThreadPool（）

 调度线程池

```java

 	/**
      * Creates a thread pool that can schedule commands to run after a
      * given delay, or to execute periodically.
      * 创建一个线程池，该线程池可以计划在
      * 给定的延迟，或周期性地执行。
      */
     public static ScheduledExecutorService newScheduledThreadPool(int corePoolSize) {
         return new ScheduledThreadPoolExecutor(corePoolSize);
     }
```


#### 2.4  newSingleThreadExecutor（）

单例线程池。优点：

​    1. 始终都由一个线程来执行

​	2. 保证所有任务按顺序完成
```java
 
 	 /**
      * Creates an Executor that uses a single worker thread operating
      * off an unbounded queue. 
      * 创建从无限的队列中使用单个工作线程操作的执行器。
      */
     public static ExecutorService newSingleThreadExecutor() {
         return new FinalizableDelegatedExecutorService
             (new ThreadPoolExecutor(1, 1,
                                     0L, TimeUnit.MILLISECONDS,
                                     new LinkedBlockingQueue<Runnable>()));
     }
 
```

### 3. 线程生命周期（状态）

#### 3.1 新建状态（NEW）

​		当程序使用<font color="red">new关键字创建了一个线程之后</font>>，该线程就处于新建状态，此时仅由JVM为其分配内存，并初始化其成员变量的值

#### 3.2 就绪状态（RUNNABLE）

​		当线程对象<font color="red">调用了start()方法之后</font>，该线程处于就绪状态。JVM会为其创建方法调用栈和程序计数器，等待调度运行。

#### 3.3  运行状态（RUNNING）

​		如果处于<font color="red">就绪状态的线程获得了CPU，开始执行run()方法的线程执行体</font>，则该线程处于运行状态。

#### 3.4 阻塞状态（BLOCKED）

​		阻塞状态是指线程因为某种原因放弃了cpu的使用权，也即让出了cpu timeslice，暂时停止运行。直到线程进入可运行状态，才有机会再次获得cpu timesslice 转到运行(running)状态。阻塞的情况分三种：

	1. 等待阻塞（o.wait -> 等待队列）：运行(running)的线程执行 o.wait()方法，JVM 会把该线程放入等待队列(waitting queue)中。
 	2. 同步阻塞 (lock-> 锁池 )：运行(running)的线程在获取对象的同步锁时，<font color="red">若该同步锁被别的线程占用</font>，则 JVM 会把该线程放入锁池(lock pool)中。
 	3. 其他阻塞 (sleep/join)：运行(running)的线程执行 Thread.sleep(long ms)或 t.join()方法，或者发出了 I/O 请求时，JVM 会把该线程置为阻塞状态。当 sleep()状态超时、join()等待线程终止或者超时、或者 I/O处理完毕时，线程重新转入可运行(runnable)状态。

#### 3.5 线程死亡（DEAD）

​		线程会以下面三种方式结束，结束后就是死亡状态。

1.  正常结束：run()或call()方法执行完成，线程正常结束。

 	2.  异常结束：线程抛出一个未捕获Exception或Error
 	3.  调用stop：直接调用该线程的 stop()方法来结束该线程—该方法通常容易导致死锁，不推荐使用。



### 4. sleep与wait的区别

	1.  对于sleep()方法，该方法是属于Thread类中的。而wait()方法，则是属于Object类中的。
 	2.  sleep()方法让当前线程暂停指定的时间，让出cpu给其他线程，但<font color="red">他的监控状态依然保持着</font> ,当指定的时间到了就会自动恢复运行状态。
 	3. 在调用sleep()方法的过程中，<font color="red">线程不会释放对象锁</font>。
 	4. 而当<font color="red">调用wait()方法的时候，线程会放弃对象锁</font>，进入等待此对象的<font color="red">等待锁定池</font>，只有针对此对象调用notify()方法后本线程才进入对象锁定池准备获取对象锁进入运行状态。

### 5.  start 与 run的区别

	1. start() 方法启动线程，真正实现了多线程运行。这时无需等待run方法执行完毕，可以直接继续执行下面的代码。
 	2. 通过调用 Thread 类的 start()方法来启动一个线程， 这时此线程是处于就绪状态， 并没有运行。
 	3. 方法 run()称为线程体，它包含了要执行的这个线程的内容，线程就进入了运行状态，开始运行 run 函数当中的代码。 Run 方法运行结束， 此线程终止。然后 CPU 再调度其它线程。
 	4. start()是线程的启动者，run()是线程任务的运行者。

```java
public class Test {

    public static void main(String[] args) {
        RunnableThread runnableThread = new RunnableThread();
        // 情况一
        runnableThread.run();
        runnableThread.run();
        // 情况二
//        new Thread(runnableThread).start();
//        new Thread(runnableThread).start();
    }
}

class RunnableThread implements Runnable{

    private int count = 5;
    @Override
    public void run() {
        while (count > 0) {
            System.out.println(Thread.currentThread().getName() + "====" + count);
            count--;
        }
    }
}
// 注释情况2   情况1输出
main====5
main====4
main====3
main====2
main====1
// 注释情况1   情况2输出   
Thread-1====5
Thread-0====5
Thread-0====3
Thread-0====2
Thread-1====4
Thread-0====1
    
```



### 6. Java锁

#### 6.1 乐观锁

​	乐观锁是一种乐观思想，即认为读多写少，遇到并发写的可能性低，每次去拿数据的时候都认为别人不会修改，所以不会上锁，但是<font color="red">在更新的时候回判断一下在此期间别人有没有去更新这个数据，采取在写时先读出当前版本号，然后加锁操作</font>（ 比较跟上一次的版本号，如果一样则更新），如果失败则要重复读-比较-写的操作。

​	java 中的乐观锁基本都是通过 CAS 操作实现的，CAS 是一种更新的原子操作，<font color="red">比较当前值跟传入值是否一样，一样则更新，否则失败。</font>

##### 6.2 悲观锁

​	悲观锁是就是悲观思想，即认为写多，遇到并发写的可能性高，每次去拿数据的时候都认为别人会修改，所以每次在读写数据的时候都会上锁，这样别人想读写这个数据就会block直到拿到锁。java中的悲观锁就是<font color="red">Synchronized</font>,AQS框架下的锁则是先尝试cas乐观锁去获取锁，获取不到，才会转换为悲观锁，如 RetreenLock。

##### 6.3 自旋锁

​	<font color="red">如果持有锁的线程能够在很短时间内释放锁资源，那么那些等待竞争锁的线程就不需要做内核态和用户态之间的切换进入阻塞挂起状态，它们只需要等一等（自旋），等持有锁的线程释放锁后即可立刻获取锁，这样就避免用户线程和内核的切换的消耗。</font>

​	线程自旋是需要消耗 cup 的，说白了就是让 cup 在做无用功，如果一直获取不到锁，那线程也不能一直占用 cup 自旋做无用功，所以需要设定一个自旋等待的最大时间。

​	如果持有锁的线程执行的时间超过自旋等待的最大时间扔没有释放锁，就会导致其它争用锁的线程在最大等待时间内还是获取不到锁，这时争用线程会停止自旋进入阻塞状态。

##### 6.4 Synchronized同步锁

​	synchronized它可以把任意一个非NULL的对象当做锁。<font color="red">他属于独占式的悲观锁同时属于可重入锁</font>。

###### Synchronized作用范围

		1.  作用于方法时，锁住的是对象的实例(this)；
  		2.  当作用于静态方法时，锁住的是Class实例，又因为Class的相关数据存储在永久带PermGen
        （jdk1.8 则是 metaspace），永久带是全局共享的，因此静态方法锁相当于类的一个全局锁，
        会锁所有调用该方法的线程。
    		3.  synchronized 作用于一个对象实例时，锁住的是所有以该对象为锁的代码块。它有多个队列，当多线程一起访问某个对象监视器的时候，对象监视器会将这些线程存储在不同的容器中。

###### Synchronized核心组件

- Wait Set：那些调用wait方法被阻塞的线程被放置在这里；

- Contention List: 竞争队列，所有请求锁的线程首先被放在这个竞争队列中；

- Entry List：Contention List 中那些<font color="red">有资格成为候选资源的线程被移动到Entry List 中</font>
- OnDeck：任意时刻，<font color="red">最多只有一个线程正在竞争锁资源，该线程被成为 OnDeck；</font>
- Owner：当前以获取到锁资源的线程被称为Owner；
- ！Owner：当前释放锁的线程；

###### Synchronized实现

1. JVM每次从队列的尾部取出一个数据用于锁竞争候选者（OnDeck）,但是并发情况下，ContentionList会被大量的并发线程进行CAS访问，为了降低对尾部元素的竞争，JVM会将一部分线程移动到EntryList中作为候选竞争线程。
2. Owner线程会在unlock时，将ContentionList中的部分线程迁移到EntryList中，并指定EntryList中的某个线程为OnDeck线程。
3. Owner 线程并不直接把锁传递给 OnDeck 线程，而是把锁竞争的权利交给 OnDeck，OnDeck需要重新竞争锁。这样虽然牺牲了一些公平性，但是能极大的提升系统的吞吐量，在JVM 中，也把这种选择行为称之为“竞争切换”。
4. OnDeck线程获取到锁资源后会变为Owner线程，而没有得到锁资源的仍然停留在EntryList中。如果Owner线程被wait方法阻塞，则转移到WaitSet队列中，直到某个时刻通过notify或者 notifyAll 唤醒，会重新进去EntryList 中。
5. 处于ContentionList、EntryList、WaitSet中的线程都处于阻塞状态，该阻塞是由操作系统来完成的（Linux 内核下采用 pthread_mutex_lock 内核函数实现的）。
6. Synchronized是非公平锁。 Synchronized在线程进入ContentionList时，<font color="red">等待的线程会先尝试自旋获取锁，如果获取不到就进入 ContentionList</font>，这明显对于已经进入队列的线程是不公平的，还有一个不公平的事情就是自旋获取锁的线程还可能直接抢占 OnDeck 线程的锁资源。
7. 每个对象都有个 monitor 对象，<font color="red">加锁就是在竞争 monitor 对象</font>，代码块加锁是在前后分别加上 monitorenter 和 monitorexit 指令来实现的，方法加锁是通过一个标记位来判断的。
8. synchronized<font color="red"> 是一个重量级操作，需要调用操作系统相关接口</font>，性能是低效的，有可能给线
   程加锁消耗的时间比有用操作消耗的时间更多。
9. Java1.6，synchronized 进行了很多的优化，有适应自旋、锁消除、锁粗化、轻量级锁及偏向锁等，效率有了本质上的提高。在之后推出的 Java1.7 与 1.8 中，均对该关键字的实现机理做了优化。引入了偏向锁和轻量级锁。都是在对象头中有标记位，不需要经过操作系统加锁。
10. 锁可以从偏向锁升级到轻量级锁，再升级到重量级锁。这种升级过程叫做锁膨胀；





#### 7.线程基本方法

##### 7.1 线程等待（wait）

​	调用该方法的线程进入WAITING状态，只有等待另外线程的通知或被中断才会返回，主要注意的是调用wait()方法后，**会释放对象的锁**。因此，wait方法一般用在同步方法或同步代码块中。



##### 7.2 线程睡眠（sleep）

​	sleep导致当前线程休眠，与wait方法不同的是**sleep不会释放当前占有的锁**，sleep(long)会导致线程进入<font color="red">TIMED-WATING</font>状态，而<font color="red">wait()方法会导致当前线程进入WATING</font>状态。



##### 7.3 线程让步（yield）

​	yield会使当前线程**让出CPU执行时间片**，与其他线程一起重新竞争CPU时间片。一般情况下，优先级高的线程有更大的可能性成功竞争得到CPU时间片。

##### 7.4 线程中断（interrupt）

​	中断一个线程，其本意是**给这个线程一个通知信号，会影响这个线程内部的一个中断标识位。这个线程本身并不会因此而改变状态（如：阻塞，终止等）。**






























