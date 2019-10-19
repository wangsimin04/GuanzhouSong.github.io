---
layout: post
title:  Introduction to Algorithms(1) Sorting
date:   2017-08-24 02:00:00 +0800
categories: 不周山
tag: 数据结构与算法
---


* content
{:toc}

# Introduction to Algorithms
![](/images/TIM_jietu_20170822110218.png)

CLSR is quite a book that almost every programmer have to read it. Hard it may be, I still try to read it.

## Divide-and-Conquer
This chapter mainly foucus on the Divide-and-Conquer method, and some of its implementation.

Devide and Conquer contains three parts:

1、Divide。divide the problem into several smaller subproblems.

2、Conquer。Solving the subproblems by using recursion.

3、Combine。put all the result together and get the finial result.

### Strassen's algorithm
it usually takes O(n<sup>3</sup>) running time to calculate a matrix。

Below is the Divide&Conquer method to slove the matrix mutiplation in O(n<sup>3</sup>)

![](/images/1353940390_8791.png)

And for the Strassen's algorithm，it narrow down the 8 times mutiplation to 7, so the running time is O(n<sup>lg7</sup>)。

![](/images/1353941151_7478.png)

Below is the analysis of the running time：

![](/images/1353941397_9672.png)

Although it only narrow down the running time from O(n<sup>3</sup>)to O(n<sup>2.81</sup>)，it's still optimal when the n is large.

### The Master Theorem
It's hard to calculate the running time of recursion, so we need our friend to help, that is the Master Theory.

![](/images/TIM_jietu_20170822142419.png)

Worth noticing the case3，which requires that f(n) pictorially larger than n<sup>log<sub>b</sub>a</sup>。It means that not only should the f(n) numerically larger than it, it have to larger in exponentiation, or f(n)/n<sup>log<sub>b</sub>a</sup> = n<sup>&lambda;</sup> where &lambda;>0

Here is a inappropriate example：T(n) = 2 * T(n/2) + nlgn

f(n)=nlgn，n^log（b,a)=n，it looks like f(n) is larger than n，but in fact you cannot implement the Master Theory because f(n) is not exponentiationly, or polynomically larger than n.

# Sorting
Sorting is the most common algorithm but each of them has its own charactar (in-place, comparison, stable etc.)

Below is the running time of different algorithms:

![](/images/TIM_jietu_20170822145332.png)

## HeapSort
An algorithm based on heap, the main idea is by keep the relative relationship between parent and child node to build a heap.

> A[PARENT(i)] >= A[i]
> the value of a node is at most the value of its parent

### Heapify

![](/images/TIM_jietu_20170822150757.png)

MAX-HEAPIFY lets the value at A[i] “float down” in the max-heap so that the subtree rooted at index i obeys the max-heap property.

for example we start from i=2, floating down until it goes into the right position.

![](/images/TIM_jietu_20170822150947.png)

### Build Max Heap

![](/images/TIM_jietu_20170822151616.png)

Above shows how to build a max heap from the array. First we put all the elements into heap, no matter large or small, and by running BUILD-MAX-HEAP(A), we can make it become a max heap. Notice that we do not need to run BUILD-MAX-HEAP(A) for every nodes, instead we only need to run from i = n/2, so we are guaranteed to run BUILD-MAX-HEAP(A) for every nodes who have child.

![](/images/max_heap_build.png)

when i = 10, starting from i=5 can make all the parent nodes heapify.

![](/images/TIM_jietu_20170822153508.png)

Step-by-step, we can see that the HEAPSORT is based on the idea that the root node is always the largest node in the heap.

Also, the difference between heap and binary tree is that in heap, we can only make sure that parent>child, but no one knows the relation between two children.

![](/images/TIM_jietu_20170822153932.png)

### Insertion
This function show how to insert an element into the max_heap, the main idea is that it add a leaf node whose value is negative Infinite, and then replace the value with the key and keep comparing with its own parent node until it satisties the MAX-HEAP property.

![](/images/TIM_jietu_20170822155227.png)

![](/images/TIM_jietu_20170822155249.png)

## QuickSort
The quicksort algorithm has a worst-case running time of O(n^2) on an input array of n numbers. Despite this slow worst-case running time, quicksort is often the **best practical choice** for sorting because it is remarkably efficient on the average: its expected running time is O(nlgn), and the constant factors hidden in the O(nlgn) notation are quite small. It also has the advantage of sorting in place.
### partition
This simple function is the whole idea about QuickSort which all based on recursion.

![](/images/TIM_jietu_20170822160606.png)

It's obvious that the whole idea is about **PARTITION**

![](/images/TIM_jietu_20170822160744.png)

PARTITION simply using what-so-called "pivot" to put all the elements into two parts :those smaller than pivot and those larger.

as it show in the picture, the i and j go through all the elements and compare the element with pivot to decide where to put the element. So it's in place, which means it does not require extra memory.

![](/images/TIM_jietu_20170822161222.png)

### Preformance
Then let's analyse the preformance.

The worstest running time is

**T(n) = T(n-1) + O(n) ==> O(n^2)**

But as we previously say, it's the worstest running time, unless you are extremly unlucky, you wouldn't get there. But when you will meet this little devil? Only when everytime you partition you all choose the min/max number and make it insertion sort.

Instead, most of the time, or, on average, we have

**T(n) = 2T(n/2) + O(n) ==> Θ(nlgn)**

Also, in fact, any split of constant proportionality yields a recursion tree of depth O(lgn), where the cost at each level
is O(n). The running time is therefore O(nlgn) whenever the split has constant proportionality.

### Randomized
As we can see, it's preferable for the preformance if the pivot can devide the array into equally two parts, even if we cannot literally do that, we can avoid the worst running time by using random number as pivot.

