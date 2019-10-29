---
title: Java容器之TreeMap & TreeSet
typora-root-url: ../../source/
date: 2019-10-25 14:54:15
tags:
- 集合
- Java
---





TreeMap是一种基于红黑树实现的Map结构，Map的元素存储在红黑树的节点上（不同于HashMap的桶+链表）,从而保证Key的有序性。TreeSet是一种能保证元素有序的集合，基于TreeMap实现，即集合中的元素全部是TreeMap的Key。

<!--more-->

# TreeMap

## 简介

- 基于红黑树实现了*NavigableMap*接口
- 基于自然排序或者传入的*Comparator*对Key进行排序
- 保证containsKey() get() put() remove()操作的时间复杂度为*log(n)*
- 非线程安全
- iterator fail-fast

## 对比HashMap

- HashMap使用hashCode()定位桶，使用equals()定位元素；TreeMap使用compare()/compareTo()定位元素。

  所以HashMap的Key需重写hashCode()和hashCode()，而TreeMap需要在构造器中传入Comparator或者Key实现Comparable。

- HashMap使用桶+链表/红黑树存储；TreeMap直接使用红黑树存储，并提供了二叉搜索树相关的操作。

## 类图

<img src="/imgs/1572003543338.png" alt="1572003543338" style="zoom:50%;" />



# TreeSet 

## 简介

- *TreeSet* 位于*java.util*包下。它是一个基于*TreeMap*的*NavigableSet*类的实现。

- 容器内元素有序：基于自然排序或者在创建*TreeSet*时提供*Comparator*，取决于使用哪个构造器。

- 保证*add() remove() contains()*操作的时间复杂度为*log(n)*

- 非线程安全

- 使用fail-fast策略，可能抛出ConcurrentModificationException异常，但是程序不能依赖该异常。

## 类图

<img src="/imgs/1572000336972.png" alt="1572000336972" style="zoom:50%;" />

