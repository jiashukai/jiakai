# java 并发编程相关面试题

### 在java中守护线程和本地线程的区别

java 中的线程分为两种： 守护线程和用户线程

任何线程都可以设置为守护线程和用户线程，通过方法Thread.setDaemon(boolean on);true 则把该线程设置为守护线程，反之则设置为用户线程。Thread.setDaemon()必须在Thread.start()之前调用，否则会抛出异常。

**区别**

唯一的区别判断虚拟机何时离开，守护线程是为其他线程提供服务的，如果全部的User Thread已经撤离，守护线程没有课服务的线程，JVM会自动撤离。也可以理解为守护线程是JVM自动创建的线程（但不一定），用户线程是程序创建的线程；比如JVM的垃圾回收线程是一个守护线程，当所有线程已经撤离，不再产生垃圾，守护线程自然就没事可干了，当垃圾回收线程是Java虚拟机上仅剩的线程时，Java虚拟机会自动离开。

守护线程：所谓守护线程，是指在程序运行的时候在后台提供一种通用服务的线程，比如垃圾回收线程就是一个典型的守护线程，并且这种线程并不属于程序中不可或缺的部分。因此， 当所有的非守护线程结束时，程序也就终止了，同时会杀死进程中的所有守护线程。反过来说，只要任何非守护线程还在运行，程序就不会终止。

用户线程：Java虚拟机在它所有非守护线程已经离开后自动离开。

守护线程和非守护线程并没有什么区别，他们唯一的区别就是虚拟机离开时的状态：
1. 当所有的非守护线程结束时，程序也就终止了，同时会杀死进程中所有的守护线程
2. 守护线程是用来服务用户线程的，如果没有其他用户线程在运行，那么久没有可服务对象，也没有理由继续下去

###  线程与进程的区别

进程： 是并发执行的程序在执行过程中分配和管理资源的基本单位，是一个动态概念，是操作系统分配资源的最小单元。
线程： 是进行的一个执行单元。比进程更小的独立运行的基本单位。线程也被称为轻量级进程。是操作系统调度的最小单元。

一个程序至少有一个进程，一个进程至少有一个线程

线程的执行过程是是线性的，尽管会发生中断或则暂停，但是进程所拥有的资源只为该线性执行过程服务，一旦发生线程切换，这些资源需要被保护起来

进程分为单线程进程和多线程进程，单线程进程宏观来看也是线性执行过程，微观上只有单一的执行过程。多线程进程宏观是线性的，微观上多个执行操作

线程的改变只代表CPU的执行过程的改变，而没有发生进程所拥有资源的变化

**区别**
1. 地址空间：同一个进程的线程共享本进程的地址空间，而进程之间则是独立的地址空间
2. 资源拥有：同一进程内的线程共享本进程的资源如内存，I/O，cpu等，但是进程之间的资源是独立的。
      一个进程崩溃后，在保护模式下不会对其他进程产生影响，但是一个线程崩溃后整个进程都死掉了，所以多进程要比多线程健壮。

      进程切换时，消耗的资源大，效率高。所以涉及到频繁的切换时，使用线程要好于进程
3. 执行过程：每个独立的进程有一个程序运行的入口，顺序执行序列。但是线程不能独立执行，必须依存在应用程序中，由应用程序提供多个线程执行控制
4. 线程是处理器调度的进本单元，但是进程不是
5. 两者均可并发执行

**优缺点：**
线程执行开销小，但是不利于资源的管理和保护

进程执行开销大，但是能够很好的进行资源管理和保护

### 什么叫线程安全？ servlet是线程安全吗？

线程安全是编程中的术语，指某个函数，函数库在多线程环境中被调用时，能够正确地处理多个线程之间的共享变量，使程序功能正确完成

Servlet不是线程安全的，servlet是单实例多线程的，当多个线程同时访问同一个方法，是不能保证共享变量的线程安全性的

Struts2的action是多实例多线程，是线程安全的，每个请求过来都会new一个新的action分配给这个请求，请求完成后销毁。

SpringMVC的Controller是不是线程安全

Struts2好处是不用考虑线程安全问题；Servlet和SpringMVC需要考虑线程安全问题，但是性能可以提升不用处理太多的gc，课使用ThreadLocal来处理多线程问题

###  如何确保线程安全

确保线程安全的方法有几个：竞争与原子操作，同步与锁，可重入，过度优化

**竞争与原子操作**

多个线程同时访问和修改一个数据，可能造成很严重的后果。出现严重后果的原因是很多操作被操作系统编译为汇编代码之后不止一条指令，因此在执行的时候可能执行了一半就被调度系统打断了而去执行别的代码了。一般将单指令的操作称为原子的(Atomic)，因为不管怎样，单条指令的执行是不会被打断的。

**同步与锁**

为了避免多个线程同时读写一个数据而产生不可预料的后果，开发人员要将各个线程对同一个数据的访问同步，也就是说，在一个线程访问数据未结束的时候，其他线程不得对同一个数据进行访问。

