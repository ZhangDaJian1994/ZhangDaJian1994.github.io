---
layout:     post                    # 使用的布局（不需要改）
title:      String StringBuffer StringBuild 区别               # 标题 
subtitle:   Hello World, Hello Blog #副标题
date:       2019-06-15              # 时间
author:   Jiafeimao                      # 作者
#header-img: img/post-bg-2015.jpg    #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
    - JAVA
---





**String** 是Java 语言中非常重要的基础类，被申明为final class，所有的属性都是final。由于它的不可变性，类似拼接、裁剪字符串等行为，都会产生新的String 对象。由于字符串操作的普遍性，所以相关操作对应用性能有明显影响。

**StringBuffer** 是为了解决上面拼接、裁剪产生太多中间对象而提供的一个类，可以通过append、add方法，把字符串拼接起来。StringBuffer 本质上是一个线程安全的可修改字符序列，保证了线程安全，随之也带来了性能开销，所以除非有线程安全的需要，否则推荐使用StringBuild。

**StringBuild** 本质上和StringBuffer 没有区别，只不过StringBuild 去掉了线程安全的部分，提高了性能。



### 知识扩展

------

1. 字符串的设计与考量

   String 的final 设计使得它可以原生的线程安全，因为你无法对其内部数据进行修改

   ```java
   private final char value[];
   ```

   StringBuffer 的实现细节，所有的方法都加上了synchronized 关键字

   ```java
   @Override
       public synchronized StringBuffer append(float f) {
           toStringCache = null;
           super.append(f);
           return this;
       }
   
       @Override
       public synchronized StringBuffer append(double d) {
           toStringCache = null;
           super.append(d);
           return this;
       }
   ```

2. 字符串缓存

   在一般应用中，越有25%的对象是字符串，并且其中约一半是重复的，String 在 Jdk 6后提供了intern() 方法，目的是提示JVM 把相应字符串缓存起来，以备重复使用。在我们创建字符串对象并调用intern() 方法的时候，如果已经有已缓存的字符串，就会返回缓存里的实例，否则将其缓存起来。

   但Jdk 6中并不推荐使用intern()，因为在Jdk 6 中，字符串被缓存在PermGen 中，也就是臭名昭著的“永久代”，这个空间是有限的，也基本不会被Full GC 之外的垃圾回收照顾到。所以很容易出现OOM异常。

   在后续版本中，缓存设置在堆中，极大避免了永久代被占满的问题

   另外，在Jdk 9中 String 的底层实现由char[] 改为了 byte[] 和编码coder，这样更节省空间。