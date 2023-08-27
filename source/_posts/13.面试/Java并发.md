---
title: Java并发
date: 2021-11-22
keywords: keywords
description: description
tags:
  - 并发
  - JAVA
categories:
  - 面试
---

## JUC 包中的原子类是哪4类?

1. 基本类型：使用原子的方式更新基本类型
- AtomicInteger:整形原子类 
-  AtomicLong:长整型原子类
- AtomicBoolean :布尔型原子类

2. 数组类型：使用原子的方式更新数组里的某个元素
- AtomicIntegerArray:整形数组原子类 
- AtomicLongArray:长整形数组原子类
- AtomicReferenceArray :引用类型数组原子类

3. 引用类型：
- AtomicReference:引用类型原子类 
- AtomicStampedRerence:原子更新引用类型里的字段原子类
- AtomicMarkableReference :原子更新带有标记位的引用类型

4. 对象的属性修改类型 ：
- AtomicIntegerFieldUpdater:原子更新整形字段的更新器
- AtomicLongFieldUpdater:原子更新长整形字段的更新器 
- AtomicStampedReference :原子更新带有版本号的引用类型。该类将整数值与引用关联起来，可用于解决原 子的更新数据和数据的版本号，可以解决使用 CAS 进行原子更新时可能出现的 ABA 问题。

## 同步和异步、并发和并行

> 同步和异步通常用来形容一次方法调用。
- 同步方法：调用一旦开始，调用者必须等到方法调用返回后，才能继续后续的行为。
- 异步方法：调用更像一个消息传递，一旦开始，方法调用就会立即返回，调用者就可以继续后续的操作

> 并发和并行是两个非常容易被混淆的概念。
- 并发：偏重于多个任务交替执行，而多个任务之间有可能还是串行的。
- 并行：是真正意义上的“同时执行


## 竞态条件，临界区，阻塞（Blocking）和非阻塞（Non-Blocking），死锁（Deadlock）、饥饿（Starvation）和活锁（Livelock）

- 竞态条件：当两个线程竞争同一资源时，如果对资源的访问顺序敏感，就称存在竞态条件。
- 临界区：用来表示一种公共资源或者说是共享数据，可以被多个线程使用。但是每一次，只能有一个线程使用它，一旦临界区资源被占用，其他线程要想使用这个资源，就必须等待。
- 饥饿是指某一个或者多个线程因为种种原因无法获得所需要的资源，导致一直无法执行。比如它的线程优先级可能太低，而高优先级的线程不断抢占它需要的资源，导致低优先级线程无法工作。
- 如果线程的智力不够，且都秉承着“谦让”的原则，主动将资源释放给他人使用，那么就会出现资源不断在两个线程中跳动，而没有一个线程可以同时拿到所有资源而正常执行。这种情况就是活锁。

## 并发级别

- 阻塞：一个线程是阻塞的，那么在其他线程释放资源之前，当前线程无法继续执行。当我们使用synchronized关键字，或者重入锁时，我门得倒的就是阻塞线程。无论是synchronized或者重入锁，都会试图在执行后续代码前，得到临界区的锁，如果得不到，线程就会被挂起等待，直到占有了所需资源为止。
- 无饥饿：如果线程之间是有优先级的，那么线程调度的时候总是会倾向于满足高优先级的线程。也就说是，对于同一个资源的分配，是不公平的！对于非公平的锁来说，系统允许高优先级的线程插队。这样有可能导致低优先级线程产生饥饿。但如果锁是公平的，满足先来后到，那么饥饿就不会产生，不管新来的线程优先级多高，要想获得资源，就必须乖乖排队。那么所有的线程都有机会执行。
- 无障碍：无障碍是一种最弱的非阻塞调度。两个线程如果是无障碍的执行，那么他们不会因为临界区的问题导致一方被挂起。换言之，大家都可以大摇大摆地进入临界区了。那么如果大家一起修改共享数据，把数据改坏了可怎么办呢？对于无障碍的线程来说，一旦检测到这种情况，它就会立即对自己所做的修改进行回滚，确保数据安全。但如果没有数据竞争发生，那么线程就可以顺利完成自己的工作，走出临界区。
- 无锁：无锁的并行都是无障碍的。在无锁的情况下，所有的线程都能尝试对临界区进行访问，但不同的是，无锁的并发保证必然有一个线程能够在有限步内完成操作离开临界区。

下面就是一段无锁的示意代码，如果修改不成功，那么循环永远不会停止。

```java
while (!atomicVar.compareAndSet(localVar, localVar+1)) {
      localVar = atomicVar.get();
}
```