同步的最常用的方法是使用锁(Lock)，它是一种非强制机制，每个线程在访问数据或资源之前首先试图获取锁，并在访问结束之后释放锁；在锁已经被占用的时候试图获取锁时，线程会等待，直到锁重新可用。

**可重入**

一个函数被重入，表示这个函数没有执行完成，但由于外部因素或内部因素，又一次进入该函数执行。一个函数称为可重入的，表明该函数被重入之后不会产生任何不良后果。可重入是并发安全的强力保障，一个可重入的函数可以在多线程环境下放心使用。

**过度优化**

我们可以使用volatile关键字试图阻止过度优化，它可以做两件事：第一，阻止编译器为了提高速度将一个变量缓存到寄存器而不写回；第二，阻止编译器调整操作volatile变量的指令顺序。

在另一种情况下，CPU的乱序执行让多线程安全保障的努力变得很困难，通常的解决办法是调用CPU提供的一条常被称作barrier的指令，它会阻止CPU将该指令之前的指令交换到barrier之后，反之亦然。
java中 可以使用同步（synchronized），使用原子类（atomic concurrent classes），实现并发锁，使用volatile关键字，使用不变类和线程安全类

### 什么是多线程中的上下文切换

多线程会共同使用一组计算机上的CPU，而线程数量大于给程序分配的CPU数量时，为了让哥哥线程都有执行的机会，就需要轮转使用CPU。不同的线程切换使用CPU发生的切花数据等就是上下文切换。

在上下文切换过程中，CPU会停止处理当前运行的程序，并保存当前程序运行的具体位置之后继续运行。上下文切换时存储和恢复CPU状态的过程，它使得线程执行能够从中断点恢复执行。上下文切换时多任务操作系统和多线程环境的基本特征。

### Java中有几种方法可以实现一个线程
1. 继承Thread类，实现run方法
2. 实现Runnable接口
3. 实现Callable接口，需要实现的是call()方法
4. 从线程池中获取一个

### 如何停止一个正在运行的线程

1. 使用共享变量的方式。在这种方式中，之所以引入共享变量，是因为该变量可以被多个执行相同任务的线程用来作为是否中断的信号，通知线程中断。
2. 使用interrupt方法终止线程

Thread.interrupt()的作用是通知线程应该中断了，到底中断还是继续执行，应该由被通知的线程自己处理，当对一个线程，调用interrupt()时
1. 如果线程处于被阻塞状态(例如处于sleep，wait，join等状态)，那么线程将立即退出被阻塞状态，并抛出一个interruptedException异常
2. 如果线程处于正常活动状态，那么会将该线程的中断标志设置为true，被设置中断标志的线程将继续正常运行，不受影响

这里再说一下interrupted()，贴一下jdk1.8源码的方法注释，当调用这个方法会将中断标志重置，如果线程继续执行，下一次调用这个方法返回为false，除非再次调用interrupt()方法设置一次。简单来说就是一次interrupt()只对应一次interrupted()的true状态

### 可以直接调用Thread类的run()方法么

可以，但是如果我们调用了Thread的run()方法，他就会和普通的方法一样，会在当前线程中执行，为了在新的线程中执行我们得代码，必须使用Thread.start()方法

###  线程的六种状态
1. 初始态：new
      创建一个Thread对象，但还未调用start()启动线程时，线程处于初始态
2. 运行态：RUNNING
    在java中运行态包括就绪态和运行态

    运行态：获得CPU执行权，正在执行的线程，由于一个CPU同一时刻只能执行一条线程，因此每个CPU每个时刻只有一条运行态的线程。

    就绪态:runnable 该状态下的线程已经获得执行所需的所有资源，只要CPU分配执行权就能运行。所有就绪态的线程存放在就绪队列中

3. 阻塞态：BLOKED

    当一条正在执行的线程请求某一资源失败时，就会进入阻塞态。而在java中，阻塞态专指请求锁失败时进入的状态。由一个阻塞队列存放所有阻塞态的线程。处于阻塞态的线程会不断请求资源，一旦请求成功，就会进入就绪队列，等待执行。锁，IO，Socket等都是资源

4. 等待态：WAITING

   当前线程中调用wait、join、park函数时，当前线程就会进入等待态。也有一个等待队列存放所有等待态的线程。线程处于等待态表示它需要等待其他线程的指示才能继续运行。进入等待态的线程会释放CPU执行权，并释放资源（如：锁）

5. 超时等待

   当运行中的线程调用sleep(time)、wait、join、parkNanos、parkUntil时，就会进入该状态；它和等待态一样，并不是因为请求不到资源，而是主动进入，并且进入后需要其他线程唤醒；进入该状态后释放CPU执行权 和 占有的资源。与等待态的区别：到了超时时间后自动进入阻塞队列，开始竞争锁。

