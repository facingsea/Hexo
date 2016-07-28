---
title: ArrayList扩容问题
date: 2016-07-28 23:28:20
description: ArrayList扩容问题
categories: 
- java
tags:
- java
---

> 
本文使用JDK1.8

当使用默认形式创建ArrayList时：
```java
ArrayList list = new ArrayList();
```
查看ArrayList源码：
```java
/**
 * Constructs an empty list with an initial capacity of ten.
 */
public ArrayList() {
    this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
}
```
可以看出，只是一个简单的赋值操作，那么`elementData`和`DEFAULTCAPACITY_EMPTY_ELEMENTDATA`又是做什么的呢，来看源码：
```java
/**
 * Shared empty array instance used for default sized empty instances. We
 * distinguish this from EMPTY_ELEMENTDATA to know how much to inflate when
 * first element is added.
 */
private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};

/**
 * The array buffer into which the elements of the ArrayList are stored.
 * The capacity of the ArrayList is the length of this array buffer. Any
 * empty ArrayList with elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA
 * will be expanded to DEFAULT_CAPACITY when the first element is added.
 */
transient Object[] elementData; // non-private to simplify nested class access

```
从注释中可以看出，`elementData`是存储ArrayList元素的数组缓存，这个缓存数组的长度就是该ArrayList的容量，任何空的ArrayList的初始值都是`DEFAULTCAPACITY_EMPTY_ELEMENTDATA`，并且当第一个元素添加时其容量将会变为`DEFAULT_CAPACITY`的值。而`DEFAULTCAPACITY_EMPTY_ELEMENTDATA`是一个空的共享的数组，是空元素的默认值，将他和`EMPTY_ELEMENTDATA`比对可以知道当地一个元素添加时需要扩容的大小值。
来看`DEFAULT_CAPACITY`和`EMPTY_ELEMENTDATA`的定义：
```java
/**
 * Default initial capacity.
 */
private static final int DEFAULT_CAPACITY = 10;

/**
 * Shared empty array instance used for empty instances.
 */
private static final Object[] EMPTY_ELEMENTDATA = {};
```
可以看出，默认情况下，ArrayList的大小值是10，而`EMPTY_ELEMENTDATA`也是一个空数组，从注释上看的话似乎跟`DEFAULTCAPACITY_EMPTY_ELEMENTDATA`差不多，继续往下看。

当向list中添加元素时：
```java
list.add("zhangsan");
```
查看add方法源码:
```java
/**
 * The size of the ArrayList (the number of elements it contains).
 *
 * @serial
 */
private int size;

/**
 * Appends the specified element to the end of this list.
 *
 * @param e element to be appended to this list
 * @return <tt>true</tt> (as specified by {@link Collection#add})
 */
public boolean add(E e) {
    ensureCapacityInternal(size + 1);  // Increments modCount!!
    elementData[size++] = e;
    return true;
}
```
可以看出，`ensureCapacityInternal()`方法是来检测是否扩容的，参数为`size + 1`， size默认为0，也就是说要检查新加元素后list的大小。然后进入到`ensureCapacityInternal()`方法中：
```java
protected transient int modCount = 0;

private void ensureCapacityInternal(int minCapacity) {
    if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
        minCapacity = Math.max(DEFAULT_CAPACITY, minCapacity);
    }

    ensureExplicitCapacity(minCapacity);
}

private void ensureExplicitCapacity(int minCapacity) {
    modCount++;

    // overflow-conscious code
    if (minCapacity - elementData.length > 0)
        grow(minCapacity);
}
```
在`ensureCapacityInternal()`方法中，首先判断了当前`elementData`是否为`DEFAULTCAPACITY_EMPTY_ELEMENTDATA`，从之前默认构造中知道，该条件是成立的，在if条件中会取`DEFAULT_CAPACITY`和`minCapacity`中的最大值，然后赋给`minCapacity`，也就是说，如果当前大小小于10的话，会默认给10，然后当`minCapacity`的值大于`elementData`时，会执行`grow()`方法。
```java
/**
 * The maximum size of array to allocate.
 * Some VMs reserve some header words in an array.
 * Attempts to allocate larger arrays may result in
 * OutOfMemoryError: Requested array size exceeds VM limit
 */
private static final int MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8;

/**
 * Increases the capacity to ensure that it can hold at least the
 * number of elements specified by the minimum capacity argument.
 *
 * @param minCapacity the desired minimum capacity
 */
private void grow(int minCapacity) {
    // overflow-conscious code
    int oldCapacity = elementData.length;
    int newCapacity = oldCapacity + (oldCapacity >> 1);
    if (newCapacity - minCapacity < 0)
        newCapacity = minCapacity;
    if (newCapacity - MAX_ARRAY_SIZE > 0)
        newCapacity = hugeCapacity(minCapacity);
    // minCapacity is usually close to size, so this is a win:
    elementData = Arrays.copyOf(elementData, newCapacity);
}

private static int hugeCapacity(int minCapacity) {
    if (minCapacity < 0) // overflow
        throw new OutOfMemoryError();
    return (minCapacity > MAX_ARRAY_SIZE) ?
        Integer.MAX_VALUE :
        MAX_ARRAY_SIZE;
}
```
可以看出此时扩容的算法为`oldCapacity + (oldCapacity >> 1)`，等价于`1.5 * oldCapacity`，即新的容量为原来（包含了新增的一个）的1.5倍，然后又比对`newCapacity`和`MAX_ARRAY_SIZE`大小，`MAX_ARRAY_SIZE`默认为`Integer.MAX_VALUE - 8`，如果`newCapacity`较大，将会把容量扩充为`Integer.MAX_VALUE`。

总结
1. 当new ArrayList()时是不会扩容的。
2. 当new ArrayList(20)时，会扩容一次。
3. 扩容时，增幅为新增元素后容量的一半。
4. ArrayList最大容量为Integer.MAX_VALUE。