- 无等待：无锁只要求有一个线程可以在有限步内完成操作，而无等待则在无锁的基础上更进一步进行扩展。它要求所有的线程都必须在有限步内完成，这样就不会引起饥饿问题。如果限制这个步骤上限，还可以进一步分解为有界无等待和线程数无关的无等待几种，它们之间的区别只是对循环次数的限制不同。

## 说一下 runnable 和 callable 有什么区别
相同点：
- 都是接口
- 都可以编写多线程程序
- 都采用Thread.start()启动线程

区别：
- Runnable 接口 run 方法无返回值；Callable 接口 call 方法有返回值，是个泛型，和Future、FutureTask配合可以用来获取异步执行的结果
- Runnable 接口 run 方法只能抛出运行时异常，且无法捕获处理；Callable 接口 call 方法允许抛出异常，可以获取异常信息
注：Callalbe接口支持返回执行结果，需要调用FutureTask.get()得到，此方法会阻塞主进程的继续往下执行，如果不调用不会阻塞。

## 线程的 run()和 start()有什么区别？

- 每个线程都是通过某个特定Thread对象所对应的方法run()来完成其操作的，run()方法称为线程体。通过调用Thread类的start()方法来启动一个线程。
- start() 方法用于启动线程，run() 方法用于执行线程的运行时代码。run() 可以重复调用，而 start() 只能调用一次。
- start()方法来启动一个线程，真正实现了多线程运行。调用start()方法无需等待run方法体代码执行完毕，可以直接继续执行其他的代码； 此时线程是处于就绪状态，并没有运行。 然后通过此Thread类调用方法run()来完成其运行状态， run()方法运行结束， 此线程终止。然后CPU再调度其它线程。

- run()方法是在本线程里的，只是线程里的一个函数，而不是多线程的。 如果直接调用run()，其实就相当于是调用了一个普通函数而已，直接待用run()方法必须等待run()方法执行完毕才能执行下面的代码，所以执行路径还是只有一条，根本就没有线程的特征，所以在多线程执行时要使用start()方法而不是run()方法。

## 为什么我们调用 start() 方法时会执行 run() 方法，为什么我们不能直接调用 run() 方法？

  这是另一个非常经典的 java 多线程面试问题，而且在面试中会经常被问到。很简单，但是很多人都会答不上来！

  new 一个 Thread，线程进入了新建状态。调用 start() 方法，会启动一个线程并使线程进入了就绪状态，当分配到时间片后就可以开始运行了。 start() 会执行线程的相应准备工作，然后自动执行 run() 方法的内容，这是真正的多线程工作。

  而直接执行 run() 方法，会把 run 方法当成一个 main 线程下的普通方法去执行，并不会在某个线程中执行它，所以这并不是多线程工作。

  总结： 调用 start 方法方可启动线程并使线程进入就绪状态，而 run 方法只是 thread 的一个普通方法调用，还是在主线程里执行。

## 什么是 Callable 和 Future?

- Callable 接口类似于 Runnable，从名字就可以看出来了，但是 Runnable 不会返回结果，并且无法抛出返回结果的异常，而 Callable 功能更强大一些，被线程执行后，可以返回值，这个返回值可以被 Future 拿到，也就是说，Future 可以拿到异步执行任务的返回值。

- Future 接口表示异步任务，是一个可能还没有完成的异步任务的结果。所以说 Callable用于产生结果，Future 用于获取结果。

## CountDownLatch