6. 终止态

   线程结束后的状态

   线程状态转换中几个需要注意的点

   1、wait()方法会释放CPU执行权 和 占有的锁。

   2、sleep(long)方法仅释放CPU使用权，锁仍然占用；线程被放入超时等待队列，与yield相比，它会使线程较长时间得不到运行。

   3、yield()方法仅释放CPU执行权，锁仍然占用，线程会被放入就绪队列，会在短时间内再次执行。

  4、wait和notify必须配套使用，即必须使用同一把锁调用；

  5、wait和notify必须放在一个同步块中调用wait和notify的对象必须是他们所处同步块的锁对象。

###  线程优先级

每一个线程都是有优先级的，一般来说，高优先级的线程在运行时会具有优先权，但这依赖于线程调度的实现，这个实现是和操作系统相关的(OS dependent)。我们可以定义线程的优先级，但是这并不能保证高优先级的线程会在低优先级的线程前执行。线程优先级是一个int变量(从1-10)，1代表最低优先级，10代表最高优先级。

Java的线程优先级调度会委托给操作系统去处理，所以与具体的操作系统优先级有关，如非特别需要，一般无需设置线程优先级

### 什么是线程调度器和时间分片

线程调度器是一个操作系统服务，它负责为Runnable状态的线程分配CPU时间。一旦我们创建一个线程并启动它，它的执行便依赖于线程调度器的实现

线程调度并不受java虚拟机控制，所以由应用程序来控制它是更好的选择

时间分片是指将可用的CPU时间分配给可用的Runnable线程的过程。分配CPU市价可以基于线程优先级或则线程等待的时间

### 你如何确保main()方法所在的线程是Java 程序最后结束的线程？

我们可以使用Thread类的join()方法来确保所有程序创建的线程在main()方法退出前结束。

1.jvm会在所有的非守护线程（用户线程）执行完毕后退出；

2、main线程是用户线程；

3、仅有main线程一个用户线程执行完毕，不能决定jvm是否退出，也即是说main线程并不一定是最后一个退出的线程。

所以如果需要确保main方法所在的线程是jvm中最后结束的线程，这里就需要用到thread类的join()方法：
在一个线程中启动另外一个线程的join方法，当前线程将会挂起，而执行被启动的线程，知道被启动的线程执行完毕后，当前线程才开始执行。

###  为什么 Thread类的sleep()和yield()方法是静态的？

Thread类的sleep()和yield()方法将在当前执行的线程上运行。所以在其他处于等待状态的线程上调用这些方法是没有意义的。

sleep()方法和yield()方法都是Thread类的静态方法，都会使当前处于运行状态的线程放弃CPU，把运行机会让给别的线程。两者的区别在于：

sleep()方法会给其他线程运行的机会，不考虑其他线程的优先级，因此会给较低优先级线程一个运行的机会；yield()方法只会给相同优先级或者更高优先级的线程一个运行的机会。

当线程执行了sleep(long millis)方法，将转到阻塞状态，参数millis指定睡眠时间；当线程执行了yield()方法，将转到就绪状态。

sleep()方法声明抛出InterruptedException异常，而yield()方法没有声明抛出任何异常。

###  notify()和notifyAll()有什么区别？

当一个线程进入wait之后，就必须等其他线程notify/notifyall,使用notifyall,可以唤醒所有处于wait状态的线程，使其重新进入锁的争夺队列中，而notify只能唤醒一个。如果没把握，建议notifyAll，防止notigy因为信号丢失而造成程序异常。

### 为什么wait, notify 和 notifyAll这些方法不在Thread类里面？

一个很明显的原因是Java提供的锁是对象级的而不是线程级的，每个对象都有锁，通过线程获得。由于wait，notify和notifyAll都是锁级别的操作，所以把他们定义在Object类中因为锁属于对象。

### 同步方法和同步块，哪个是更好的选择？

同步块是更好的选择，因为它不会锁住整个对象（当然你也可以让它锁住整个对象）。同步方法会锁住整个对象，哪怕这个类中有多个不相关联的同步块，这通常会导致他们停止执行并需要等待获得这个对象上的锁。

同步块更要符合开放调用的原则，只在需要锁住的代码块锁住相应的对象，这样从侧面来说也可以避免死锁。

###  为什么你应该在循环中检查等待条件？

处于等待状态的线程可能会受到错误警报和伪唤醒，如果不在循环中检查等待条件，程序就会在没有满足结束条件的情况下退出。

### java中wait和sleep方法的不同？

最大的不同是在等待时wait会释放锁，而sleep一直持有锁。wait通常被用于线程间交互，sleep通常被用于暂停执行。

其此：sleep是线程Thread中的方法，而wait是对象中的方法

java通过  中断  和 共享变量  来实现多个线程之间的通讯和协作

###  如何在两个线程间共享变量

在两个线程间共享变量即可实现共享。一般来说，共享变量要求变量本身是线程安全的，然后再线程内使用的时候，如果有对共享变量的复合操作，name也得保证复合操作的线程安全性。

###  ThreadLocal

#### ThreadLocal的简单介绍
  ThreadLocal是线程内部的数据存储类，可以使用ThreadLocal在指定的线程中存储数据，而存储后的数据也只有在这个指定的线程中才能够获取到，其他线程则无法过去

