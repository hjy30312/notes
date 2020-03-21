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

| 名称            | 类型                     | 含义                                       |
| --------------- | ------------------------ | ------------------------------------------ |
| corePoolSize    | int                      | 核心线程池数量                             |
| maximumPoolSize | int                      | 最大线程池数量                             |
| keepAliveTime   | long                     | 线程最大空闲时间（非核心线程闲置超时时长） |
| unit            | TimeUnit                 | 时间单位                                   |
| workQueue       | BlockingQueue<Runnable>  | 线程等待队列                               |
| threadFactory   | ThreadFactory            | 线程创建工厂                               |
| handler         | RejectedExecutionHandler | 拒绝策略                                   |



![11183270-a01aea078d7f4178](C:\Users\54336\Desktop\11183270-a01aea078d7f4178.webp)

​													             线程任务处理流程



#### 2.1 CachedThreadPool()

​	可缓存线程池，优点如下：

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

     ​	定长线程池，优点如下：

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

          LinkedBlockingQueue：

          ​		

     ​	











