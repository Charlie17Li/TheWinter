- [Java并发编程](#java%E5%B9%B6%E5%8F%91%E7%BC%96%E7%A8%8B)
  - [线程不安全的代码](#%E7%BA%BF%E7%A8%8B%E4%B8%8D%E5%AE%89%E5%85%A8%E7%9A%84%E4%BB%A3%E7%A0%81)
  - [java并发两个基本的关键字](#java%E5%B9%B6%E5%8F%91%E4%B8%A4%E4%B8%AA%E5%9F%BA%E6%9C%AC%E7%9A%84%E5%85%B3%E9%94%AE%E5%AD%97)
    - [Synchronize](#synchronize)
    - [Volatile](#volatile)
## Java并发编程

### 线程不安全的代码
> 1. 读取-修改-写入
> 2. 先检查后执行
> 3. 发布未完整构造的对象

**第一种 消失的请求数**
```java
public class UnsafeCountingServlet extends GenericServlet implements Servlet {
    private long count = 0;  //计数器

    public void service(ServletRequest serquest){
        ++count;    
        //To do something else...
    }
    /*
        ++count; 分成了三个动作，当有多个线程读写的时候，容易出错;
    */
}
```

**第二种 意外怀孕**
```java
public class UnsafeSingleton{
    private static UnsafeSingleton instance = null;

    public static UnsafeSingleton getInstace(){
        if(instance == null)
            instance = new UnsafeSingleton();

        return instance;
    }

    /*
        1. 在单线程下，绝不会生二胎，而在多线程下可能意外怀孕.
    */
}
```

**第三种 考试泄题**
```java
public class ThisEscape{
    private final List<Event> listOfEvents;

    public ThisEscape(EventSource source){
        //给事件源添加监听器
        source.registerListener(new EventListener(){
            public void onEvent(Event e){
                doSomething(e);
            }
        });
        listOfEvents = new ArrayList<Event>();
    }
    void doSomething(Event e){
        this.listOfEvents.add(e);
    }

    /*
        问题在于ThisEscape还没有实例化完成之前，就把this对象泄露出去，
        使得外部可以调用实例对象的方法.   
    */
}
```

**第三种变体 半成品**
```java
public class StuffIntoPublic{
    public Holder holder;
}
public class Holder{
    private int n;

    public Holder(int n){
        this.n = n;
    }

    public void assertSanity(){
        if(n != n)
            throw new AssertionError("this statement is false.");
    }
}
```

### java并发两个基本的关键字

#### Synchronize
> 当它用来修饰一个方法或者一个代码的时候，能够保证在同一时刻最多只有一个线程执行盖段代码

需要思考的问题：
- 如何保证同一时刻最多只有一个线程执行该段代码
- 这种保证，有何意义？

首先进入一个线程，然后拿到那个锁的唯一一把钥匙，则其他线程只能 阻塞状态，等到该线程走出 Synchronize后就会释放锁，然后其他线程争夺锁.
```java
synchronize(this){
    //这种方式，适用于修饰非静态方法：将对象实例作为锁。
}
```

所有的加锁行为，都可以带来两个保障：`原子性`和`可见性`.

锁的持有者是**线程**,而不是调用.

```java
public class Widget {
    public synchronized void doSomething() {
        ...
    }
}

public class LoggingWidget extends Widget {
    public synchronized void doSomething() {
        System.out.println(toString() + ": calling doSomething");
        super.doSomething();
    }
}
/*这种情况，并不会发生死锁？*/
``` 
*什么是内置锁？* --java中每个对象都有一把与之关联的锁.
*什么是重入？* --以上就是，理解此时只有一个对象，即只有一把钥匙.

#### Volatile