#### 涉及到的几个重要类
1. Thread: 一个Thread代表一个线程
2. ThreadLocal：
3. ThreadLocalmap:可以看成一盒HashMap，但是它本身具体的实现并没有继承HashMap甚至跟java.util.map都没有关系。只是内部的实现跟hashMap类似
4. ThreadLocalMap.Entry:把它看成是保存键值对的对象，其本质上是一个weakReference<ThreadLocal>对象

#### ThreadLocal的简单使用
1. 创建一个泛型为String的ThreadLocal


      `val threadLocal = ThreadLocal<String>()`
2. 在指定线程中存储值并取出值
```
threadLocal.set("MainThreadValue")
        Log.e(TAG, "${Thread.currentThread().name} : threadLocal = " + threadLocal.get())

        Thread(Runnable {
            threadLocal.set("Thread1Value")
            Log.e(TAG,"${Thread.currentThread().name} : threadLocal = " + threadLocal.get())
        },"Thread1").start()

        Thread(Runnable {
            Log.e(TAG,"${Thread.currentThread().name} : threadLocal = " + threadLocal.get())
        },"Thread2").start()
```

输出时可以发现在不同线程中访问的是同一个ThreadLocal对象，但是在不同的线程中获取的值却是不一样的。

#### ThreadLocal的源码分析
```
public class ThreadLocal<T>
```
ThreadLocal是一个泛型类，所以我们可以在ThreadLocal中存储任意类型的值，它的核心方法有3个：get(),set(T value),remove().

**1.set()方法**
```
public void set(T value) {
       Thread t = Thread.currentThread();
       ThreadLocalMap map = getMap(t);
       if (map != null)
           map.set(this, value);
       else
           createMap(t, value);
}

ThreadLocalMap getMap(Thread t) {
           return t.threadLocals;
}

void createMap(Thread t, T firstValue) {
          t.threadLocals = new ThreadLocalMap(this, firstValue);
}       
```
根据当前所在线程getMap(t)获取ThreadLocal对象，如果ThreadLocalMap ！= null就将需要保存的值以<key,value>的形式保存，key是ThreadLocal实例，value是传入的参数value，ThreadLocalMap == null就创建ThreadLocalMap对象，将对象赋值给Thread实例的ThreadLocals属性，并将值保存。我们发现数据的存储于ThreadLocalMap有很大关系，那很自然的数据的获取也与ThreadLocalMap有关

**ThreadLocalMap**
```
ThreadLocalMap(ThreadLocal<?> firstKey, Object firstValue) {
    table = new Entry[INITIAL_CAPACITY];
    int i = firstKey.threadLocalHashCode & (INITIAL_CAPACITY - 1);
    table[i] = new Entry(firstKey, firstValue);
    size = 1;
    setThreshold(INITIAL_CAPACITY);
}
```
每个线程对象都有一个ThreadLocalMap类型的变量。ThreadLocalMap是ThreadLocal的内部静态类，是用于线性探测法的散列表实现的。每一个线程对象可以往Map中添加多个ThreadLocal对象为键的键值对，每个键对应的值唯一。所以通过一个ThreadLocal对象设置的值，在每个线程中都是唯一且相互独立的。唯一是因为键的唯一性，独立是因为每个线程都有自己的ThreadLocalMap内部变量。它是归线程所有的。

ThreadLocalMap的构造方法里显示定义一个初始大小为16的Entry数组实例table，用于存储Entry对象。不难理解key，value被封装进了Entry对象里。也就是说，ThreadLocalMap维护着一张哈希表也就是table数组，并且设定了一个临界值setThreashold(),当哈希表里面存储的对象达到容量的2/3的时候就需要扩容。

**注意：用于存放Entry对象的table数组是每一个线程都持有的，内部使用THreadLocal<?> key来区分数据来自哪个ThreadLocal。这也是为什么在指定的线程中可以获取到相应的值**

#### Entry
```
static class Entry extends WeakReference<ThreadLocal<?>> {
            /** The value associated with this ThreadLocal. */
            Object value;

            Entry(ThreadLocal<?> k, Object v) {
                super(k);
                value = v;
            }
        }
```
entry是ThreadLocalMap中的静态内部类。需要注意的是entry继承自WeakReference，是一个弱引用很有可能会被系统回收掉。被弱引用关联的对象只能生存到下一次垃圾收集发生之前。当垃圾收集器工作时，无论当前内存是否足够，都会回收掉只被引用关联的对象。因此如果某个线程已经运行结束，不会因为这里有对它的引用而就无法回收，这个键会在回收后变成null。

#### ThreadLocal的set方法
ThreadLocal的set()方法就是调用了ThreadLocalMap的set()

