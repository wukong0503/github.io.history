---
layout: post
title: "Java Object类"
date: 2017-09-01
categories: Java
tags: Java
---



## 1. 概述

Object类在java.lang包中，是所有java类的终极父类。在不明确给出超类的情况下，Java会自动把Object作为要定义类的超类。
接口是不能继承Object类的，即“Object类不作为接口的父类”。

## 2. API预览

```
package java.lang;

public class Object {

    /**
    * registerNatives函数前面有native关键字修饰，
    * Java中，用native关键字修饰的函数表明该方法的实现并不是在Java中去完成，
    * 而是由C/C++去完成，并被编译成了.dll，由Java去调用。
    * 方法的具体实现体在dll文件中，对于不同平台，其具体实现应该有所不同。
    * 用native修饰，即表示操作系统，需要提供此方法，Java本身需要使用。
    * 具体到registerNatives()方法本身，其主要作用是将C/C++中的方法映射到Java中的native方法，
    * 实现方法命名的解耦。
    */
    private static native void registerNatives();

    // 返回一个对象的运行时类。
    public final native Class<?> getClass();

    /**  
     * hashCode 的常规协定是：   
     * 1.在 Java 应用程序执行期间，在对同一对象多次调用 hashCode 方法时，
     *   必须一致地返回相同的整数，前提是将对象进行 equals 比较时所用的信息没有被修改。
     *   从某一应用程序的一次执行到同一应用程序的另一次执行，该整数无需保持一致。  
     *   
     * 2.如果根据 equals(Object) 方法，两个对象是相等的，
     *   那么对这两个对象中的每个对象调用 hashCode 方法都必须生成相同的整数结果。
     *     
     * 3.如果根据 equals(java.lang.Object) 方法，两个对象不相等，
     *   那么对这两个对象中的任一对象上调用 hashCode 方法不要求一定生成不同的整数结果。
     *   但是，程序员应该意识到，为不相等的对象生成不同整数结果可以提高哈希表的性能。   
     */   
    public native int hashCode();

    // 指示某个其他对象是否与此对象“相等”。
    public boolean equals(Object var1) {
        return this == var1;
    }

    // 创建并返回对象的一个副本
    protected native Object clone() throws CloneNotSupportedException;

    public String toString() {
        return this.getClass().getName() + "@" + Integer.toHexString(this.hashCode());
    }

    // 唤醒在此对象监视器上等待的单个线程。
    public final native void notify();

    // 唤醒在此对象监视器上等待的所有线程。
    public final native void notifyAll();

    // 导致当前的线程等待，直到其他线程调用此对象的 notify() 方法或 notifyAll() 方法，或者超过指定的时间量。
    public final native void wait(long var1) throws InterruptedException;

    // 导致当前的线程等待，直到其他线程调用此对象的 notify() 方法或 notifyAll() 方法，或者其他某个线程中断当前线程，或者已超过某个实际时间量。
    public final void wait(long var1, int var3) throws InterruptedException {
        if (var1 < 0L) {
            throw new IllegalArgumentException("timeout value is negative");
        } else if (var3 >= 0 && var3 <= 999999) {
            if (var3 > 0) {
                ++var1;
            }

            this.wait(var1);
        } else {
            throw new IllegalArgumentException("nanosecond timeout value out of range");
        }
    }

    public final void wait() throws InterruptedException {
        this.wait(0L);
    }

    // 当垃圾回收器确定不存在对该对象的更多引用时，由对象的垃圾回收器调用此方法。
    protected void finalize() throws Throwable {
    }

    static {
        registerNatives();
    }
}
```


{% include comments.html %}
