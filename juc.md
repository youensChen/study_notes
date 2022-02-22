# JUC大纲

![image-20210330091643005](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210330091650.png)

# JUC是什么

```java
// java.util.concurrent包
```

<img src="https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210330093053.png" alt="image-20210330093053694" style="zoom:150%;" />

## 回顾

![image-20210330093729988](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210330093730.png)

![image-20210330094054147](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210330094054.png)

## 多线程模板

![image-20210330100016240](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210330100016.png)

```java
public class Main {
    public static void main(String[] args) {
        //资源类
        Ticket ticket = new Ticket(); 
        // 线程
        new Thread(() -> {  
            for (int i = 1; i < 40; i++) {
                ticket.saleTicket();
            }
        }, "AA").start();
        new Thread(() -> {
            for (int i = 1; i < 40; i++) {
                ticket.saleTicket();
            }
        }, "BB").start();
        new Thread(() -> {
            for (int i = 1; i < 40; i++) {
                ticket.saleTicket();
            }
        }, "CC").start();
    }
}

class Ticket{
    private int num = 100;
	// 操作都要封装在资源类里
    public synchronized void saleTicket() {
        System.out.println(Thread.currentThread().getName() + "卖出第" + num-- + "张票，还剩" + num + "张票。");
        
    }
}

// JUC写法
class Ticket {
    private int num = 100;
    Lock lock = new ReentrantLock();
    public void saleTicket() {
        lock.lock();
        try {
            if (num > 0) {
                System.out.println(Thread.currentThread().getName() + "卖出第" + num-- + "张票，还剩" + num + "张票。");
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }
}
```

![image-20210330105739709](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210331142959.png)

## 线程之间通信（生产/消费）

![image-20210330124059191](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210330124059.png)

### 传统写法

```java
public class Main {
    public static void main(String[] args) {
        //资源类
        AirConditioner airConditioner = new AirConditioner();
        new Thread(() -> { // 线程
            for (int i = 0; i < 10; i++) {
                try {
                    Thread.sleep(200);
                    airConditioner.increment();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }, "A").start();


        new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                try {
                    Thread.sleep(100);
                    airConditioner.decrement();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }, "B").start();

        new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                try {
                    Thread.sleep(50);
                    airConditioner.increment();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }, "C").start();


        new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                try {
                    Thread.sleep(200);
                    airConditioner.decrement();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }, "D").start();

    }
}

class AirConditioner {
    private int num = 0;
    public synchronized void increment() throws InterruptedException { // 操作

        // 1判断
        while (num != 0) { // 不能用if
            this.wait();
        }
        // 干活
        num++;
        System.out.println(Thread.currentThread().getName() + "\t" + num);
        // 通知
        this.notify();

    }

    public synchronized void decrement() throws InterruptedException {
        while (num == 0) {
            this.wait();
        }
        num--;
        System.out.println(Thread.currentThread().getName() + "\t" + num);
        this.notify();
    }


    
```

### JUC写法

==Lock==

```java
// JUC写法
    
class AirConditioner {
    private int num = 0;
    private Lock lock = new ReentrantLock();
    private Condition condition = lock.newCondition();

    public void increment() {
        lock.lock();
        try {
            // 1判断
            while (num != 0) {
                condition.await();
            }
            // 干活
            num++;
            System.out.println(Thread.currentThread().getName() + "\t" + num);
            // 通知
            condition.signalAll();
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }

    public void decrement() {
        lock.lock();
        try {
            while (num == 0) {
                condition.await();
            }
            num--;
            System.out.println(Thread.currentThread().getName() + "\t" + num);
            condition.signalAll();
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }
}
```



## JUC的优点

![image-20210330140351964](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210330140352.png)

==Lock和Condition配合能实现精准的唤醒和控制，不会再造成一次性唤醒多个线程而造成的多线程抢夺资源的现象。能实现线程
A -> B -> C -> D的线程控制==