```
public void set(T value) {
    // 获取当前线程
    Thread t = Thread.currentThread();
    ThreadLocalMap map = getMap(t);
    // 已经调用过set或get方法时，往线程的map中添加新的值，键为当前ThreadLocal对象
    if (map != null)
        map.set(this, value);
    // 没有调用过set或get方法时，为当前线程创建一个ThreadLocalMap
    // 并添加第一个值，键为当前ThreadLocal对象
    else
        createMap(t, value);
}

// 返回Thread对象t的ThreadLocalMap属性，初始为null
ThreadLocalMap getMap(Thread t) {
    return t.threadLocals;
}


void createMap(Thread t, T firstValue) {
    t.threadLocals = new ThreadLocalMap(this, firstValue);
}
```
**ThreadLocalMap中的set**

```
private void set(ThreadLocal<?> key, Object value) {

            // We don't use a fast path as with get() because it is at
            // least as common to use set() to create new entries as
            // it is to replace existing ones, in which case, a fast
            // path would fail more often than not.

            Entry[] tab = table;
            int len = tab.length;
           //计算存储的位置i
            int i = key.threadLocalHashCode & (len-1);
           //如果存储的位置i已经存储了对象，就向后寻找下一个位置（nextIndex(i,len)），以此类推直到找到空的位置
            for (Entry e = tab[i];
                 e != null;
                 e = tab[i = nextIndex(i, len)]) {
                ThreadLocal<?> k = e.get();
           //如果Entry对象的key值和需要存储的key值是同一个，就对这个value进行替换
                if (k == key) {
                    e.value = value;
                    return;
                }
            //key为空对这个位置的Entry对象重新赋值
                if (k == null) {
                    replaceStaleEntry(key, value, i);
                    return;
                }
            }
           //table数组中没有这样的含有这个key值的Entry对象，创建Entry对象存储
           //判断table数组中存储对象的个数是否超过了临界值（threshold），如果超出了需要扩充并重新计算所有对象的位置（rehash()）
            tab[i] = new Entry(key, value);
            int sz = ++size;
            if (!cleanSomeSlots(i, sz) && sz >= threshold)
                rehash();
        }
```
致分析上面都已经标注出来了，需要注意的是Entry对象是继承是WeakReference也就是一个弱引用是会被回收的，所以对应 的key值可能是为null的。存放对象之后是需要判断数组中存储对象的个数是否超过了设定的临界值threshold的大小，如果超过了需要扩容，并且还要重新计算扩容后所有对象的位置。扩容的方法是rehash()

**扩容方法rehash()**
```
private void rehash() {
            expungeStaleEntries();

            // Use lower threshold for doubling to avoid hysteresis
            if (size >= threshold - threshold / 4)
                resize();
        }
```
rehash()中主要做了两步操作：
1. 显示删除过时的Entry：expungeStaleEntries()
2. 如果存储对象个数大于临界值的3/4,扩容

**expungeStaleEntries()**
```
private void expungeStaleEntries() {
            Entry[] tab = table;
            int len = tab.length;
            for (int j = 0; j < len; j++) {
                Entry e = tab[j];
                if (e != null && e.get() == null)
                    expungeStaleEntry(j);
            }
        }
```
删除数组中过时的Entry对象。有些疑问什么是过时的Entry？为什么会过时？Entry是弱引用会被回收。这个方法中判断的删除条件是，Entry对象不为空并且key值为空。可见expungStaleEntry(j) 方法就是删除指定索引的Entry对象。

#### resize()扩容
```
private void resize() {
            Entry[] oldTab = table;
            int oldLen = oldTab.length;
            int newLen = oldLen * 2;
            Entry[] newTab = new Entry[newLen];
            int count = 0;

            for (int j = 0; j < oldLen; ++j) {
                Entry e = oldTab[j];
                if (e != null) {
                    ThreadLocal<?> k = e.get();
                    if (k == null) {
                        e.value = null; // Help the GC
                    } else {
                        int h = k.threadLocalHashCode & (newLen - 1);
                        while (newTab[h] != null)
                            h = nextIndex(h, newLen);
                        newTab[h] = e;
                        count++;
                    }
                }
            }

            setThreshold(newLen);
            size = count;
            table = newTab;
        }
```
先是创建一个是原来容量两倍的Entry[]数组，在遍历原来的数组，将key值为空的Entry对象的value置为空方便GC回收，key不为空的Entry对象先根据key的hashcode计算需要存放的位置存入新的数组中，存储结束后别忘了更新临界值。

####  get()方法
调用getMap(t);方法获取当前线程的ThreadLocalMap对象，ThreadLocalMap != null的时候调用ThreadLocalMap的getEntry();方法获取ThreadLocalMap.Entry对象，再获取Entry对象中的value值并返回。如果ThreadLocalMap==null的时候（可能未调用过set();方法进行初始化），调用setInitialValue();初始化value（null），保存，并返回。

####  remove()方法
调用了ThreadLocalMap的remove()方法;最终调用了Entry的clear();方法，也就是Reference的clear();方法，删除数组中根据key的hashcode计算出来的索引i位置上的Entry对象。

**总结**
总结：从ThreadLocal的set();，get()和remove();方法可以看出，它们所操作的对象都是当前线程的ThreadLocalMap对象的table数组，因此在不同线程中访问同一个ThreadLocal的set();,get();和remove();方法，它们对ThreadLocal所做的读/写操作仅限于各自线程的内部，这就是为什么ThreadLocal可以在多个线程中互不干扰地存储和修改数据。

