---
layout:     post
title:      java volatile学习笔记
subtitle:   java volatile
keyword:     java,volatile,并发
description : java volatile学习笔记
date:       2017-07-13
author:     licc
header-img: img/post_bg_debug.png
catalog: true
tags:
    - spring
    - guava
---
## 前言
   
   深入volatile需要对java内存模型有一定的了解，建议先看看[java内存模型](http://ifeve.com/java-memory-model-0/)
    
---    
## 一.相关资料
  
   1.  [java volatile 关键字解惑](http://www.jianshu.com/p/195ae7c77afe)
   2.  [单例模式、双检测锁定DCL、volatile（转）](http://crud0906.iteye.com/blog/576321)
   3.  [正确使用 Volatile 变量](https://www.ibm.com/developerworks/cn/java/j-jtp06197.html)
   4.  [聊聊并发（一）深入分析Volatile的实现原理](http://ifeve.com/volatile/)
   
## 二.volatile 注意事项

#### 运算结果并不依赖于当前值，或者能确保只有单一的线程能够修改变量的值  
    
   ```java
      import java.util.concurrent.Executor;
      import java.util.concurrent.ExecutorService;
      
      /**
       * 对变量的写操作不依赖于当前值。
       * @author lichangchao
       * @version 1.0.0
       * @date 2017/6/14 13:12
       * @see
       */
      public class Voliatile_error1  {
        public static volatile int i = 0;
        public static void main(String args[]){
      
          new Thread(new Runnable(){
            public void run(){
              for(int j = 0; j < 100; j++)
                i++;
              System.out.println("Thread1 end...");
            }
          }).start();
      
          new Thread(new Runnable(){
            public void run(){
              for(int j = 0; j < 100; j++)
                i++;
              System.out.println("Thread2 end...");
            }
          }).start();
      
          i++;
      
          try {
            Thread.sleep(500);
          } catch (InterruptedException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
          }
          System.out.println("i = " + i);
        }
      }
   ```
   
#### 该变量没有包含在具有其他变量的不变式中
  
  ```java
     /**
        *该变量没有包含在具有其他变量的不变式中。
        * @author lichangchao
        * @version 1.0.0
        * @date 2017/6/14 16:16
        * @see
        */
       public class Voliatile_error2 {
         private volatile int lower = 0;
         private volatile int upper = 10;
       
       
         public int getLower() { return lower; }
         public int getUpper() { return upper; }
       
         public void setLower(int value) {
           if (value > upper)
             throw new IllegalArgumentException("");
           lower = value;
         }
       
         public void setUpper(int value) {
           if (value < lower)
             throw new IllegalArgumentException("");
           upper = value;
         }
     
     
     }
      
   ```  
   
   
## 三.volatile 适用场景
####  状态标识
```java
/**
 * 调用setIsopen方法并设置isopen为true，由于isopen是volatile修饰的，所以一经修改，其他线程都可以拿到isopen的最新值，用户请求就可以执行促销逻辑了
 * @author lichangchao
 * @version 1.0.0
 * @date 2017/6/14 13:12
 * @see
 */
public class Volatile_success1 {
    private volatile boolean isopen;
    public void run() {
        if (isopen) {
            //促销逻辑
        } else {
            //正常逻辑
        }
    }
    public void setIsopen(boolean isopen) {
        this.isopen = isopen;
    }
}
```

####  doubleCheck单列
```java
/**
 * 对变量的写操作不依赖于当前值。
 * @author lichangchao
 * @version 1.0.0
 * @date 2017/6/14 13:12
 * @see
 */
class Singleton {
    private volatile static Singleton instance;
    public static Singleton getInstance() {
        if (instance == null) {
            synchronized (Singleton.class){
                if (instance == null) {
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }

}

//public class Singleton {
//    static class SingletonHolder {
//        static Singleton instance = new Singleton();
//    }
//
//    public static Singleton getInstance(){
//        return SingletonHolder.instance;
//    }
//}
```   
####  开销较低的读－写锁策略
```java
/**
 * 对变量的写操作不依赖于当前值。
 * @author lichangchao
 * @version 1.0.0
 * @date 2017/6/14 13:12
 * @see
 **/
public class CheesyCounter {

    private volatile int value;
 
    public int getValue() { return value; }
 
    public synchronized int increment() {
        return value++;
    }
}
```