```java
class ShareResouce {
    private int num = 1; // 标志位  1-a, 2-b, 3-c
    private Lock lock = new ReentrantLock();
    private Condition condition1 = lock.newCondition(); //相当于3把锁
    private Condition condition2 = lock.newCondition();
    private Condition condition3 = lock.newCondition();

    public void print5() {
        lock.lock();
        try {
            // 判断
            while (num != 1) {
                condition1.await();
            }
            //干活
            for (int i = 0; i < 5; i++) {
                System.out.println(Thread.currentThread().getName() + "\t第" + i + "次");
            }
            // 通知
            num = 2;
            condition2.signal();

        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }

    public void print10() {
        lock.lock();
        try {
            while (num != 2) {
                condition2.await();
            }
            for (int i = 0; i < 10; i++) {
                System.out.println(Thread.currentThread().getName() + "\t第" + i + "次");
            }
            num = 3;
            condition3.signal();

        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }

    public void print15() {
        lock.lock();
        try {
            while (num != 3) {
                condition3.await();
            }
            for (int i = 0; i < 15; i++) {
                System.out.println(Thread.currentThread().getName() + "\t第" + i + "次");
            }
            num = 1;
            condition1.signal();

        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }
}
```

# 锁

## 8种问题

```java
public class Main {
    public static void main(String[] args) throws Exception {
        Phone phone = new Phone();
        // Phone phone2 = new Phone();
        new Thread(() -> {
            phone.sentEmail();
        }, "A").start();

        Thread.sleep(100); //线程A先启动
        new Thread(() -> {
            phone.sentSMS();
            phone.hello();
            //phone2.sentSMS();
        }, "B").start();
    }
}

class Phone {
    public static synchronized void sentEmail() {
        // try { TimeUnit.SECONDS.sleep(4); } catch (InterruptedException e) { e.printStackTrace(); }
        System.out.println("sentEmail");
    }

    public static  synchronized void sentSMS() {
        System.out.println("sentSMS");
    }
    public void hello(){
        System.out.println("hello");
    }
}
```

![image-20210330144328872](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210330144328.png)

```
1、邮件 短信
2、邮件 短信
3、hello 邮件
4、短信 邮件
5、邮件 短信
6、邮件 短信
7、短信 邮件
8、短信 邮件
```

## 总结

### 1-2

synchronize锁住对象

![image-20210330150134332](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210330150134.png)

### 3-4

普通方法和锁无关

![image-20210330150908701](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210330150908.png)

### 5-6

static synchronize锁的是整个类

![image-20210330151942471](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210330151942.png)

# 集合类不安全

## List不安全

### 故障现象

```java
public static void main(String[] args) throws Exception {
        List<String> strings = new ArrayList<>();
        for (int i = 0; i < 3; i++) {
            new Thread(() -> {
                strings.add(UUID.randomUUID().toString().substring(0, 8));
                System.out.println(strings);
            }, String.valueOf(i)).start();
        }
    }
//运行结果每次都不一样
[5d0fe05d, dc457b95]
[5d0fe05d, dc457b95]
[5d0fe05d, dc457b95]

[5fa029e6, null, 9dba79e7]
[5fa029e6, null, 9dba79e7]
[5fa029e6, null, 9dba79e7]
//此时程序并没有报错，而当我们提高线程数量时
//将会报错 java.util.ConcurrentModificationException

```

### 出现原因



### 解决方案

```java
// 1、使用线程安全的类，如Vector，但是性能会下降
List<String> list = new Vector<>();

// 2、使用Collections工具类获得一个线程安全的ArrayList，但是只能小数据，不能应对高并发场景
List<String> list = Collections.synchronizedList(new ArrayList<>());

//3、终极解决方案，使用JUC下的CopyOnWriteArrayList解决
List<String> strings = new CopyOnWriteArrayList<>();

//CopyOnWriteArrayList写时复制，读写分离的思想，当有新数据要写入时，他写的是一个副本，而旧版本可供其他线程去读，写完之后，释放写锁，更新旧版本。
// CopyOnWriteArrayList.add()源码
public boolean add(E e) {
        final ReentrantLock lock = this.lock;
        lock.lock(); //加锁
        try {
            Object[] elements = getArray(); //旧版本
            int len = elements.length;
            Object[] newElements = Arrays.copyOf(elements, len + 1); //扩容1，写时复制CopyOnWrite
            newElements[len] = e;
            setArray(newElements); // 写完覆盖旧版本
            return true;
        } finally {
            lock.unlock();
        }
}


```

![image-20210330165233814](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210330165233.png)

## Set不安全

### 解决方案

```java
//1、
Set<String> set = Collections.synchronizedSet(new HashSet<>());

//2、
Set<String> set = new CopyOnWriteArraySet<>();

```

## Map不安全

#### 解决方案

```java
// 1、
Map<String, String> strings = Collections.synchronizedMap(new HashMap<>());

// 2、
Map<String, String> strings = new ConcurrentHashMap<>();
```



# 多线程的四种方式

## 继承Thread

```java
class MyThread extends Thread{
    
}
```