### 什么是ThreadLocal

ThreadLocal的设计初衷就是为了提供线程内部的局部变量，方便在本线程内随时随地的读取，并且与其他线程隔离。

ThreadLocal是Java里一种特殊的变量。每个线程都有一个ThreadLocal就是每个线程都拥有了自己独立的一个变量，竞争条件被彻底消除了。它是为创建代价高昂的对象获取线程安全的好方法，比如你可以用ThreadLocal让SimpleDateFormat变成线程安全的，因为那个类创建代价高昂且每次调用都需要创建不同的实例所以不值得在局部范围使用它，如果为每个线程提供一个自己独有的变量拷贝，将大大提高效率。首先，通过复用减少了代价高昂的对象的创建个数。其次，你在没有使用高代价的同步或者不变性的情况下获得了线程安全。

每个ThreadLocal可以放一个线程级别的变量，但是它本身可以被多个线程共享使用，而且又可以达到线程安全的目的，且绝对线程安全。

### ThreadLocal的使用场景

  很多时候我们会创建一些静态域来保存全局对象，那么这个对象就可能被任意线程访问，如果能保证是线程安全的，那倒是没啥问题，但是有时候很难保证线程安全，这时候我们就需要为每个线程都创建一个对象的副本，我们也可以用ConcurrentMap<Thread, Object>来保存这些对象，这样会比较麻烦，比如当一个线程结束的时候我们如何删除这个线程的对象副本呢？如果使用ThreadLocal就不用有这个担心了，ThreadLocal保证每个线程都保持对其线程局部变量副本的隐式引用，只要线程是活动的并且 ThreadLocal 实例是可访问的；在线程消失之后，其线程局部实例的所有副本都会被垃圾回收（除非存在对这些副本的其他引用）


1. 当某些数据以线程为作用域，并且不同线程拥有不同数据副本的时候。

        最常见的ThreadLocal使用场景为 用来解决数据库连接、Session管理等

       Spring的事务管理器通过AOP切入业务代码，在进入业务代码前，会根据对应的事务管理器提取出相应的事务对象，假如事务管理器是DataSourceTransactionManager，就会从DataSource中获取一个连接对象，通过一定的包装后将其保存在ThreadLocal中。并且Spring也将DataSource进行了包装，重写了其中的getConnection()方法，或者说该方法的返回将由Spring来控制，这样Spring就能让线程内多次获取到的Connection对象是同一个。

2. 复杂逻辑下对象传递，比如监听器的传递

      使用参数传递的话：当函数调用栈更深时，设计会很糟糕，为每一个线程定义一个静态变量监听器，如果是多线程的话，一个线程就需要定义一个静态变量，无法扩展，这时候使用ThreadLocal就可以解决问题
### java中interrupted和isinterrupted方法的区别？

1. interrupt

    interrupt方法用于中断线程。调用该方法的线程的状态将被置位“中断状态”
**注意：线程中断仅仅是置线程的中断状态位，不会停止线程。需要用户自己去监视线程状态并做处理。支持线程中断的方法就是在监视线程中断的状态，一旦线程的中断状态被置为“中断状态”，就会抛出中断异常**

2.interrupted

      查询当前线程的中断状态，并且清除原状态。如果一个线程被中断了，第一次调用interrupted则返回true，第二次和后面的就返回false了。

3.isInterrupted

  仅仅是查询当前线程的中断状态

### Thread类中的yield方法有什么作用

    使当前线程从执行状态变为可执行状态（就绪状态）

    当前线程到了就绪状态，那么接下来哪个线程会从就绪状态变成执行状态呢？可能是当前线程，也可能是其他线程，看系统的分配了。

###  怎么检测一个线程是否拥有锁

在java.lang.Thread中有一个方法叫holdsLock(),它返回true如果当且仅当当前线程拥有某个具体对象的锁。

### 什么是可重入锁（ReentrantLock()）

可重入锁，也叫递归锁，指的是同一线程外层函数获得锁之后，内层递归函数仍然有获取该锁的代码，但不受影响。

**可重入性**
ReentrantLock的字面意思就是再进入的锁，其实synchronized关键字所使用的锁也是可重入的，两者关于这个的区别不大。两者都是同一个线程每进入一次，锁的计数器都自增1，所以要等到锁的计数器下降为0时才能释放锁。

**锁的实现**
Synchronized是依赖于JVM实现的，而ReenTrantLock是JDK实现的，前者的实现时比较难见到的，后者有直接的源码可供阅读。

**功能区别**

便利性：很明显synchronized的使用比较方便简单，并且由编译器去保证锁的加锁和释放，而ReenTrantLock需要手工声明来加锁和释放锁，为了避免忘记手工释放锁造成死锁，所以最好在finally中声明释放锁

锁的细粒度和灵活度：很明显ReenTrantLock优于Synchronized

