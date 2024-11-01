# Java并发面试题

## 线程

### 什么是线程和进程？

**进程**是计算机中的程序在数据集合上的一次运行活动，是系统进行资源分配和调度的基本单位，是程序的实体。

**线程**是轻量级的进程，是程序执行的最小单位，线程只拥有进程中的一部分资源因此切换和调度的成本远远小于进程。

### 如何创建线程？

1. **继承Thread类**。
   
   继承Thread类并重写其中的run方法。

2. **实现Runnable接口**。
   
   实现Runnable接口并重写其中的run方法。
   
   实际使用时以Runnable为参数构造新的Thread对象。

3. **实现Callable接口**。
   
   与Runnable类似，不过能拿到返回值。

4. **使用线程池**。

### wait和sleep方法的区别

Thread.sleep()方法会让当前线程休眠若干时间。

obj.wait()方法会让当前线程在这个对象上等待，直到其他线程调用了obj.notify()方法为止。

### 可以直接调用Thread类的run方法吗？

run方法调用Thread类中Runnable对象的run方法执行，只会在当前线程中串行执行run()中的代码。

Thread类中的start方法会新建一个线程并让这个线程执行run方法。

## volatile关键字

### 如何保证变量可见性

使用**volatile**声明一个变量，等于告诉虚拟机这个变量极有可能被某些程序或线程修改。为了确保变量被修改后，应用程序范围内的所有线程都能够看到这个改动，虚拟机必须采取一些特殊的手段，保证这个变量的可见性。

**volatile**在对变量进行读写操作时，会插入特定的内存屏障，强制变量在主存而不是线程本地内存中读取数据，从而禁止指令重排序。

## 乐观锁与悲观锁

### CAS是怎么实现的

CAS包含三个参数CAS（V，E，N），V表示要更新的变量，E表示预期值，N表示新值。仅当V值等于E值时，才会将V的值设为N，如果V值和E值不同，则说明已经有其他线程做了更新，则当前线程什么都不做。

### CAS有哪些问题

1. **ABA问题**。如果对象的值被其他线程连续修改两次，经过这两次修改后，对象的值又恢复为原值，这样当前线程就无法正确判断这个对象是否被修改过。

2. **循环开销大**。CAS会用到自旋操作来进行重试，如果长时间不成功会给CPU带来非常大的执行开销。

3. **只能保证一个共享变量的原子操作**。

### 如何使用synchronized？

1. **指定加锁对象**。进入同步代码前要获得给定对象的锁。

2. **直接作用于实例方法**。对当前实例加锁，进入同步代码前要获得当前实例的锁。

3. **直接作用于静态方法**。对当前类加锁，进入同步代码前要获得当前类的锁。

### synchronized底层原理

synchronized同步代码块使用的是**monitorenter**和**monitorexit**，分别指向同步代码块的开始位置和结束位置。

在执行**monitorenter**时，会尝试获取对象的锁，如果锁的计数器为0则表示锁可以获取，获取后将锁计数器设1。

对象锁的拥有者线程可以执行**monitorexit**指令释放锁，执行后锁计数器设为0，表明锁被释放。

### synchronized锁升级原理

（复杂，面试很少问到）

### synchronized和ReentrantLock有什么区别

1. **等待可中断**。线程在等待锁的过程中，可以根据需要取消对锁的请求执行其他功能。

2. **可实现公平锁**。ReentrantLock可以指定是公平锁或非公平锁，而synchronized只能是非公平锁。

### AQS

## ThreadLocal

### ThreadLocal在哪里能用到？

### ThreadLocal原理了解吗？

ThreadLocal是一个线程的局部变量，是只有当前线程可以访问到的数据。

ThreadLocal的**set**方法首先获得当前线程对象，然后通过getMap拿到线程的ThreadLocalMap并将值放入ThreadLocalMap中。ThreadLocalMap的key为ThreadLocal对象，value是我们所需的值。

**get**方法获取线程的ThreadL对象，将自己作为key取得内部的实际数据。

### ThreadLocal内存泄漏是怎么导致的？

ThreadLocalMap的key使用了弱引用，当线程池中的线程执行完与ThreadLocal相关的代码时，key只存在弱引用，在垃圾回收时key会被清理掉，而value不会被清理。

这样，ThreadLocalMap中会出现key为null的entry，如果不做任何措施value永远无法被GC回收。

### 如何避免内存泄漏

使用ThreadLocal.remove()方法将这个变量移除

## 线程池

### 线程池的常见参数有哪些？

+ **corePoolSize**：线程池中的线程数量。

+ **maximumPoolSize**：线程池中最大线程数量。

+ **workQueue**：任务队列，提交但尚未被执行的任务。

+ **handler**：拒绝策略。

+ keepAliveTime：当前线程池数量超过最大值时，多余空闲线程的存活时间。

+ unit：keepAliveTime的单位。

+ threadFactory：线程工厂。

### 线程池中的队列类型有哪些？

+ **SynchronousQueue**：SynchronousQueue的队列长度为0，一个线程往队列放数据的时候必须等待另一个线程从队列中取走数据。

+ **ArrayBlockingQueue**：若实际线程数小于corePoolSize，则优先创建新的线程，若大于corePoolSize，则将新任务加入等待队列，若大于maximumPoolSize，则执行拒绝策略。

+ **LinkedBlockingQueue**：若实际线程数小于corePoolSize，则优先创建新的线程，若大于corePoolSize，则将新任务加入等待队列。

+ **PriorityBlockingQueue**：是一种特殊的无界队列，可以根据任务的优先级顺序先后执行。

### 线程池的拒绝策略有哪些？

+ **AbortPolicy**：该策略会直接抛出异常。

+ **CallerRunsPolicy**：该策略直接在调用者线程中进行被丢弃的任务。

+ **DiscardOledestPolicy**：该策略丢弃队列中最老的一个任务，并尝试再次提交当前任务。

+ **DiscardPolicy**：默默丢弃无法处理的任务。

### 如何创建线程池？

通过ThreadPoolExecutor来创建

### 使用execute和submit提交任务的区别

+ 使用execute()提交任务，如果在执行过程中抛出异常，且这个异常没有在任务内捕获，那么该异常会导致当前线程终止。

+ 使用submit()提交任务，如果在任务执行中发生异常，这个异常会被封装在由submit()返回的Future对象中，当调用Future.get()时会捕获一个ExecutionException。