## 实现Runnable接口

```java
class MyThread implements Runnable{
    @Override
    public void run() {
        
    }
}
```



## 实现Callable接口

```java
public class Main {
    public static void main(String[] args) throws Exception {
        // new Thread()没有传入Callable接口的构造函数
        FutureTask futureTask = new FutureTask(new MyTask()); // FutureTask实现了Runnable接口
        new Thread(futureTask, "A").start();
        new Thread(futureTask, "B").start(); //并不会再次执行

        System.out.println(2*2);// 主线程做其他事
        
        // 会等待线程执行完获得返回值，在期间可以去做其他事情，对等线程计算完复杂问题后会有返回值
        System.out.println(futureTask.get()); //get一般放在最后
        
    }

}
class MyTask implements Callable<Integer> {

    @Override
    public Integer call() throws Exception {
        System.out.println("come in here");
        try { TimeUnit.SECONDS.sleep(2); } catch (InterruptedException e) { e.printStackTrace(); }
        return 1024;
    }
}
```

![image-20210331150218209](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210331150218.png)



## 线程池



# JUC辅助类

## CountDownLatch倒数器

**==java.util.concurrent.CountDownLatch;==**

### 问题

```java
public static void main(String[] args) throws Exception {
        for (int i = 0; i < 6; i++) {
            new Thread(() -> {
                System.out.println(Thread.currentThread().getName()+"\t离开教室");
            }, String.valueOf(i)).start();
        }
        //需求，main线程要在其他线程执行完后才退出
        System.out.println(Thread.currentThread().getName()+"\t班长关门");
    }
0	离开教室
main	班长关门
3	离开教室
4	离开教室
1	离开教室
2	离开教室
5	离开教室
```

### 解决方式	

```java
public static void main(String[] args) throws Exception {
        //相当于计数器
        CountDownLatch countDownLatch = new CountDownLatch(6);//计数器初始值为6
        for (int i = 0; i < 6; i++) {
            new Thread(() -> {
                System.out.println(Thread.currentThread().getName()+"\t离开教室");
                countDownLatch.countDown(); //计数器减1 线程不阻塞
                System.out.println(Thread.currentThread().getName()+"\t回家吃饭");
            }, String.valueOf(i)).start();
        }
        //需求，main线程要在其他线程执行完后才退出
        countDownLatch.await(); //等待计数器为0
        System.out.println(Thread.currentThread().getName()+"\t离开教室回家吃饭");
    }
0	离开教室
4	离开教室
4	回家吃饭
3	离开教室
3	回家吃饭
2	离开教室
2	回家吃饭
1	离开教室
1	回家吃饭
0	回家吃饭
5	离开教室
5	回家吃饭
main	离开教室回家吃饭
```

## CyclicBarrier循环栅栏

**==java.util.concurrent.CyclicBarrier;==**

```java
//类似加数器，加到一定数量后才会执行某个线程

public static void main(String[] args){
        //相当于加数器
        CyclicBarrier cyclicBarrier = new CyclicBarrier(7, ()-> System.out.println("集齐7颗龙珠，召唤神龙"));
        for (int i = 1; i < 8; i++) {
            int finalI = i; // lambda表达式要用final变量
            new Thread(() -> {
                System.out.println(Thread.currentThread().getName()+"\t找到第"+ finalI +"颗龙珠");
                try {
                    cyclicBarrier.await();// 等待集齐龙珠 线程阻塞
                    System.out.println(Thread.currentThread().getName()+"：\t 收集完龙珠 欧耶");
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } catch (BrokenBarrierException e) {
                    e.printStackTrace();
                }
            }, String.valueOf(i)).start();
        }
    }
1	找到第1颗龙珠
4	找到第4颗龙珠
6	找到第6颗龙珠
2	找到第2颗龙珠
3	找到第3颗龙珠
5	找到第5颗龙珠
7	找到第7颗龙珠
集齐7颗龙珠，召唤神龙
3：	 收集完龙珠 欧耶
1：	 收集完龙珠 欧耶
2：	 收集完龙珠 欧耶
4：	 收集完龙珠 欧耶
7：	 收集完龙珠 欧耶
5：	 收集完龙珠 欧耶
6：	 收集完龙珠 欧耶
```



## Semaphore信号量

![image-20210331162255775](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210331162255.png)

**==java.util.concurrent.Semaphore;==**