**ReenTrantLock独有的功能**
1. ReenTrantLock可以指定公平锁还是非公平锁，而synchronized只能是非公平锁。所谓的公平锁就是先等待的线程先获得锁
2. ReenTrantLock提供了一个Condition（条件）类，用来实现分组唤醒需要唤醒的线程们，而不是像synchronized要么随机唤醒一个，要么全部唤醒
3. ReenTrantLock提供了一种能够中断等待锁的线程的机制，通过lock.lockInterruptibly()来实现这个机制

**ReenTrantLock原理简述**

ReenTrantLock的实现时一种自旋锁，通过循环调用CAS操作来实现加锁

### 当一个线程进入某个对象的一个synchronized的实例方法后，其它线程是否可进入此对象的其它方法？

如果其他方法没有synchronized的话，其他线程是可以进入的。所以要开放一个线程安全的对象时，得保证每个方法都是线程安全的。

### 乐观锁和悲观锁的理解及如何实现，有哪些实现方式？

**悲观锁：**
总是假设最坏的情况，每次去拿数据的时候都认为别人会修改，所以每次那数据的时候都会上锁，这样别人向南这个数据就会阻塞直到它拿到锁。传统的关系型数据库里边就用到了很多这种锁机制，比如行锁，表锁，读锁，写锁等，都是在做操作之前先上锁。java中的同步synchronized关键字的实现也是悲观锁。

**乐观锁：**
就是很乐观，每次去拿数据的时候都认为别人不会修改，所以不会上锁，但是在更新的时候会判断一下在此期间别人有没有去更新这个数据，可以使用版本号等机制。乐观锁使用与多读的应用类型，这样可以提高吞吐量，像数据库提供的类似于write_condition机制，其实都是乐观锁。CAS就是乐观锁的一种实现方式。

**悲观锁的实现**
在对任何记录修改前，先尝试为该记录加上排他锁。如果加锁失败，说明该记录正在被修改，name当前查询可能要等待或则抛出异常，如果加锁成功，那么就可以对记录做修改，事务完成后就会解锁。

悲观锁的策略过于保守，并发性能不好而且有产生死锁的风险，所以悲观锁要慎重使用。目前它主要用于数据竞争激烈的环境，以及发生并发冲突时使用锁保护数据的成本要低于回滚事务的成本的环境中

**乐观锁的实现**

1.一般乐观锁的实现都是基于数据版本号，使用版本号时，可以在数据初始化时指定一个版本号，每次对数据的更新操作都对版本号执行+1操作。并判断当前版本号是不是该数据的最新版本号，版本号不一致时可以采取丢弃和再次尝试的策略

2.Java中的Compare and Swap即CAS ，当多个线程尝试使用CAS同时更新同一个变量时，只有其中一个线程能更新变量的值，而其它线程都失败，失败的线程并不会被挂起，而是被告知这次竞争中失败，并可以再次尝试。　CAS 操作中包含三个操作数 —— 需要读写的内存位置（V）、进行比较的预期原值（A）和拟写入的新值(B)。如果内存位置V的值与预期原值A相匹配，那么处理器会自动将该位置值更新为新值B。否则处理器不做任何操作。

如果系统的并发非常大的话，悲观锁定会带来非常大的并发问题。所以此时我们就要选着乐观锁进行并发控制

###  CAS缺点

1. ABA问题

比如说一个线程one从内存位置V中取出A，这时候另一个线程two也从内存中取出A，并且two进行了一些操作变成了B，然后two又将V位置的数据变成A，这时候线程one进行CAS操作发现内存中仍然是A，然后one操作成功。尽管线程one的CAS操作成功，但可能存在潜藏的问题。

2. 循环时间长开销大：

  对于资源竞争严重（线程冲突严重）的情况，CAS自旋的概率会比较大，从而浪费更多的CPU资源，效率低于synchronized

3. 只能保证一个共享变量的原子操作

当对一个共享变量执行操作时，我们可以使用循环CAS的方式来保证原子操作，但是对多个共享变量操作时，循环CAS就无法保证操作的原子性，这个时候就可以用锁。

### synchronizedMap()与ConcurrentHashMap主要区别

Collections.synchronizedMap()与ConcurrentHashMap主要区别是：Collections.synchronizedMap()和Hashtable一样，在调用map所有方法的时候，都对整个map进行同步（即不同线程可以同时调用同一个map）。而ConcurrentHashMap的实现却更加精细，它对map中的所有桶加了锁，所以，只要有一个线程访问map，其他线程就无法进入map，而如果一个线程在访问ConcurrentHashMap某个桶时，其他线程，仍然可以对map执行某些操作。这样，ConcurrentHashMap在性能以及安全性方面，明显比Collections.synchronizedMap()更加有优势。同时，同步操作精确控制到桶，所以，即使在遍历map时，其他线程试图对map进行数据修改，也不会抛出ConcurrentModificationException。

### CopyOnWriteArrayList可以用于什么应用场景

