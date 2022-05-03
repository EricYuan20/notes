## 线程的状态

```java
//新生
NEW
//运行
RUNNABLE 
//阻塞
BLOCKED 
//等待
WAITING 
//超时等待
TIMED_WAITING
//终止
TERMINATED
```

## Lock

**公平锁：** 十分公平，必须先来后到~；

**非公平锁：** 十分不公平，可以插队；**(默认为非公平锁)**

### 传统的 synchronized

```java
/**
 * 真正的多线程开发
 * 线程就是一个单独的资源类，没有任何的附属操作！
 */
public class SaleTicketDemo01 {
    public static void main(String[] args) {
        // 多线程操作
        // 并发：多线程操作同一个资源类，把资源类丢入线程
        Ticket ticket = new Ticket();

        // @FunctionalInterface 函数式接口 jdk1.8之后 lambda表达式
        new Thread(()->{
            for(int i=0;i<40;i++){
                ticket.sale();
            }
        },"A").start();
        new Thread(()->{
            for(int i=0;i<40;i++){
                ticket.sale();
            }
        },"B").start();
        new Thread(()->{
            for(int i=0;i<40;i++){
                ticket.sale();
            }
        },"C").start();
    }
}
// 资源类
// 属性+方法
// oop
class Ticket{
    private int number=50;


    // 卖票的方式
    // synchronized 本质：队列，锁
    public synchronized void sale(){
        if(number>0){
            System.out.println(Thread.currentThread().getName()+" 卖出了第"+number+" 张票,剩余："+number+" 张票");
            number--;
        }
    }
}

```

### **Lock接口**

```java
public class SaleTicketDemo02 {
    public static void main(String[] args) {
        // 多线程操作
        // 并发：多线程操作同一个资源类，把资源类丢入线程
        Ticket2 ticket = new Ticket2();
        new Thread(()->{for(int i=0;i<40;i++) ticket.sale(); },"A").start();
        new Thread(()->{for(int i=0;i<40;i++) ticket.sale(); },"B").start();
        new Thread(()->{for(int i=0;i<40;i++) ticket.sale(); },"C").start();
    }
}

// lock三部曲
// 1、Lock lock=new ReentrantLock();
// 2、lock.lock() 加锁
// 3、finally=> 解锁：lock.unlock();
class Ticket2{
    private int number=50;

    Lock lock=new ReentrantLock();

    // 卖票的方式
    // 使用Lock 锁
    public void sale(){
        //加锁
        lock.lock();
        try {
            //业务代码
            if(number>=0){
                System.out.println(Thread.currentThread().getName()+" 卖出了第"+number+" 张票,剩余："+number+" 张票");
                number--;
            }
        }catch (Exception e) {
            e.printStackTrace();
        }
        finally {
            // 解锁
            lock.unlock();
        }
    }
}

```

### **Synchronized 和 Lock区别**

- 1、Synchronized 内置的Java关键字，Lock是一个Java类

- 2、Synchronized 无法判断获取锁的状态，Lock可以判断

- 3、Synchronized 会自动释放锁，lock必须要手动加锁和手动释放锁！可能会遇到死锁

- 4、Synchronized 线程1(获得锁->阻塞)、线程2(等待)；

  lock就不一定会一直等待下去，lock会有一个trylock去尝试获取锁，不会造成长久的等待。

- 5、Synchronized 是可重入锁，不可以中断的，非公平的；Lock，可重入的，可以判断锁，可以自己设置公平锁和非公平锁；

- 6、Synchronized 适合锁少量的代码同步问题，Lock适合锁大量的同步代码；

## 常用辅助类（掌握）

### CountDownLatch(倒数计时)

```java
// 减法计数器
public class CountDownLatchDemo {
    public static void main(String[] args) throws InterruptedException {
        // 总数是6,有必须要执行的任务时再使用
        CountDownLatch countDownLatch = new CountDownLatch(6);

        for (int i = 1; i <= 6; i++) {
            new Thread(() -> {
                System.out.println(Thread.currentThread().getName() + "Go Out!");
                countDownLatch.countDown(); // 数量-1
            }, String.valueOf(i)).start();
        }

        countDownLatch.await(); // 等待计数器归零 在向下执行
        countDownLatch.countDown(); // -1

        System.out.println("close Door");
    }
}
```

