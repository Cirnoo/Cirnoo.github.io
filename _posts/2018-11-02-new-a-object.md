---
layout: post
title:  "如何new一个对象"
date:   2018-11-02 
categories: 学习笔记
tags: C++
excerpt: MyObject * object = new MyObject(); 的过程中发生了什么？
---

* content
{:toc}

### 1. 分配内存空间

`void * mem = operate new (sizeof(MyObject));`  
此过程为MyObject分配一个对应大小的空间，`operate new()`是一个可以重载的操作符函数，在没有重载的情况下内部默认调用malloc(n)分配一个大小为n的空间。

### 2. 指针类型转换
`MyObject* pc = static_cast<MyObject *>(mem);`  
第一步得到的指针是void * 型，还需要转化成对应对象类型的指针才能被使用。

### 3. 调用构造函数 
`pc–>MyObject::MyObject();`  
对于有构造函数的类，不论有没有括号，都用构造函数进行初始化；如果没有构造函数，则不加括号的new只分配内存空间，不进行内存的初始化，而加了括号的new会在分配内存的同时初始化为0。括号中有参数则调用对应参数的构造函数。


这样就得到了一个我们需要的对象。
