---
title: 冒泡排序
date: 2016-08-02 16:54:36
description: 排序算法之冒泡排序
categories: 
- algorithm
tags:
- algorithm
---

冒泡算法是一种简单的交换排序算法，它的平均时间复杂度为O(n^2)，这就导致它在处理大数据量时效率非常低。

## 主要思想
1. 从头开始比较相邻的两个元素，如果它们的顺序与所需的结果相反，则交换它们的位置。
2. 如果该轮比较完，按照步骤1进行下一轮比较

通过每次的比较交换可以发现，大数会逐步移动到后面（假设从小到大排序），可以根据下面的图来理解：
假设待比较的数组为：{5, 1, 12, -5, 16}
![](/assert/algorithm/algorithm-sort-bubble/bubble-sort-1.png)

根据上图，我们可以写出大概的代码：
```java
public void bubble(int[] arrs){
    int tmp;
    int len = arrs.length;
    for (int i = 0; i < len - 1; i++) { // 控制次数
        for (int j = 0; j < len - i - 1; j++) { // 控制相邻的两个数
            if(arrs[j] > arrs[j + 1]){
                tmp = arrs[j];
                arrs[j] = arrs[j + 1];
                arrs[j + 1] = tmp;
            }
        }
    }
}

```

## 复杂度分析
通过以上程序可以看出，它的时间复杂度是O(n^2)，那么有没有情况让时间复杂度再小一点呢？来看下面两个数组：
假设数组为：{2, 3, 4, 5, 1}
![](/assert/algorithm/algorithm-sort-bubble/bubble-sort-2.png)

从图中可以看出，该次排序需要花费O(n^2)才能完成。
假设另一个数组为：{6, 1, 2, 3, 4, 5}
![](/assert/algorithm/algorithm-sort-bubble/bubble-sort-3.png)

可以看出，该次排序其实只需要O(n)就能完成，如果按照之前的程序，将花费不必要的循环和判断。

## 优化
为解决该问题，我们可以添加一个变量来记录在某一轮排序过程中是否交换过数据，则说明数据已经按照要求排列好，程序可以结束，避免不必要的循环比较。
```java
public void bubbleSort3(int[] arrs) {
    boolean swapped = true;
    int j = 0;
    int tmp;
    while (swapped) {
        swapped = false;
        j++;
        for (int i = 0; i < arrs.length - j; i++) {
            if (arrs[i] > arrs[i + 1]) {
                tmp = arrs[i];
                arrs[i] = arrs[i + 1];
                arrs[i + 1] = tmp;
                swapped = true;
            }
        }
    }
}
```

## 参考
1. [Bubble Sort](http://www.algolist.net/Algorithms/Sorting/Bubble_sort)

