# 一、基本概念
## 1.1 程序、进程与线程

- **程序（program）**：为完成特定任务，用某种语言编写的一组指令的集合。即指一段静态的代码，静态对象。
- **进程（process）**：程序的一次执行过程，或是正在内存中运行的应用程序。如：运行中的QQ，运行中的网易音乐播放器。
- **线程（thread）**：进程可进一步细化为线程，是程序内部的一条执行路径。一个进程中至少有一个线程。
## 1.2 线程调度

- **分时调度**：所有线程轮流使用 CPU 的使用权，并且平均分配每个线程占用 CPU 的时间。
- **抢占式调度**：优先级高的线程以较大概率优先使用CPU。如果线程的优先级相同，则会随机选择一个线程执行（即，线程的随机性），Java使用此种方式调度。
## 1.3 并行与并发

- **并行（parallel）**：同一时刻，多件事情同时发生，即同一时刻，有多条指令同时在多个CPU上同时执行。

![image.png](https://gitee.com/litan33/image-host/raw/master/img/20230915202906.png)
- **并发（concurrency）**：同一时段内，有多件事情发生，即在一段时间内，有多条指令在单个CPU上快速轮换、交替执行，使得在宏观上具有多个进程同时执行的效果。

![image.png](https://gitee.com/litan33/image-host/raw/master/img/20230915203259.png)
# 二、线程的创建与启动

## 2.1 继承Thread类（方式1）

```java
class ThreadDemo extends Thread {
    // 线程示例，打印100以内的偶数
    @Override
    public void run(){
    // 重写run()，在run()中定义线程运行的任务，此处是打印100以内的偶数
        for (int i = 0; i <= 100; i += 2) {
            System.out.println(Thread.currentThread().getName() + ": " + i);
        }
    }
}

public class MainDemo {
    public static void main(String[] args){
        ThreadDemo t1 = new ThreadDemo();
        // 要用start()方法启动线程,
        // 而不是t1.run(),
        // 否则t1.run()将会被在main线程中运行，
        // 而非在子线程t1中运行,就不是真正意义上的“多”线程了
        t1.start()
    }
}
```

## 2.2 实现Runnable接口（方式2）

```java
class RunnableDemo implements Runnable {
    // 线程示例，打印100以内的偶数
    @Override
    public void run(){
    // 重写run()，在run()中定义线程运行的任务，此处是打印100以内的偶数
        for (int i = 0; i <= 100; i += 2) {
            System.out.println(Thread.currentThread().getName() + ": " + i);
        }
    }
}

public class MainDemo {
    public static void main(String[] args){
        RunnableDemo r1 = new RunnableDemo();
        // 需将创建的Runnable实例传入Thread实例中，
        // 即，Runnable中的run()的真正执行者还是Thread类的对象
        Thread t1 = new Thread(r1);
        // 启动线程
        t1.start()
    }
}
```

## 2.3 Thread与Runnable比较

### 2.3.1 区别

- **Thread**：线程代码存放在Thread子类的run()方法中。
- **Runnbale**：线程代码存放在Runnable实现子类的run()方法中。
### 2.3.2 Runnable的优势

- 避免了单继承的局限性（**多继承**）；
- 多个线程可以共享同一个接口实现类的对象，非常适合多个相同线程来处理同一份资源（**可共享**）;
- 增加程序的健壮性，实现解耦操作，代码可以被多个线程共享，代码和线程独立（**耦合度低**）

# 三、Thread的常用结构及方法

## 3.1 构造器

- `public Thread()`：创建线程对象。
- `public Thread(String name)`：创建具名线程对象。
- `public Thread(Runnable target)`：创建指定了目标对象（Runnable）的线程对象。
- `public Thread(Runnable target, String name)`：创建指定了目标对象（Runnable）的具名线程对象。

## 3.2 常用方法系列1

- `public void run()`：定义线程待执行的任务。
- `public void start()`：启动线程，使JVM虚拟机启动子线程运行run()的任务。
- `public void setName(String name)`：为线程命名。
- `public String getName()`：获取线程名。
- `public static Thread currentThread()`：返回对当前正在执行的线程对象的引用。
- `public static void sleep(long mills)`：使其所在的线程暂停指定的毫秒数。注意，不是谁调用暂停谁（因为是static方法），而是在谁中调用就暂停谁；另外，此方法暂停线程时，不会释放其所在线程获取的锁。
- `public static void yield()`：yield()只是让当前线程暂停一下，让系统的线程调度器重新调度一次，希望优先级与当前线程相同或更高的其他线程能够获得执行机会，但是这个不能保证，完全有可能的情况是，当某个线程调用了yield方法暂停之后，线程调度器又将其调度出来重新执行。

## 3.3 常用方法系列2

- `public final boolean isAlive()`：判断线程是否为活跃状态（即线程已启动且未终止），是则返回 `true`。
- `public final void join()`：等待该线程终止。如下图，则Thread_A会等Thread_B执行完才会继续执行

```mermaid
stateDiagram-v2
    [*] --> In_Thread_A
    state In_Thread_A {
        [*] --> Thread_B.join()
        Thread_B.join() --> [*]
    }
    In_Thread_A --> [*]

```
- `public final void join(long millis)`：等待该线程终止的时间最长为 millis 毫秒。如果millis时间到，将不再等待。
- `public final void join(long millis, int nanos)`：等待该线程终止的时间最长为 millis 毫秒 + nanos 纳秒。
- `public final void stop()`：使线程立刻强行终止并释放所有已获取的锁，可能会出现数据安全问题，已过时。
- `void suspend()/void resume()`： 这两个操作就好比播放器的暂停和恢复。二者必须成对出现，否则非常容易发生死锁。suspend()调用会导致线程暂停，但不会释放任何锁资源，导致其它线程都无法访问被它占用的锁，直到调用resume()。已过时，不建议使用。

## 3.4 线程的优先级

优先级高的线程优先执行（被执行的几率更大），Thread类常见的三个优先级常量：
- `MAX_PRIORITY(10)`：最高优先级。
- `MIN_PRIORITY(1)`：最低优先级。
- `NORM_PRIORITY(5)`：普通优先级（线程默认级）。

与之相关的方法如下：
- `public final int getPriority()`：获取线程优先级。
- `public final void setPriority(int newPriority)`：设置线程优先级，范围是[1, 10]之间。

## 3.5 守护进程

有一种线程，它是在后台运行的，它的任务是为其他线程提供服务的，这种线程被称为“守护线程”。JVM的垃圾回收线程就是典型的守护线程。

守护线程有个特点，就是如果所有非守护线程都死亡，那么守护线程自动死亡。形象理解：兔死狗烹，鸟尽弓藏

调用setDaemon(true)方法可将指定线程设置为守护线程。必须在线程启动之前设置，否则会报IllegalThreadStateException异常。

调用isDaemon()可以判断线程是否是守护线程。