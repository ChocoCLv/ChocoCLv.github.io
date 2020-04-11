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

## API
```Java
// 返回(大于等输入key)的最小的key/entry,不存在返回null
Entry<K, V>                ceilingEntry(K key)
K                          ceilingKey(K key)
void                       clear()
Object                     clone()
// 返回comparator
Comparator<? super K>      comparator()
boolean                    containsKey(Object key)
// 降序返回key/map
NavigableSet<K>            descendingKeySet()
NavigableMap<K, V>         descendingMap()
Set<Entry<K, V>>           entrySet()
// 返回第一个key/entry
Entry<K, V>                firstEntry()
K                          firstKey()
// 返回(小于等于输入key)的最大的key/entry,不存在返回null
Entry<K, V>                floorEntry(K key)
K                          floorKey(K key)
V                          get(Object key)
// 返回优先级高于指定k的部分map,inclusive为是否包含当前key
NavigableMap<K, V>         headMap(K to, boolean inclusive)
SortedMap<K, V>            headMap(K toExclusive)
// 返回大于给定key的第一个节点
Entry<K, V>                higherEntry(K key)
K                          higherKey(K key)
boolean                    isEmpty()
Set<K>                     keySet()
// 最后一个key/entry
Entry<K, V>                lastEntry()
K                          lastKey()
// 返回小于给定key的第一个节点
Entry<K, V>                lowerEntry(K key)
K                          lowerKey(K key)
// 返回NavigableSet,可以导航..有low/high等方法
NavigableSet<K>            navigableKeySet()
// 弹出第一个key/entry
Entry<K, V>                pollFirstEntry()
Entry<K, V>                pollLastEntry()
V                          put(K key, V value)
V                          remove(Object key)
int                        size()
SortedMap<K, V>            subMap(K fromInclusive, K toExclusive)
NavigableMap<K, V>         subMap(K from, boolean fromInclusive, K to, boolean toInclusive)
// 返回尾部map,小于给定k,inclusive为控制是否包含
NavigableMap<K, V>         tailMap(K from, boolean inclusive)
SortedMap<K, V>            tailMap(K fromInclusive)
```

# TreeSet 

## 简介

- *TreeSet* 位于*java.util*包下。它是一个基于*TreeMap*的*NavigableSet*类的实现。

- 容器内元素有序：基于自然排序或者在创建*TreeSet*时提供*Comparator*，取决于使用哪个构造器。

- 保证*add() remove() contains()*操作的时间复杂度为*log(n)*

- 非线程安全

- 使用fail-fast策略，可能抛出ConcurrentModificationException异常，但是程序不能依赖该异常。

## 类图

<img src="/imgs/1572000336972.png" alt="1572000336972" style="zoom:50%;" />

```Java
boolean                   add(E object)
boolean                   addAll(Collection<? extends E> collection)
void                      clear()
Object                    clone()
boolean                   contains(Object object)
// 返回第一个/最后一个元素
E                         first()
E                         last()
boolean                   isEmpty()
// 弹出第一个或者最后一个元素
E                         pollFirst()
E                         pollLast()
// 返回大于/小于给定元素的元素
E                         higher(E e)
E                         lower(E e)
// 返回小于/大于给定元素的最大/最小的一个
E                         floor(E e)
E                         ceiling(E e)
boolean                   remove(Object object)
int                       size()
Comparator<? super E>     comparator()
Iterator<E>               iterator()
// 降序遍历
Iterator<E>               descendingIterator()
// 返回大于/小于给定元素的所有元素集合,endInclusive为是否包含的控制量
SortedSet<E>              headSet(E end)
NavigableSet<E>           headSet(E end, boolean endInclusive)
SortedSet<E>              tailSet(E start)
NavigableSet<E>           tailSet(E start, boolean startInclusive)
// 降序的set
NavigableSet<E>           descendingSet()
// 子集合
SortedSet<E>              subSet(E start, E end)
NavigableSet<E>           subSet(E start, boolean startInclusive, E end, boolean endInclusive)
```