CopyOnWriteArrayList(免锁容器)的好处之一是当多个迭代器同时遍历和修改这个表时，不会抛出ConcurrentModificationException。在CopyOnWriteArrayList中，写入将导致创建整个底层数组的副本，而源数组将保留在原地，使得复制的数组在被修改时，读取操作可以安全地执行。

          1.由于写操作的时候，需要拷贝数组，会消耗内存，如果原数组的内容比较多的情况下，可能导致young gc或则full gc

          2.不能用于实时读的场景，像拷贝数组，新增元素都需要时间，所以调用一个set操作后，读取到数据可能还是旧的，虽然CopyOnWriteArrayList能做到最终一致性，但是还是没法满足实时性要求。

CopyOnWriteArrayList透露的思想：
         1.读写分离，读和写分开

         2.最终一致性

         3.使用另外开辟空间的思路，来解决并发冲突

###  volatile关键字的作用   应用场景

在启动线程的时候，虚拟机为每个内存分配了一块工作内存，不仅包含了线程内部定义的局部变量，也包含了线程所需要的共享变量的副本，当然这是为了提高执行效率，读副本比直接读主内存更快

那么对于volatile修饰的变量（共享变量）来说，在工作内存发生了变化后，必须要马上写到主内存中，而线程读取到是volatile修饰的变量时，必须去主内存中去获取最新的值，而不是读工作内存中主内存的副本，这就有效的保证了线程之间变量的可见性。

特性一： 内存可见性，即线程A对volatile变量的修改，其他线程获取的volatile变量都是最新的

      volatile的例子很难重现，因为只有在对变量读取频率很高的情况下，虚拟机才不会及时写会到主内存，当频率没有达到虚拟机认为的高频率时，普通变量和volatile变量是同样的处理逻辑。

特性二：可以禁止指令重排序

        为了尽可能减少内存操作速度远慢于CPU运行速度所带来的CPU空置的影响，虚拟机会按照自己的一些规则将程序编写顺序打乱。

        如果变量没有volatile修饰，程序执行的顺序可能会进行重排序

volatile用于多线程环境下的单次操作（单次读或单次写）

要使 volatile 变量提供理想的线程安全，必须同时满足下面两个条件：
1. 对变量的写操作不依赖于当前值
2. 该变量没有包含在具有其他变量的不变式中

可以被写入 volatile 变量的这些有效值独立于任何程序的状态，包括变量的当前状态。

###  为什么代码会重排序？

在执行程序时，为了提供性能，处理器和编译器常常会对指令进行重排序，但是不能随意冲排序;

       1.单线程环境下不能改变程序的运行结果

       2.存在数据依赖关系的不允许重排序
**注意：重排序不会影响单线程环境的执行结果，但是会破坏多线程的执行语义**

### 死锁与活锁的区别，死锁与饥饿的区别？

**死锁：**  是指两个或则两个以上的进程在执行过程中，因争夺资源而造成的一种互相等待的现象，若无外力作用，他们都将无法推进下去

**产生死锁的必要条件：**
1. 互斥条件：所谓互斥就是进程在某一时间内独占资源
2. 请求与保持条件：一个进程因请求资源而阻塞时，对已获得的资源保持不放
3. 不剥夺条件:进程已获得资源，在未使用完成之前，不能强行剥夺
4. 循环等待条件：若干进程之间形成一种头尾相接的循环等待资源关系

**活锁：**  任务或则执行者没有被阻塞，由于某些条件没有满足，导致一直重复尝试，失败，尝试，失败

活锁和死锁的区别在于：处于活锁的实体是在不断的改变状态，所谓“活”，而处于死锁的实体表现为等待；活锁有可能自行解开，死锁则不能。

**饥饿：**   一个或多个线程因为种种原因无法获得所需要的资源，导致一直无法执行的状态

java中导致饥饿的原因：
1. 高优先级线程吞噬所有低优先级线程的CPU时间
2. 线程被永久堵塞在一个等待进入同步块的状态，因为其他线程总是能在它之前持续地对该同步块进行访问
3. 线程在等待一个本身也处于永久等待完成的对象，因为其他线程总是被持续地唤醒

###  java中用到的线程调度算法是什么

线程调度模式：
1. 抢占式调度

    抢占式调度指的是每条线程执行的时间，线程的切换都由系统控制。系统控制指的是在系统某种运行机制下，可能每条线程都分同样的执行时间片，也可能是某些线程执行的时间片较长，甚至某些线程得不到执行的时间片。

2. 协同式调度

    协同式调度指某一线程执行完后主动通知系统切换到另一线程上执行

Java使用的线程调度是抢占式调度，在JVM中体现为让可运行池中优先级高的线程拥有CPU使用权，如果可运行池中线程优先级一样则随机选择线程，但要注意的是实际上一个绝对时间点只有一个线程在运行（这里是相对于一个CPU来说，如果你的机器是多核的还是可能多个线程同时运行的），直到此线程进入非可运行状态或另一个具有更高优先级的线程进入可运行线程池，才会使之让出CPU的使用权，更高优先级的线程抢占了优先级低的线程的CPU。

采用的是时间片轮转的方式