```java
public static void main(String[] args){
        // 模拟7部车抢占3个车位
        Semaphore semaphore = new Semaphore(3); // 3个车位
        for (int i = 1; i <= 7; i++) {
            new Thread(() -> {
                try {
                    semaphore.acquire(); // 抢到车位
                    System.out.println(Thread.currentThread().getName()+"\t抢到了车位");
                    TimeUnit.SECONDS.sleep(new Random().nextInt(4)); //随机停车0~3秒
                    System.out.println(Thread.currentThread().getName()+"\t离开了车位");
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } finally {
                    semaphore.release(); // 释放车位
                }
            }, String.valueOf(i)).start();
        }
    }


2	抢到了车位
3	抢到了车位
1	抢到了车位
3	离开了车位
4	抢到了车位
1	离开了车位
5	抢到了车位
5	离开了车位
4	离开了车位
7	抢到了车位
2	离开了车位
6	抢到了车位
6	离开了车位
7	离开了车位
```



# ReadWriteLock

## ReadWriteLock相较于Lock的优点

![image-20210331164457750](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210331164457.png)

==Lock上述三种情况都不能共存==

## 出现的问题

```java
public class Main {
    public static void main(String[] args) {
        MyCache myCache = new MyCache();

        for (int i = 0; i < 4; i++) {
            int finalI = i;
            new Thread(() -> {
                myCache.put(finalI + "", finalI + "");
            }, String.valueOf(i)).start();
        }

        for (int i = 0; i < 4; i++) {
            int finalI = i;
            new Thread(() -> {
                myCache.get(finalI + "");
            }, String.valueOf(i)).start();
        }
    }
}

class MyCache {
    private volatile Map<String, Object> map = new HashMap<>();

    public void put(String key, Object value) {
        System.out.println(Thread.currentThread().getName() + " ----开始写入");
        //网络拥堵
        try {
            TimeUnit.MILLISECONDS.sleep(150);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        map.put(key, value);
        System.out.println(Thread.currentThread().getName() + " ----写入完成");
    }

    public void get(String key) {
        System.out.println(Thread.currentThread().getName() + " 开始读取");
        //网络拥堵
        try {
            TimeUnit.MILLISECONDS.sleep(100);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        Object res = map.get(key);
        System.out.println(Thread.currentThread().getName() + " 读取完成" + res);
    }
}
//不加锁
0 ----开始写入 // 一个线程写入时，其他线程又读又写，违反数据一致性
3 ----开始写入
1 ----开始写入
2 ----开始写入
0 开始读取
1 开始读取
2 开始读取
3 开始读取
1 读取完成null
3 读取完成null
2 读取完成null
0 读取完成null
0 ----写入完成
1 ----写入完成
3 ----写入完成
2 ----写入完成
    
// put与get添加synchronized或者lock，虽然避免了问题，但是读取数据时也加锁了，不允许其他线程读取。
0 ----开始写入
0 ----写入完成
3 开始读取
3 读取完成null
2 开始读取
2 读取完成null
1 开始读取
1 读取完成null
0 开始读取
0 读取完成0
3 ----开始写入
3 ----写入完成
2 ----开始写入
2 ----写入完成
1 ----开始写入
1 ----写入完成
```

## 解决方法

==ReadWriteLock既保证了数据一致性，又提高了并发性能（多个线程可同时读）==

```java
public class Main {
    public static void main(String[] args) {
        MyCache myCache = new MyCache();

        for (int i = 0; i < 4; i++) {
            int finalI = i;
            new Thread(() -> {
                myCache.put(finalI + "", finalI + "");
            }, String.valueOf(i)).start();
        }

        for (int i = 0; i < 4; i++) {
            int finalI = i;
            new Thread(() -> {
                myCache.get(finalI + "");
            }, String.valueOf(i)).start();
        }
    }
}

class MyCache {
    private volatile Map<String, Object> map = new HashMap<>();
    private ReadWriteLock lock = new ReentrantReadWriteLock(); //默认不公平

    public void put(String key, Object value) {
        lock.writeLock().lock(); // 加写锁
        try {
            System.out.println(Thread.currentThread().getName() + " ----开始写入");
            //网络拥堵
            TimeUnit.MILLISECONDS.sleep(150);
            map.put(key, value);
            System.out.println(Thread.currentThread().getName() + " ----写入完成");
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            lock.writeLock().unlock();
        }

    }

    public void get(String key) {
        lock.readLock().lock();// 加读锁
        try {
            System.out.println(Thread.currentThread().getName() + " 开始读取");
            //网络拥堵
            TimeUnit.MILLISECONDS.sleep(100);
            Object res = map.get(key);
            System.out.println(Thread.currentThread().getName() + " 读取完成" + res);
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            lock.readLock().unlock();
        }
    }
}
1 ----开始写入 // 1线程开始写数据，此时其他线程不可以 读 也不可以 写
1 ----写入完成
0 ----开始写入
0 ----写入完成
2 ----开始写入
2 ----写入完成
3 ----开始写入
3 ----写入完成
1 开始读取 // 1线程开始 读 数据，此时其他线程也可以 读
0 开始读取
3 开始读取
2 开始读取
1 读取完成1
0 读取完成0
2 读取完成2
3 读取完成3
```

