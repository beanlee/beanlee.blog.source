---
layout: post
title : Java基础集合类复习
category : 技术
tagline: ""
tags : [Java, 总结]
description : Java基础集合类的问题简单整理
date: 2014-06-06 00:00:01
---

Java基础集合类的问题简单整理

<!--more-->

# JAVA基础集合类Review

##JAVA集合类分类
* Collection：通常是一组有一定规律的独立元素。List必须按照特定的顺序持有这些元素，而Set则不能保存重复的元素。
* Map：一组以“键－－值”（key-value）形式出现的pairs。


### Collection Interface
Collection是最基本的集合接口，它下面的实现类包括List(LinkedList, ArrayList,Vector)、Set等。

* List接口是有序的Collection，每一个元素都能够被用户使用索引来访问，类似Java中的数组。与Set不同，List允许存在相同的元素。

  LinkedList:它是有序的允许元素为null，并且提供额外的get，remove，insert方法来操作LinkedList的首或尾部，类似链表的结构，可以用来实现堆栈（Stack）、队列（Queue）或者双向队列（Deque），它是不同步的。

  ArrayList:它的内部实现机制实际上使用的是Object数组，且一个大小可变，但是每一个ArrayList在初始化时都有一个容量，外部看这个容量是可以根据元素的添加自动增加。当容量不够时，系统就会创建一个更大的数组（1.5倍左右）并且把老的数组完全拷贝到新的数组中，同时老数组就自动进入GC待处理状态，等待垃圾回收。

  相比LinkedList，ArrayList中的get(index)的方法速度较快，效率高。

  Vector:线程同步的ArrayList，当一个线程开始迭代Vector时，另一个线程来添加、删除等改变Vector时，就会抛出ConcurrentModificationException。

  Stack:继承自Vector，从名字可以看出堆栈，它实现了一个后进先出的堆栈。


* Set接口是一种不允许重复的Collection，即任意的两个元素e1和e2都有e1.equals(e2)=false，Set最多有一个null元素。这其中equals方法就是依靠元素的hashCode来判断是否相等，有时可以通过重写一些对象的HashCode方法来改变判断相等的判断逻辑。

### Map Interface

* HashTable
  
  HashTable集成Map接口，实现了一个key-value映射的哈希表。任何非空（non-null）的对象都可以作为key或value。HashTable是线程同步的。
  
  不了解的知识点：Hashtable通过initial capacity和load factor两个参数调整性能。通常缺省的load factor 0.75较好地实现了时间和空间的均衡。增大load factor可以节省空间但相应的查找时间将增大，这会影响像get和put这样的操作。
  
  hashCode和equals方法是否重写相关：
  
* HashMap
  
  HashMap和Hashtable类似，不同之处在于HashMap是非同步的，并且允许null，即null value和null key。，但是将HashMap视为Collection时（values()方法可返回Collection），其迭代子操作时间开销和HashMap 的容量成比例。因此，如果迭代操作的性能相当重要的话，不要将HashMap的初始化容量设得过高，或者load factor过低。
  
* WeakHashMap

  WeakHashMap是一种改进的HashMap，它对key实行“弱引用”，如果一个key不再被外部所引用，那么该key可以被GC回收。实际开发中还没有用过，可以尝试用一下。

## 总结

* List集合是有序集合，集合中的元素可以重复，访问集合中的元素可以根据元素的索引来访问。
* Set集合是无序集合，集合中的元素不可以重复，访问集合中的元素只能根据元素本身来访问（也是不能集合里元素不允许重复的原因）。
* Map集合中保存Key-value对形式的元素，访问时只能根据每项元素的key来访问其value。
* 如果涉及到堆栈，队列等操作，应该考虑用List，对于需要快速插入，删除元素，应该使用LinkedList，如果需要快速随机访问元素，应该使用ArrayList。
* 如果程序在单线程环境中，或者访问仅仅在一个线程中进行，考虑非同步的类，其效率较高，如果多个线程可能同时操作一个类，应该使用同步的类。要特别注意对哈希表的操作，作为key的对象要正确复写equals和hashCode方法。
* 尽量返回接口而非实际的类型，如返回List而非ArrayList，这样如果以后需要将ArrayList换成LinkedList时，客户端代码不用改变。这就是针对抽象编程。

## 参考
[柳志超Blog中一篇文章《Java集合类》](http://liuzhichao.com/p/652.html)