it's basiclly the same idea as quicksort but instead we use random number in array as pivot.

![](/images/TIM_jietu_20170822164049.png)

## Sorting in Linear Time
In this chapter we do not use comparison sorts but sorting algorithm in other ways, it may seems stupid but in some particular situation it can be surprisingly fast.

### Lower Bound for Comparison Sorts
Below is a decidion tree that all the comparison sorts based on, we can see that the worst running time is related to the height of the tree, in this case lgn.

![](/images/TIM_jietu_20170822165202.png)

**Theorem**

**Any comparison sort algorithm requires Ω(nlgn) comparisons in the worst case.**

the proof is hard so I'm gonna skip it. But the main idea is that if you use comparison sorts, you cannot do better than **O(nlgn)**.

So it's safe to say that **heapsort and merge sort are asymptotically optimal comparison sorts.**
### Counting Sort
![](/images/TIM_jietu_20170823084604.png)

The main idea about counting sort is to create an array C contains k elements where k is the number of possible value in the original array A. Then the value of an element in C is actually the number of elements in A that are smaller than or equal to the index. For example the first element in C, whose index is 0, has value 2, which means there are 2 elements smaller than or equal 0, and the value of index 3 is 3, so there are 3 elements smaller than or equal to 3.

![](/images/TIM_jietu_20170823084243.png)

The function show how to implement the counting sort, first if the value of an input element is i , we increment C[i]. Then we add the current one with previous one, which make it the value of an element in C is the number of elements in A that are smaller than or equal to the index i. After we finish building the array C, we can start sorting to Array B. Using C[A[i]]as index for Array B and each time we finish, C[A[i]] minus one and the next element who have the same value will use it as index in array B.

An important property of counting sort is that it is stable: numbers with the same value appear in the output array in the same order as they do in the input array. That is, it breaks ties between two numbers by the rule that whichever number appears first in the input array appears first in the output array.

### Radix sort
Radix sort works based on counting sort, especially with his **stable** charactar. Radix sort work as below:

![](/images/TIM_jietu_20170823134648.png)

it sort the least significant digit of n numbers with d digits, and then sort the higher significant digit.

![](/images/TIM_jietu_20170823135006.png)

the implement is extremly simple, just sort every digit with counting sort.

Also, the runnning time analyse is :

> Lemma

> Given n d-digit numbers in which each digit can take on up to k possible values,RADIX-SORT correctly sorts these numbers in Θ(d(n+k)) time if the stable sort it uses takes Θ(n+k)time.

Note that no matter counting sort or radix sort, it only fast because we assume the input array, which do not have lots of possible values. if the possible value k is too large, the array C, will be too large to implement and consume huge amount of memory.

### Bucket Sort
Whereas counting sort assumes that the input consists of integers in a small range, bucket sort assumes that the input is generated by a random process that distributes elements uniformly and independently over the interval [0,1).

![](/images/TIM_jietu_20170823141753.png)

It's pretty much like hashmap where you put all the elements into several buckets evenly and sort them individually.

![](/images/TIM_jietu_20170823141958.png)

the running time of bucket sort is O(n), but I don't really understand the proof.

## Medians and Order Statistics
The i th order statistic of a set of n elements is the i th smallest element. For example, the ***minimum*** of a set of elements is the first order statistic (i = 1), and the ***maximum*** is the nth order statistic (i = n). A ***median***, informally, is the “halfway point” of the set. When n is odd, the median is unique, occurring at i = (n+1)/2. When n is even, there are two medians, occurring at i = n/2 and i = n/2 + 1. Thus, regardless of the parity of n, medians occur at i = (n+1)/2 (the lower median) and i = (n+1)/2 (the upper median). For simplicity in this text, however, we consistently use the phrase “the median” to refer to the **lower median**.

### Min & Max
![](/images/TIM_jietu_20170823143315.png)

Finding the Min or Max number is easy, just go through all the number and all is well. Also we can conclude that (n-1) comparisons are necessary to determine the minimum.

There are also some improvement we can make. If we want to find max and min in an array Simultaneously, we do not need (2n-2) comparisons, instead, 3(n/2) comparisons are sufficent.

Rather than processing each element of the input by comparing it against the current minimum and maximum, at a cost of 2 comparisons per element,we process elements in pairs. We compare pairs of elements from the input first with each other, and then we compare the smaller with the current minimum and the larger to the current maximum, at a cost of 3 comparisons for every 2 elements.

We set up initial values for the current minimum and maximum depends on whether n is odd or even. If n is odd, we set both the minimum and maximum to the value of the first element, and then we process the rest of the elements in pairs. If n is even, we perform 1 comparison on the first 2 elements to determine the initial values of the minimum and maximum, and then process the rest of the elements in pairs as in the case for odd n.

### ***i*** th number
The general selection problem appears more difficult than the simple problem of finding a minimum. Yet, surprisingly, the asymptotic running time for both problems is the same: Θ(n).

As in quicksort, we partition the input array recursively. But unlike quicksort, which recursively processes both sides of the partition, RANDOMIZED-SELECT works on only one side of the partition. This difference shows up in the analysis: whereas quicksort has an expected running time of Θ(nlgn), the expected running time of RANDOMIZED-SELECT is Θ(n), assuming that the elements are distinct.

![](/images/TIM_jietu_20170823145701.png)

Instead of sorting all the element and choose ***i*** th element, we can use PARTITION as a simpler way. As shown above, we first partition the whole array and return the index of pivot q, then we compare the index of pivot with i and decide which part(left or right) to partition.

But it still not enough since we cannot guarantee the even split and may cause worst running time. So we use an improved way to guarantee that the pivot we choose can evenly(at least not too shabby) partition the array.

![](/images/TIM_jietu_20170823154439.png)

By finding the median of groups of medians, the running time will be better.