# BlockQueue阻塞队列

## 概念

![image-20210331192249038](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210331192249.png)



## 好处

![image-20210331192446859](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210331192447.png)

==不在需要手动判断标志位，await、signal==

## 实现类

![image-20210331194348790](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210331194348.png)

![image-20210331194443374](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210331194443.png)

## 常用方法

![image-20210331194904689](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210331194904.png)

## 测试

```java
public class Main {
    public static void main(String[] args) {
        BlockingQueue<String> queue = new ArrayBlockingQueue<>(5);
        Random random = new Random();
        for (int i = 0; i < 2; i++) { //生产者2个
            new Thread(() -> {
                for (int j = 0; j < 10; j++) {
                    try {
                        System.out.println(Thread.currentThread().getName()+"生产了:"+j);
                        queue.put(j+"");
                        TimeUnit.SECONDS.sleep(random.nextInt(3));
                    } catch (Exception e) {
                        e.printStackTrace();
                    }
                }
            }, String.valueOf(i)).start();
        }

        for (int i = 2; i < 12; i++) { //消费者
            new Thread(() -> {
                for (int j = 0; j < 2; j++) {
                    try {
                        System.out.println(Thread.currentThread().getName()+"消费了："+queue.take());
                        TimeUnit.SECONDS.sleep(random.nextInt(2));
                    } catch (Exception e) {
                        e.printStackTrace();
                    }
                }
            }, String.valueOf(i)).start();
        }
    }
}

1生产了:0
0生产了:0
2消费了：0
3消费了：0
1生产了:1
6消费了：1
0生产了:1
7消费了：1
1生产了:2
4消费了：2
0生产了:2
1生产了:3
3消费了：2
2消费了：3
0生产了:3
1生产了:4
10消费了：3
0生产了:4
11消费了：4
1生产了:5
6消费了：4
5消费了：5
1生产了:6
8消费了：6
1生产了:7
9消费了：7
1生产了:8
0生产了:5
7消费了：8
1生产了:9
4消费了：5
5消费了：9
0生产了:6
8消费了：6
0生产了:7
10消费了：7
0生产了:8
0生产了:9
11消费了：8
9消费了：9
```



# ThreadPool线程池

![image-20210331214653024](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210331214653.png)

## 为什么使用线程池

![image-20210331213313938](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210331213314.png)

## 三种线程池

### FixedThreadPool

```java
public static void main(String[] args) {
        // 执行长期任务性能好，线程池线程数固定
        ExecutorService threadPool = Executors.newFixedThreadPool(5);
        Random random = new Random();
        try {
            for (int i = 0; i < 10; i++) {
                threadPool.execute(()->{
                    System.out.println(Thread.currentThread().getName()+"占用线程");
                    try { TimeUnit.SECONDS.sleep(random.nextInt(2)); } catch (InterruptedException e) { 							e.printStackTrace(); }
                    System.out.println(Thread.currentThread().getName()+"离开线程");
                });
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            threadPool.shutdown();
        }
    }

pool-1-thread-2占用线程
pool-1-thread-1占用线程
pool-1-thread-1离开线程
pool-1-thread-1占用线程
pool-1-thread-5占用线程
pool-1-thread-5离开线程
pool-1-thread-4占用线程
pool-1-thread-3占用线程
pool-1-thread-5占用线程
pool-1-thread-3离开线程
pool-1-thread-3占用线程
pool-1-thread-3离开线程
pool-1-thread-3占用线程
pool-1-thread-4离开线程
pool-1-thread-2离开线程
pool-1-thread-3离开线程
pool-1-thread-5离开线程
pool-1-thread-1离开线程
pool-1-thread-4占用线程
pool-1-thread-4离开线程

```

### SingleThreadExecutor