![image](http://java-run-blog.oss-cn-zhangjiakou.aliyuncs.com/e314e760c9564beb8b9df1e0c5238ce8.png)

一个非常实用的多线程控制工具类。“Count Down”在英文中意为倒计数，Latch为门闩的意思。如果翻译成为倒计数门闩，我想大家都会觉得不知所云吧！因此，这里简单地称之为倒计数器。在这里，门闩的含义是：把门锁起来，不让里面的线程跑出来。因此，这个工具通常用来控制线程等待，它可以让某一个线程等待直到倒计时结束，再开始执行。

```java
import java.util.concurrent.CountDownLatch;

public class CountDownLatchDemo2 extends Thread {

  public static void main(String[] args) {
    CountDownLatch countDownLatch=new CountDownLatch(100);
    for (int i = 0; i < 100; i++) {
      new CountDownLatchDemo2().start();
      countDownLatch.countDown();
    }
  }

  @Override
  public void run() {
    try {
      System.out.println("---------子线程"+Thread.currentThread().getName()+"正在执行");
      Thread.sleep(3000);
      System.out.println("----over-------子线程"+Thread.currentThread().getName()+"执行完毕");
    } catch (InterruptedException e) {
      e.printStackTrace();
    }
  }
}
```

主线程运行，一直等到CountLatch的所有线程都到达才继续往下执行。

countDownLatch.countDown();不断往下减少，一直为0时。表明所有执行完成。



## CyclicBarrier

![image](http://java-run-blog.oss-cn-zhangjiakou.aliyuncs.com/99b3f2c3d4a54365910c1771484d1472.png)

​	CyclicBarrier可以理解为循环栅栏。栅栏就是一种障碍物，比如，通常在私人宅邸的周围就可以围上一圈栅栏，阻止闲杂人等入内。这里当然就是用来阻止线程继续执行，要求线程在栅栏处等待。前面Cyclic意为循环，也就是说这个计数器可以反复使用。比如，假设我们将计数器设置为10，那么凑齐第一批10个线程后，计数器就会归零，然后接着凑齐下一批10个线程，这就是循环栅栏内在的含义。

```java
import java.util.concurrent.CyclicBarrier;

public class CycliBarrierDemo extends Thread{
    @Override
    public void run() {
        System.out.println("开始进行数据分析");
    }
    //循环屏障
    //可以使得一组线程达到一个同步点之前阻塞.
    public static void main(String[] args) {
        CyclicBarrier cyclicBarrier=new CyclicBarrier
                (3,new CycliBarrierDemo());
        new Thread(new DataImportThread(cyclicBarrier,"file1")).start();
        new Thread(new DataImportThread(cyclicBarrier,"file2")).start();
        new Thread(new DataImportThread(cyclicBarrier,"file3")).start();
    }
}

import java.util.concurrent.BrokenBarrierException;
import java.util.concurrent.CyclicBarrier;

public class DataImportThread extends Thread{
    private CyclicBarrier cyclicBarrier;
    private String path;
    public DataImportThread(CyclicBarrier cyclicBarrier, String path) {
        this.cyclicBarrier = cyclicBarrier;
        this.path = path;
    }
    @Override
    public void run() {
        System.out.println("开始导入："+path+" 数据");
        //TODO
        try {
            cyclicBarrier.await(); //阻塞 condition.await()
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (BrokenBarrierException e) {
            e.printStackTrace();
        }
    }
}
```

## 为什么使用线程池？（优点）

- 降低资源消耗。 通过重复利用已创建的线程降低线程创建和销毁造成的消耗。
- 提高响应速度。 当任务到达时，任务可以不需要的等到线程创建就能立即执行。
- 提高线程的可管理性。 线程是稀缺资源，如果无限制的创建，不仅会消耗系统资源，还会降低系统的稳定性， 使用线程池可以进行统一的分配，调优和监控。

## 线程池缺点

- 设计更加复杂：
- 上下文切换的开销：
- 增加资源消耗：

## Executor

- newFixedThreadPool()方法：该方法返回一个固定线程数量的线程池。该线程池中的线程数量始终不变。当有一个新的任务提交时，线程池中若有空闲线程，则立即执行。若没有，则新的任务会被暂存在一个任务队列中，待有线程空闲时，便处理在任务队列中的任务。
- newSingleThreadExecutor()方法：该方法返回一个只有一个线程的线程池。若多余一个任务被提交到该线程池，任务会被保存在一个任务队列中，待线程空闲，按先入先出的顺序执行队列中的任务。
- newCachedThreadPool()方法：该方法返回一个可根据实际情况调整线程数量的线程池。线程池的线程数量不确定，但若有空闲线程可以复用，则会优先使用可复用的线程。若所有线程均在工作，又有新的任务提交，则会创建新的线程。处理任务。所有线程在当前任务执行完毕后，将返回线程池进行复用。
- newSingleThreadScheduledExecutor()方法：该方法返回一个ScheduledExecutorService对象，线程池大小为1。ScheduledExecutorService接口在ExecutorService接口之上扩展了在给定时间执行某任务的功能，如在某个固定的延时之后执行，或者周期性执行某个任务。
- newScheduledThreadPool()方法：该方法也返回一个ScheduledExecutorService对象，但该线程池可以指定线程数量。

## 线程池参数

- corePoolSize：指定了线程池中的线程数量。
- maximumPoolSize：指定了线程池中的最大线程数量。
- keepAliveTime：当线程池线程数量超过corePoolSize时，多余的空闲线程的存活时间。即，超过corePoolSize的空闲线程，在多长时间内，会被销毁。
- unit：keepAliveTime的单位。
- workQueue：任务队列，被提交但尚未被执行的任务。
- threadFactory：线程工厂，用于创建线程，一般用默认的即可。
- handler：拒绝策略。当任务太多来不及处理，如何拒绝任务。

## 线程池拒绝策略

- AbortPolicy策略：该策略会直接抛出异常，阻止系统正常工作。
- CallerRunsPolicy策略：只要线程池未关闭，该策略直接在调用者线程中，运行当前被丢弃的任务。显然这样做不会真的丢弃任务，但是，任务提交线程的性能极有可能会急剧下降。
- DiscardOledestPolicy策略：该策略将丢弃最老的一个请求，也就是即将被执行的一个任务，并尝试再次提交当前任务。
- DiscardPolicy策略：该策略默默地丢弃无法处理的任务，不予任何处理。如果允许任务丢失，我觉得这可能是最好的一种方案了吧！
- 自己扩展、实现RejectedExecutionHandler接口

## 线程池执行流程

- 当线程池中线程数小于corePoolSize时，新提交任务将创建一个新线程执行任务，即使此时线程池中存在空闲线程。
- 当线程池中线程数达到corePoolSize时，新提交任务将被放入workQueue中，等待线程池中任务调度执行 。
- 当workQueue已满，且maximumPoolSize > corePoolSize时，新提交任务会创建新线程执行任务。
- 当workQueue已满，且提交任务数超过maximumPoolSize，任务由RejectedExecutionHandler处理。
- 当线程池中线程数超过corePoolSize，且超过这部分的空闲时间达到keepAliveTime时，回收这些线程。
- 当设置allowCoreThreadTimeOut(true)时，线程池中corePoolSize范围内的线程空闲时间达到keepAliveTime也将回收。

## 线程通信

- 通过共享对象通信

- 忙等待：（死循环监听）

  ```java
  while(!sharedSignal.hasDataToProcess()){
    //do nothing... busy waiting
  }
  
  ```

- wait(),notify()和notifyAll()
- 假唤醒

## ThreadLocal 是什么？
ThreadLocal 是一个本地线程副本变量工具类。主要用于将私有线程和该线程存放的副本对象做一个映射，各个线程之间的变量互不干扰，在高并发场景下，可以实现无状态的调用，适用于各个线程不共享变量值的操作。

##  ThreadLocal 工作原理是什么？
ThreadLocal 原理：每个线程的内部都维护了一个 ThreadLocalMap，它是一个 Map（key,value）数据格式，key 是一个弱引用，也就是 ThreadLocal 本身，而 value 存的是线程变量的值。

## ThreadLocal 如何解决 Hash 冲突？
与 HashMap 不同，ThreadLocalMap 结构非常简单，没有 next 引用，也就是说 ThreadLocalMap 中解决 Hash 冲突的方式并非链表的方式，而是采用线性探测的方式。所谓线性探测，就是根据初始 key 的 hashcode 值确定元素在 table 数组中的位置，如果发现这个位置上已经被其他的 key 值占用，则利用固定的算法寻找一定步长的下个位置，依次判断，直至找到能够存放的位置。

## ThreadLocal 的内存泄露是怎么回事？
ThreadLocal 在 ThreadLocalMap 中是以一个弱引用身份被 Entry 中的 Key 引用的，因此如果 ThreadLocal 没有外部强引用来引用它，那么 ThreadLocal 会在下次 JVM 垃圾收集时被回收。这个时候 Entry 中的 key 已经被回收，但是 value 又是一强引用不会被垃圾收集器回收，这样 ThreadLocal 的线程如果一直持续运行，value 就一直得不到回收，这样就会发生内存泄露。

## ThreadLocal如何避免内存泄露？
既然已经发现有内存泄露的隐患，自然有应对的策略，在调用ThreadLocal的get()、set()可能会清除ThreadLocalMap中key为null的Entry对象，这样对应的value就没有GC Roots可达了，下次GC的时候就可以被回收，当然如果调用remove方法，肯定会删除对应的Entry对象。

如果使用ThreadLocal的set方法之后，没有显示的调用remove方法，就有可能发生内存泄露，所以养成良好的编程习惯十分重要，使用完ThreadLocal之后，记得调用remove方法。

```java
  ThreadLocal<String> localName = new ThreadLocal();
  try {
      localName.set("小狼");
      // 其它业务逻辑
  } finally {
      localName.remove();
  }
```

## 为什么 ThreadLocalMap 的 key 是弱引用？

我们知道 ThreadLocalMap 中的 key 是弱引用，而 value 是强引用才会导致内存泄露的问题，至于为什么要这样设计，这样分为两种情况来讨论：

- key 使用强引用：这样会导致一个问题，引用的 ThreadLocal 的对象被回收了，但是 ThreadLocalMap 还持有 ThreadLocal 的强引用，如果没有手动删除，ThreadLocal 不会被回收，则会导致内存泄漏。
- key 使用弱引用：这样的话，引用的 ThreadLocal 的对象被回收了，由于 ThreadLocalMap 持有 ThreadLocal 的弱引用，即使没有手动删除，ThreadLocal 也会被回收。value 在下一次 ThreadLocalMap 调用 set、get、remove 的时候会被清除。

比较以上两种情况，我们可以发现：由于 ThreadLocalMap 的生命周期跟 Thread 一样长，如果都没有手动删除对应 key，都会导致内存泄漏，但是使用弱引用可以多一层保障，弱引用 ThreadLocal 不会内存泄漏，对应的 value 在下一次 ThreadLocalMap 调用 set、get、remove 的时候被清除，算是最优的解决方案。


## 说说自己是怎么使用 synchronized 关键字？

- 修饰实例方法：作用于当前对象实例加锁，进入同步代码前要获得当前对象实例的锁。

- 修饰静态方法：作用于当前类对象加锁，进入同步代码前要获得当前类对象的锁 。也就是给当前类加锁，会作 用于类的所有对象实例，因为静态成员不属于任何一个实例对象，是类成员( static 表明这是该类的一个静态 资源，不管new了多少个对象，只有一份，所以对该类的所有对象都加了锁)。所以如果一个线程A调用一个实 例对象的非静态 synchronized 方法，而线程B需要调用这个实例对象所属类的静态 synchronized 方法，是允 许的，不会发生互斥现象，因为访问静态 synchronized 方法占用的锁是当前类的锁，而访问非静态 synchronized 方法占用的锁是当前实例对象锁。 

- 修饰代码块，指定加锁对象：对给定对象加锁，进入同步代码库前要获得给定对象的锁。 和 synchronized 方 法一样，synchronized(this)代码块也是锁定当前对象的。synchronized 关键字加到 static 静态方法和 synchronized(class)代码块上都是是给 Class 类上锁。这里再提一下:synchronized关键字加到非 static 静态 方法上是给对象实例上锁。另外需要注意的是:尽量不要使用 synchronized(String a) 因为JVM中，字符串常量池具有缓冲功能!


## 讲一下 synchronized 关键字的底层原理？

synchronized 同步语句块的实现使用的是 monitorenter 和 monitorexit 指令，其中 monitorenter 指令指向同 步代码块的开始位置，monitorexit 指令则指明同步代码块的结束位置。 当执行 monitorenter 指令时，线程试图 获取锁也就是获取 monitor(monitor对象存在于每个Java对象的对象头中，synchronized 锁便是通过这种方式获取 锁的，也是为什么Java中任意对象可以作为锁的原因) 的持有权.当计数器为0则可以成功获取，获取后将锁计数器设 为1也就是加1。相应的在执行 monitorexit 指令后，将锁计数器设为0，表明锁被释放。如果获取对象锁失败，那当 前线程就要阻塞等待，直到锁被另外一个线程释放为止。

## 程序执行中出现异常，锁会释放吗?这会造成什么影响？怎样去避免？
当程序执行中出现异常，锁会释放，这可能造成代码执行到一半。

## 如何停止一个线程

1. 使用退出标志，使线程正常退出，也就是当run方法完成后线程终止。
2. 使用stop方法强行终止，但是不推荐这个方法，因为stop和suspend及resume一样都是过期作废的方法。
3. 使用interrupt方法中断线程。


## 什么是零拷贝

> 零拷贝就是一种避免 CPU 将数据从一块存储拷贝到另外一块存储的技术。针对操作系统中的设备驱动程序、文件系统以及网络协议堆栈而出现的各种零拷贝技术极大地提升了特定应用程序的性能，并且使得这些应用程序可以更加有效地利用系统资源。这种性能的提升就是通过在数据拷贝进行的同时，允许 CPU 执行其他的任务来实现的。

![拷贝](https://java-run-blog.oss-cn-zhangjiakou.aliyuncs.com/blog/ZleZmm.png)

> 从上图中可以看出，共产生了四次数据拷贝，即使使用了DMA来处理了与硬件的通讯，CPU仍然需要处理两次数据拷贝，与此同时，在用户态与内核态也发生了多次上下文切换，无疑也加重了CPU负担。

 