原理：

`countDownLatch.countDown()` ：数量-1

`countDownLatch.await()`：等待计数器归零 在向下执行

**await等待计数器为0，就唤醒，再继续向下运行**

### CyclicBarrier(循环壁垒)

![img](https://img2020.cnblogs.com/blog/1988929/202104/1988929-20210412001128495-1742955859.png)

```java
// 加法计数器
public class CyclicBarrierDemo {
    public static void main(String[] args) {
        /**
         * 集齐七个龙珠召唤神龙
         */
        // 召唤龙珠的线程
        CyclicBarrier cyclicBarrier = new CyclicBarrier(7, ()->{
            System.out.println("召唤神龙成功！");
        });

        for (int i = 1; i <= 7; i++) {
            final int temp = i;
            // lambda能操作i吗？ 不行！
            new Thread(()->{
                System.out.println(Thread.currentThread().getName() + "收集了" + temp + "个龙珠");

                try {
                    cyclicBarrier.await(); // 等待，通过await()来计数 直到召唤后才执行后面的 与 CountDownLatch 有区别
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } catch (BrokenBarrierException e) {
                    e.printStackTrace();
                }
            }).start();
        }
    }
}
```

### Semaphore(记录型信号量)

![img](https://img2020.cnblogs.com/blog/1988929/202104/1988929-20210412001151192-91445236.png)

```java
public class SemaphoreDemo {
    public static void main(String[] args) {
        // 线程数量：停车位 限流！
        Semaphore semaphore = new Semaphore(3);

        for (int i = 1; i <= 6; i++) {
            new Thread(()->{
                // acquire() 得到
                try {
                    semaphore.acquire();
                    System.out.println(Thread.currentThread().getName() + "抢到车位");
                    TimeUnit.SECONDS.sleep(2);
                    System.out.println(Thread.currentThread().getName() + "离开车位");
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } finally {
                    semaphore.release();// release() 释放
                }
            }, String.valueOf(i)).start();
        }
    }
}
```

### ReadWriteLock(读写锁)

![img](https://img2020.cnblogs.com/blog/1988929/202104/1988929-20210412001210692-1230271610.png)

```java
/**
 * 独占锁（写锁）：一次只能被一个线程占有
 * 共享锁（读锁）：多个线程可以同时占有
 * ReadWriteLock
 * 读-读：可以共存
 * 读-写：不能共存
 * 写-写：不能共存
 */
public class ReadWriteLockDemo {
    public static void main(String[] args) {

        myCacheLock myCache = new myCacheLock();

        // 寫入
        for (int i = 1; i <= 5; i++) {
            final int temp = i;
            new Thread(()->{
                myCache.put(temp+"", temp+"");
            }, String.valueOf(i)).start();
        }

        // 讀取
        for (int i = 1; i <= 5; i++) {
            final int temp = i;
            new Thread(()->{
                myCache.get(temp+"");
            }, String.valueOf(i)).start();
        }
    }
}

/**
 * 自定义缓存
 */
// 加锁的
class myCacheLock {
    private volatile Map<String, Object> map = new HashMap<>();
    // 读写锁 更加细粒度的控制
    private ReadWriteLock readWriteLock = new ReentrantReadWriteLock();

    // 存， 写入的时候 只希望一个线程去写
    public void put(String key, Object value) {
        readWriteLock.writeLock().lock();
        try {
            System.out.println(Thread.currentThread().getName() + "写入" +  key);
            map.put(key, value);
            System.out.println(Thread.currentThread().getName() + "写入完毕");
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            readWriteLock.writeLock().unlock();
        }
    }

    // 取， 读  所有人都可以读！
    public void get(String key) {
        readWriteLock.readLock().lock();
        try {
            System.out.println(Thread.currentThread().getName() + "读取" +  key);
            Object o = map.get(key);
            System.out.println(Thread.currentThread().getName() + "读取ok" +  key);
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            readWriteLock.readLock().unlock();
        }
    }
}
```

## 阻塞队列

阻塞队列（BlockingQueue）

![img](https://img2020.cnblogs.com/blog/1988929/202104/1988929-20210412001229757-1616628551.png)

**四组API**

| 方式         | 抛出异常  | 不会抛出异常，有返回值 | 阻塞 等待 | 超时 等待                |
| :----------- | --------- | ---------------------- | --------- | ------------------------ |
| 添加         | add()     | offer()                | put()     | offer(timenum，timeUnit) |
| 移除         | remove()  | poll()                 | take()    | poll(timenum，timeUnit)  |
| 得到队首元素 | element() | peek()                 | -         | -                        |

## 线程池(重点)

### **线程池：三大方法**

- ExecutorService threadPool = Executors.newSingleThreadExecutor(); // 单个线程
- ExecutorService threadPool2 = Executors.newFixedThreadPool(5); // 创建一个固定的线程池的大小
- ExecutorService threadPool3 = Executors.newCachedThreadPool(); // 可伸缩的

```java
// Executors 工具类，3大方法
public class ThreadPool {
    public static void main(String[] args) {
        ExecutorService threadPool = Executors.newSingleThreadExecutor(); // 单个线程
//        ExecutorService threadPool = Executors.newFixedThreadPool(5); // 创建一个固定的线程池的大小
//        ExecutorService threadPool = Executors.newCachedThreadPool(); // 可伸缩的

        try {
            for (int i = 0; i < 100; i++) {
                // 使用线程池后，使用线程池创建线程
                threadPool.execute(()->{
                    System.out.println(Thread.currentThread().getName() + " ok");
                });
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            // 线程池用完，程序结束，关闭线程池
            threadPool.shutdown();
        }
    }
}
```

### **线程池：七大参数**

源码剖析：

```java
public static ExecutorService newSingleThreadExecutor() {
    return new FinalizableDelegatedExecutorService
        (new ThreadPoolExecutor(1, 1,
                                0L, TimeUnit.MILLISECONDS,
                                new LinkedBlockingQueue<Runnable>()));
}


public static ExecutorService newFixedThreadPool(int nThreads) {
    return new ThreadPoolExecutor(nThreads, nThreads,
                                  0L, TimeUnit.MILLISECONDS,
                                  new LinkedBlockingQueue<Runnable>());
}


public static ExecutorService newCachedThreadPool() {
    return new ThreadPoolExecutor(0, Integer.MAX_VALUE,
                                  60L, TimeUnit.SECONDS,
                                  new SynchronousQueue<Runnable>());
}

// 本质：三大方法都是开启 ThreadPoolExecutor 即：
public ThreadPoolExecutor(int corePoolSize,  //核心线程池大小
                          int maximumPoolSize, //最大的线程池大小
                          long keepAliveTime,  //超时了没有人调用就会释放
                          TimeUnit unit, //超时单位
                          BlockingQueue<Runnable> workQueue, //阻塞队列
                          ThreadFactory threadFactory, //线程工厂 创建线程的 一般不用动
                          RejectedExecutionHandler handler //拒绝策略
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

![img](https://img2020.cnblogs.com/blog/1988929/202104/1988929-20210412001333949-1361376523.png)

### **手动创建线程池**

```java
/**
 * new ThreadPoolExecutor.AbortPolicy() // 该拒绝策略为：超出最大承载，就会抛出异常
 * new ThreadPoolExecutor.CallerRunsPolicy() // 该拒绝策略为：哪来的去哪里 main线程进行处理
 * new ThreadPoolExecutor.DiscardPolicy() // 该拒绝策略为：队列满了,丢掉异常，不会抛出异常
 * new ThreadPoolExecutor.DiscardOldestPolicy() // 该拒绝策略为：队列满了，尝试去和最早的进程竞争，不会抛出异常
 */
public class ThreadPoolDemo {
    public static void main(String[] args) {
        // 自定义线程池  一般情况下，工作中只用 ThreadPoolExecutor 安全！
         /*最大线程如何获取？ （调优）
             1、CPU密集型  电脑的核数是几核就选择几；选择maximunPoolSize的大小
             2、IO密集型  程序中有15个大型任务，io十分占用资源；I/O密集型就是判断我们程序中十分耗I/O的线程数量，大约是最大I/O数的一倍到两倍之间。
         */
        // 获取CPU核数
        System.out.println(Runtime.getRuntime().availableProcessors()); // 4
        ThreadPoolExecutor threadPool = new ThreadPoolExecutor(
                2,
                Runtime.getRuntime().availableProcessors(),
                3,
                TimeUnit.SECONDS,
                new LinkedBlockingDeque<>(3),
                Executors.defaultThreadFactory(),
                new ThreadPoolExecutor.AbortPolicy()
        );

        try {
            // 最大承载量： Max +  Deque = 5 + 3 超过则报 RejectedExecutionException
            for (int i = 1; i <= 9; i++) {
                threadPool.execute(()->{
                    System.out.println(Thread.currentThread().getName() + " ok");
                });
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            threadPool.shutdown();
        }
    }
}
```

## 四大函数式接口（掌握）

==掌握：Lambda表达式、链式编程、函数式接口、Stream流式计算==

### **函数式接口：只有一个方法的接口**

```java
@FunctionalInterface
public interface Runnable {
    public abstract void run();
}
```

![img](https://img-blog.csdnimg.cn/20200727203732511.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQxNjE3ODQ4,size_16,color_FFFFFF,t_70)

### **Function 函数型接口**

![img](https://img2020.cnblogs.com/blog/1988929/202104/1988929-20210412001536937-555498756.png)

```java
/**
 * Function 函数型接口
 */
public class FunctionDemo {
    public static void main(String[] args) {
/*        Function<String, String> funInterface = new Function<>() {
            @Override
            public String apply(String s) {
                return s;
            }
        };*/
        // Lambda 表达式简化
        Function<String, String> funInterface = (str)->{return str;};
        System.out.println(funInterface.apply("Hello"));
    }
}
```

### **Predicate 断定型接口**

![img](https://img2020.cnblogs.com/blog/1988929/202104/1988929-20210412001604032-270967566.png)

### **Consumer 消费型接口**

![img](https://img2020.cnblogs.com/blog/1988929/202104/1988929-20210412001622586-1707498811.png)

### **Supplier 供给型接口**

![img](https://img2020.cnblogs.com/blog/1988929/202104/1988929-20210412001637789-974581726.png)

## Stream流式计算

定义：存储+计算

![img](https://img2020.cnblogs.com/blog/1988929/202104/1988929-20210412001653370-613680957.png)

```java
/**
 * 题目要求:一分钟内完成此题，只能用一行代码实现!
 * 现在有5个用户!筛选:
 * 1、ID必须是偶数
 * 2、年龄必须大于23岁
 * 3、用户名转为大写字母
 * 4、用户名字母倒着排序
 * 5、只输出一个用户!
 */
public class StreamDemo {
    public static void main(String[] args) {
        User a = new User(1, "a", 21);
        User b = new User(2, "b", 22);
        User c = new User(3, "c", 23);
        User d = new User(4, "d", 24);
        User e = new User(5, "e", 25);
        User f = new User(6, "f", 26);

        // 集合管存储
        List<User> list = Arrays.asList(a, b, c, d, e, f);

        // 计算交给流
        list.stream()
                .filter(u -> {
                    return u.getId() % 2 == 0;
                })
                .filter(u -> {
                    return u.getAge() > 23;
                })
                .map(u -> {
                    return u.getName().toUpperCase();
                })
                .sorted((u1, u2) -> {
                    return  u2.compareTo(u1);
                })
                .limit(1)
                .forEach(System.out::println);

    }
}
```

## ForkJoin

ForkJoin 特点： 工作窃取！

实现原理是：**双端队列**！从上面和下面都可以去拿到任务进行执行！

![img](https://img2020.cnblogs.com/blog/1988929/202104/1988929-20210412001953549-662873456.png)

**ForkJoin的计算类！**

```java
/**
 * 计算求和的任务
 */
public class ForkJoinDemo extends RecursiveTask<Long> {

    private long star;
    private long end;

    //临界值
    private long temp = 1000000L;
	
    public ForkJoinDemo(long star, long end) {
        this.star = star;
        this.end = end;
    }

    /**
     * 计算方法
     *
     * @return Long
     */
    @Override
    protected Long compute() {
        if ((end - star) < temp) {
            Long sum = 0L;
            for (Long i = star; i < end; i++) {
                sum += i;
            }
//            System.out.println(sum);
            return sum;
        } else {
            //使用forkJoin 分而治之 计算
            //计算平均值
            long middle = (star + end) / 2;
            ForkJoinDemo forkJoinDemoTask1 = new ForkJoinDemo(star, middle);
            forkJoinDemoTask1.fork();  //拆分任务，把线程任务压入线程队列
            ForkJoinDemo forkJoinDemoTask2 = new ForkJoinDemo(middle, end);
            forkJoinDemoTask2.fork();  //拆分任务，把线程任务压入线程队列
            long taskSum = forkJoinDemoTask1.join() + forkJoinDemoTask2.join();
            return taskSum;
        }
    }
}
```

测试：

```
public class Test {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
//        test1(); // 24549
//        test2(); // 15065
        test3(); // 490
    }

    // 普通
    public static void test1() {
        long start = System.currentTimeMillis();
        Long sum = 0L;
        for (Long i = 1L; i <= 10_0000_0000; i++) {
            sum += i;
        }

        long end = System.currentTimeMillis();
        System.out.println("Sum = " + sum + "，时间 = " + (end - start));
    }

    // 使用ForkJoin
    public static void test2() throws ExecutionException, InterruptedException {
        long start = System.currentTimeMillis();

        ForkJoinPool forkJoinPool = new ForkJoinPool();
        ForkJoinTask<Long> task = new ForkJoinDemo(0L, 10_0000_0000);
        ForkJoinTask<Long> submit = forkJoinPool.submit(task);
        Long sum = submit.get();
        long end = System.currentTimeMillis();

        System.out.println("Sum = " + sum + "，时间 = " + (end - start));
    }

    // Stream 并行流
    public static void test3() {
        long start = System.currentTimeMillis();

        // range() : ()  rangeClosed() : (]
        long sum = LongStream.rangeClosed(0L, 10_0000_0000).parallel().reduce(0, Long::sum);

        long end = System.currentTimeMillis();
        System.out.println("Sum = " + sum + "，时间 = " + (end - start));
    }
}
```

## 异步回调

![img](https://img2020.cnblogs.com/blog/1988929/202104/1988929-20210412002430393-1913192486.png)

但是我们平时都使用**CompletableFuture**

```java
 public static void main(String[] args) throws ExecutionException, InterruptedException {
        // 没有返回值的 runAsync 异步回调
        CompletableFuture<Void> completableFuture = CompletableFuture.runAsync(() -> {
            try {
                TimeUnit.SECONDS.sleep(2);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println(Thread.currentThread().getName() + "runAsync => Void");
        });

        System.out.println("22222");

        completableFuture.get(); // 阻塞获取结果

        System.out.println("=============================");

        // 有返回值的 runAsync 异步回调
        CompletableFuture<Integer> completableFuture = CompletableFuture.supplyAsync(() -> {
            System.out.println(Thread.currentThread().getName() + "supplyAsync => Integer");
            int i = 10 / 0;
            return 1024;
        });

        System.out.println(completableFuture.whenComplete((t, u) -> {
            System.out.println("T => " + t); // 正常的返回结果
            System.out.println("U => " + u); // 错误信息 java.lang.ArithmeticException: / by zero
        }).exceptionally((e) -> {
            System.out.println(e.getMessage());
            return 233; // 可以获取到错误的返回结果
        }).get());
    }
}
```

## JMM

> 请你谈谈你对Volatile 的理解

Volatile 是 Java 虚拟机提供 轻量级的同步机制

**1、保证可见性**
**2、不保证原子性**
**3、禁止指令重排**

> 什么是JMM？

JMM：JAVA内存模型，不存在的东西，是一个概念，也是一个约定！

**关于JMM的一些同步的约定：**

1、线程解锁前，必须把共享变量**立刻**刷回主存；

2、线程加锁前，必须**读取主存**中的最新值到工作内存中；

3、加锁和解锁是同一把锁；

线程中分为 **工作内存、主内存**

**8种操作:**

- **Read（读取）**：作用于主内存变量，它把一个变量的值从主内存传输到线程的工作内存中，以便随后的load动作使用；
- **load（载入）**：作用于工作内存的变量，它把read操作从主存中变量放入工作内存中；
- **Use（使用）**：作用于工作内存中的变量，它把工作内存中的变量传输给执行引擎，每当虚拟机遇到一个需要使用到变量的值，就会使用到这个指令；
- **assign（赋值）**：作用于工作内存中的变量，它把一个从执行引擎中接受到的值放入工作内存的变量副本中；
- **store（存储）**：作用于主内存中的变量，它把一个从工作内存中一个变量的值传送到主内存中，以便后续的write使用；
- **write（写入）**：作用于主内存中的变量，它把store操作从工作内存中得到的变量的值放入主内存的变量中；
- **lock（锁定）**：作用于主内存的变量，把一个变量标识为线程独占状态；
- **unlock（解锁）**：作用于主内存的变量，它把一个处于锁定状态的变量释放出来，释放后的变量才可以被其他线程锁定；

**JMM对这8种操作给了相应的规定**：

- 不允许read和load、store和write操作之一单独出现。即使用了read必须load，使用了store必须write

- 不允许线程丢弃他最近的assign操作，即工作变量的数据改变了之后，必须告知主存

- 不允许一个线程将没有assign的数据从工作内存同步回主内存

- 一个新的变量必须在主内存中诞生，不允许工作内存直接使用一个未被初始化的变量。就是对变量实施use、

  store操作之前，必须经过assign和load操作

- 一个变量同一时间只有一个线程能对其进行lock。多次lock后，必须执行相同次数的unlock才能解锁

- 如果对一个变量进行lock操作，会清空所有工作内存中此变量的值，在执行引擎使用这个变量前，必须重新

  load或assign操作初始化变量的值

- 如果一个变量没有被lock，就不能对其进行unlock操作。也不能unlock一个被其他线程锁住的变量

- 对一个变量进行unlock操作之前，必须把此变量同步回主内存
  所以需要A线程知道主存中的值被修改，即使用 volatile 关键词

## Volatile

### **1、保证可见性**

```java
public class Test01 {

    // 如果不加volatile 程序会死循环
    // 加了volatile是可以保证可见性的
    private volatile static int num = 0;

    public static void main(String[] args) {
        // main线程 和 测试线程
        new Thread(()->{
            while (num == 0) {}
        }, "测试线程").start();

        try {
            TimeUnit.SECONDS.sleep(1);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        num = 1;
        System.out.println(num);
    }
}
```

### **2、不保证原子性**

原子性：不可分割，线程A在执行任务的时候，不能被打扰的，也不能被分割的，要么同时成功，要么同时失败。

```java
public class Test02 {

    // volatile不保证原子性
    private volatile static int number = 0;

    public static void add() {
        number++; 
    }

    // 预计20w 实际 < 20w
    public static void main(String[] args) {
        for (int i = 0; i < 20; i++) {
            new Thread(()->{
                for (int j = 0; j < 10000; j++) {
                    add();
                }
            }).start();
        }

        while (Thread.activeCount() > 2) { // mian gc
            Thread.yield();
        }

        System.out.println(Thread.currentThread().getName() + "    " + number);
    }
}
```

## 各种锁的理解

### 1、公平锁、非公平锁

**公平锁**：非常公平；不能插队的，必须先来后到

```java
// 源码
/**
     * Creates an instance of {@code ReentrantLock}.
     * This is equivalent to using {@code ReentrantLock(false)}.
     */
public ReentrantLock() {
    sync = new NonfairSync();
}
```

**非公平锁**：非常不公平，允许插队的，可以改变顺序。

```java
/**
     * Creates an instance of {@code ReentrantLock} with the
     * given fairness policy.
     *
     * @param fair {@code true} if this lock should use a fair ordering policy
     */
public ReentrantLock(boolean fair) {
    sync = fair ? new FairSync() : new NonfairSync();
}
```

### 2、可重入锁

1.什么是可重入锁？
可重入锁，也称递归锁，指的是同一线程外层函数获得锁之后，内层递归函数仍能能获取该锁。同一个线程中，在外层方法获取锁后，进入内层方法会自动获取锁。线程可以进入任何一个它已经拥有的锁所同步着的代码块。

ReentrantLock、Synchronized 都是典型的可重入锁。

2.可重入锁有什么作用？
可重入锁最大的作用就是可以避免死锁。

![img](https://img2020.cnblogs.com/blog/1988929/202104/1988929-20210416215138903-1869118048.png)

**Synchronized 版**

```java
public class Demo01 {
    public static void main(String[] args) {

        Phone phone = new Phone();

        new Thread(()->{
            phone.sms();
        }, "A").start();

        new Thread(()->{
            phone.sms();
        }, "B").start();

    }
}

class Phone {

    public static synchronized void sms() {
        System.out.println(Thread.currentThread().getName() + " sms");
        call(); // 这里也有一把锁
    }

    private static synchronized void call() {
        System.out.println(Thread.currentThread().getName() + " call");
    }
}
```

**Lock 版**

```java
public class Demo02 {
    public static void main(String[] args) {

        Phone2 phone = new Phone2();

        new Thread(()->{
            phone.sms();
        }, "A").start();

        new Thread(()->{
            phone.sms();
        }, "B").start();

    }
}

class Phone2 {

    static Lock lock = new ReentrantLock();

    public static void sms() {
        // 加锁
        lock.lock(); // 细节：这个是两把锁，两个钥匙
        try {
            System.out.println(Thread.currentThread().getName() + " => sms");
            call(); // 这里也有一把锁
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            // 释放锁
            lock.unlock();
        }
    }

    private static void call() {
        lock.lock();
        try {
            System.out.println(Thread.currentThread().getName() + " => call");
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }
}
```

- lock锁必须配对，相当于lock和 unlock 必须数量相同，否则就会死锁在里面
- 在外面加的锁，也可以在里面解锁；在里面加的锁，在外面也可以解锁

### 3、自旋锁

spinlock

![img](https://img2020.cnblogs.com/blog/1988929/202104/1988929-20210416215215714-1055509786.png)

**自定义自旋锁**

```java
public class SpinLock {

    // Integer 0
    // Thread null
    AtomicReference<Thread> atomicReference = new AtomicReference<>();

    // 加锁
    public void myLock() {
        Thread thread = Thread.currentThread();
        System.out.println(thread.currentThread().getName() + " ==> myLock");

        // 自旋锁
        while (!atomicReference.compareAndSet(null, thread)) {
            System.out.println(Thread.currentThread().getName()+" ==> 自旋中~");
        }
    }

    // 解锁
    public void myUnLock() {
        Thread thread = Thread.currentThread();
        System.out.println(thread.currentThread().getName() + " ==> myUnLock");

        atomicReference.compareAndSet(thread, null);
    }
}
```

**测试类**

```java
public class TestSpinLock {
    public static void main(String[] args) throws InterruptedException {
//        ReentrantLock reentrantLock = new ReentrantLock();
//        reentrantLock.lock();
//        reentrantLock.unlock();

        SpinLock spinLock = new SpinLock();
        // 底层使用CAS实现
        new Thread(() -> {
            spinLock.myLock();
            try {
                TimeUnit.SECONDS.sleep(3);
            } catch (InterruptedException e) {
                e.printStackTrace();
            } finally {
                spinLock.myUnLock();
            }
        }, "T1").start();

        TimeUnit.SECONDS.sleep(2);

        new Thread(() -> {
            spinLock.myLock();
            try {
                TimeUnit.SECONDS.sleep(5);
            } catch (InterruptedException e) {
                e.printStackTrace();
            } finally {
                spinLock.myUnLock();
            }
        }, "T2").start();
        /*  T1.Lock() -- 2s后 -->  T2.Lock()开始自旋 、T1.sleep(3)
            --> T1.UnLock() --> T2.sleep(1) --> T2.UnLock()
        * T1 进来是拿到锁，是期望的null，变成thread，此时T1没有自旋，而是跳出了循环，
        * 这时候线程 T2 拿到锁后是 thread，进入while循环，开始自旋，
        * 等待 T1 解锁变回 null，T2才能退出while循环，走出自旋 	
        * T2线程必须等待T1线程Unlock后，才能Unlock，在这之前进行自旋等待
        * */
    }
}
```

### 4、死锁

![img](https://img2020.cnblogs.com/blog/1988929/202104/1988929-20210416215231782-236744017.png)

**解决问题**

1、使用`jps - l`定位进程号

![img](https://img2020.cnblogs.com/blog/1988929/202104/1988929-20210416215246297-658407278.png)

2、使用`jstack 进程号`找到死锁问题

![img](https://img2020.cnblogs.com/blog/1988929/202104/1988929-20210416215259568-1075354823.png)

![img](https://img2020.cnblogs.com/blog/1988929/202104/1988929-20210416215308375-267415779.png)

**面试，工作中！排查问题！**

**1、日志**

**2、堆栈信息**