```java
public static void main(String[] args) {
        // 一个任务一个任务地执行，线程池线程数只有一个
        ExecutorService threadPool = Executors.newSingleThreadExecutor();
        Random random = new Random();
        try {
            for (int i = 0; i < 10; i++) {
                threadPool.execute(()->{
                    System.out.println(Thread.currentThread().getName()+"占用线程");
                    try { TimeUnit.SECONDS.sleep(random.nextInt(2)); } catch (InterruptedException e) { e.printStackTrace(); }
                    System.out.println(Thread.currentThread().getName()+"离开线程");
                });
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            threadPool.shutdown();
        }
    }

pool-1-thread-1占用线程
pool-1-thread-1离开线程
pool-1-thread-1占用线程
pool-1-thread-1离开线程
pool-1-thread-1占用线程
pool-1-thread-1离开线程
pool-1-thread-1占用线程
pool-1-thread-1离开线程
pool-1-thread-1占用线程
pool-1-thread-1离开线程
pool-1-thread-1占用线程
pool-1-thread-1离开线程
pool-1-thread-1占用线程
pool-1-thread-1离开线程
pool-1-thread-1占用线程
pool-1-thread-1离开线程
pool-1-thread-1占用线程
pool-1-thread-1离开线程
pool-1-thread-1占用线程
pool-1-thread-1离开线程
```



### CachedThreadPool

![image-20210331220705413](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210331220705.png)

```java
public static void main(String[] args) {
        // 线程池线程数有N个，可自动根据任务数量创建线程数
        ExecutorService threadPool = Executors.newCachedThreadPool();
        Random random = new Random();
        try {
            for (int i = 0; i < 10; i++) {
                threadPool.execute(()->{
                    System.out.println(Thread.currentThread().getName()+"占用线程");
                    try { TimeUnit.SECONDS.sleep(random.nextInt(2)); } catch (InterruptedException e) { e.printStackTrace(); }
                    System.out.println(Thread.currentThread().getName()+"离开线程");
                });
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            threadPool.shutdown();
        }
    }

pool-1-thread-1占用线程
pool-1-thread-4占用线程
pool-1-thread-6占用线程
pool-1-thread-2占用线程
pool-1-thread-3占用线程
pool-1-thread-2离开线程
pool-1-thread-8占用线程
pool-1-thread-5占用线程
pool-1-thread-6离开线程
pool-1-thread-7占用线程
pool-1-thread-3离开线程
pool-1-thread-7离开线程
pool-1-thread-9占用线程
pool-1-thread-9离开线程
pool-1-thread-10占用线程
pool-1-thread-10离开线程
pool-1-thread-8离开线程
pool-1-thread-1离开线程
pool-1-thread-5离开线程
pool-1-thread-4离开线程
```

## 底层原理

三种线程池的获得方法其实都是调用一个==ThreadPoolExecutor==构造函数

```java
// FixedThreadPool
/* @param nThreads the number of threads in the pool
 * @return the newly created thread pool
 * @throws IllegalArgumentException if {@code nThreads <= 0}
 */
 public static ExecutorService newFixedThreadPool(int nThreads) {
        return new ThreadPoolExecutor(nThreads, nThreads,
                                      0L, TimeUnit.MILLISECONDS,
                                      new LinkedBlockingQueue<Runnable>());
    }

// SingleThreadPool
public static ExecutorService newSingleThreadExecutor() {
        return new FinalizableDelegatedExecutorService
            (new ThreadPoolExecutor(1, 1,
                                    0L, TimeUnit.MILLISECONDS,
                                    new LinkedBlockingQueue<Runnable>()));
    }

// CachedThreadPool
public static ExecutorService newCachedThreadPool() {
        return new ThreadPoolExecutor(0, Integer.MAX_VALUE,
                                      60L, TimeUnit.SECONDS,
                                      new SynchronousQueue<Runnable>());
    }
```

### ThreadPoolExecutor构造函数

