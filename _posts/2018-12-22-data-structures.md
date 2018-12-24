---
layout: post
title:  "数据结构设计"
date:   2018-12-22 
categories: 面试
tags: C++
excerpt:  程序=数据结构+算法
---

* content
{:toc}

##### 循环队列
循环队列用一个头指针，一个尾指针和一个链表即可实现。

##### 优先队列
堆排序的思想，进队出队后需要对堆进行调整，push和pop的时间复杂度都是O(logn)