```java
 /**
     * Creates a new {@code ThreadPoolExecutor} with the given initial
     * parameters.
     *
     * @param corePoolSize the number of threads to keep in the pool, even
     *        if they are idle, unless {@code allowCoreThreadTimeOut} is set
     * @param maximumPoolSize the maximum number of threads to allow in the
     *        pool
     * @param keepAliveTime when the number of threads is greater than
     *        the core, this is the maximum time that excess idle threads
     *        will wait for new tasks before terminating.
     * @param unit the time unit for the {@code keepAliveTime} argument
     * @param workQueue the queue to use for holding tasks before they are
     *        executed.  This queue will hold only the {@code Runnable}
     *        tasks submitted by the {@code execute} method.
     * @param threadFactory the factory to use when the executor
     *        creates a new thread
     * @param handler the handler to use when execution is blocked
     *        because the thread bounds and queue capacities are reached
     * @throws IllegalArgumentException if one of the following holds:<br>
     *         {@code corePoolSize < 0}<br>
     *         {@code keepAliveTime < 0}<br>
     *         {@code maximumPoolSize <= 0}<br>
     *         {@code maximumPoolSize < corePoolSize}
     * @throws NullPointerException if {@code workQueue}
     *         or {@code threadFactory} or {@code handler} is null
     */
    public ThreadPoolExecutor(int corePoolSize,// 线程池中的常驻核心线程数，可为0
                              int maximumPoolSize,// 线程池中能够容纳同时执行的最大线程数，必须大于等于1
                              long keepAliveTime, // 多余的空闲线程的存活时间，当线程池中的线程数量超过        														corePoolSize，当空闲时间达到keepAliveTime时，多余														的线程会被销毁知道线程池中的线程数量达到corePoolSize为止
                              TimeUnit unit, // keepAliveTime的时间单位
                              BlockingQueue<Runnable> workQueue, // 任务队列，被提交但未被执行的队列
                              ThreadFactory threadFactory, // 表示生成线程池中工作线程的线程工厂，用于创建线程，一般默认
                              RejectedExecutionHandler handler) { // 拒绝策略，表示当workQueue满了，并且工作线程大于等															于maximumPoolSize时，如何拒绝请求来执行的Runnable的策略
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

### 工作原理

![image-20210402114545799](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210402114546.png)

![image-20210402114712677](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210402114712.png)



### 合理设置参数

#### CPU密集型任务

==如果任务是CPU密集型，则最大线程数maximum就是cpu核心数加1==



#### IO密集型任务

==CPU核心数 / 阻塞系数==





### 拒绝策略

![image-20210402223456662](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210402223503.png)

#### AbortPolicy

```java
// 如果要同时执行的线程数大于maximumPoolsize + BlockedQueue.size，将会抛出异常
// java.util.concurrent.RejectedExecutionException
public class Main {
    public static void main(String[] args) {
        ExecutorService threadPool = new ThreadPoolExecutor(2,
                5,
                2L,
                TimeUnit.SECONDS,
                new LinkedBlockingDeque<>(3),
                Executors.defaultThreadFactory(),
                new ThreadPoolExecutor.AbortPolicy()); // AbortPolicy
        Random random = new Random();
        try {
            for (int i = 1; i <= 9; i++) {
                threadPool.execute(() -> {
                    System.out.println(Thread.currentThread().getName()+"\t办理业务");
                    try { TimeUnit.SECONDS.sleep(random.nextInt(2)); } catch (InterruptedException e) { e.printStackTrace(); }

                });

            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            threadPool.shutdown();
        }
    }
}

pool-1-thread-1	办理业务
pool-1-thread-4	办理业务
pool-1-thread-3	办理业务
pool-1-thread-2	办理业务
pool-1-thread-5	办理业务
pool-1-thread-3	办理业务
pool-1-thread-4	办理业务
pool-1-thread-1	办理业务
java.util.concurrent.RejectedExecutionException: Task Main$$Lambda$1/1078694789@7ba4f24f rejected from java.util.concurrent.ThreadPoolExecutor@3b9a45b3[Running, pool size = 5, active threads = 4, queued tasks = 0, completed tasks = 4]
	at java.util.concurrent.ThreadPoolExecutor$AbortPolicy.rejectedExecution(ThreadPoolExecutor.java:2047)
	at java.util.concurrent.ThreadPoolExecutor.reject(ThreadPoolExecutor.java:823)
	at java.util.concurrent.ThreadPoolExecutor.execute(ThreadPoolExecutor.java:1369)
	at Main.main(Main.java:21)
```





#### CallerRunsPolicy

```java

ExecutorService threadPool = new ThreadPoolExecutor(2,
                                                    5,
                                                    2L,
                                                    TimeUnit.SECONDS,
                                                    new LinkedBlockingDeque<>(3),
                                                    Executors.defaultThreadFactory(),
                                                    new ThreadPoolExecutor.CallerRunsPolicy()); // CallerRunsPolicy
pool-1-thread-1	办理业务
pool-1-thread-3	办理业务
main	办理业务
pool-1-thread-2	办理业务
pool-1-thread-4	办理业务
pool-1-thread-1	办理业务
pool-1-thread-5	办理业务
pool-1-thread-4	办理业务
pool-1-thread-4	办理业务
```



#### DiscardOldestPolicy

```java
ExecutorService threadPool = new ThreadPoolExecutor(2,
                5,
                2L,
                TimeUnit.SECONDS,
                new LinkedBlockingDeque<>(3),
                Executors.defaultThreadFactory(),
                new ThreadPoolExecutor.DiscardOldestPolicy());
```



#### DiscardPolicy

```java
ExecutorService threadPool = new ThreadPoolExecutor(2,
                5,
                2L,
                TimeUnit.SECONDS,
                new LinkedBlockingDeque<>(3),
                Executors.defaultThreadFactory(),
                new ThreadPoolExecutor.DiscardPolicy());
```





## 如何选择线程池

==哪个都不用，使用自定义的==

![image-20210402121257166](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210402121257.png)

![image-20210402121314244](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210402121314.png)

![image-20210402121330345](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210402121330.png)

### 自定义线程池



# 分支合并框架

==分治+递归，不过是将分治的任务交给新的线程==

## ForkJoinPool

## ForkJoinTask

## RecursiveTask

![image-20210403103341075](https://cdn.jsdelivr.net/gh/Youenschang/picgo/img/20210403103348.png)

```java
class MyTask extends RecursiveTask<Integer> {

    private final Integer MIN_TASK = 10;
    private int start;
    private int end;
    private int result;

    public MyTask(int start, int end) {
        this.start = start;
        this.end = end;
        this.result = 0;
    }

    @Override
    protected Integer compute() {
        if ((end - start) < MIN_TASK) { // 子任务小于10不再分治递归
            for (int i = start; i <= end; i++) {
                result += i;
            }
        } else {
            int mid = (start + end) / 2;
            MyTask myTask1 = new MyTask(start, mid); // 分治
            MyTask myTask2 = new MyTask(mid + 1, end);
            myTask1.fork(); // 交给对等线程
            myTask2.fork();
            result = myTask1.join() + myTask2.join(); // 递归
        }
        return result;
    }
}

public class Main {
   // 多线程计算0到100的累计和
   public static void main(String[] args) throws Exception {
        MyTask myTask = new MyTask(0, 100);
        ForkJoinPool threadPool = new ForkJoinPool();
        ForkJoinTask<Integer> submit = threadPool.submit(myTask);
        System.out.println(submit.get());
        threadPool.shutdown();
    }

}
```

# 异步回调

## 无返回值异步回调

```java
public static void main(String[] args) throws Exception {
        // 开启新线程执行任务，无返回值
        CompletableFuture<Void> completableFuture = CompletableFuture.runAsync(() ->
                System.out.println(Thread.currentThread().getName() + "没有返回值，update sql ok"));
        // Waits if necessary for this future to complete, and then returns its result.
        // completableFuture.get(); 返回值为null
}
// 输出
ForkJoinPool.commonPool-worker-1没有返回值，update sql ok
```

## 有返回值异步回调

### 无异常

```java
public static void main(String[] args) throws Exception {
        CompletableFuture<Integer> completableFuture = CompletableFuture.supplyAsync(() -> {// supplyAsync
            System.out.println(Thread.currentThread().getName() + "\t执行任务");
            return 1024;// get()返回值
        });
        System.out.println(completableFuture.whenComplete((t, u) -> {
            System.out.println("u: " + u);
            System.out.println("t: " + t);

        }).exceptionally(f -> {
            System.out.println(f.getMessage());
            return 404;// get()返回值
        }).get());

    }
//输出
ForkJoinPool.commonPool-worker-1	执行任务
u: null
t: 1024
1024
```



### 有异常

```java
public static void main(String[] args) throws Exception {
        CompletableFuture<Integer> completableFuture = CompletableFuture.supplyAsync(() -> {// supplyAsync
            System.out.println(Thread.currentThread().getName() + "\t执行任务");
            System.out.println(1024 / 0);
            return 1024;  // get()返回值
        });
        System.out.println(completableFuture.whenComplete((t, u) -> {
            System.out.println("u: " + u);
            System.out.println("t: " + t);

        }).exceptionally(f -> {
            System.out.println(f.getMessage());
            return 404; // get()返回值
        }).get());

    }

// 输出
ForkJoinPool.commonPool-worker-1	执行任务
u: java.util.concurrent.CompletionException: java.lang.ArithmeticException: / by zero
t: null
java.lang.ArithmeticException: / by zero
404
```

