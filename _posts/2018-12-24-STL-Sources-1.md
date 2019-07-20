---
layout: post
title:  "STL源码剖析 笔记 allocator"
date:   2018-12-24 
categories: 笔记
tags: C++ STL
excerpt: 源码面前，了无秘密
---

* content
{:toc}
　　
很早以前就看到图书馆的《STL源码剖析》，粗略看了几眼后觉得比较深奥，也觉得没有时间来读，临近期末，终于能抽出时间好好品味侯捷大师的大作了，读完前面几章就觉得应该早点读的，真的是可以说是学习STL必备的一本书，之前在使用STL中遇到的一些疑惑也解开了。STL是一座宝库，通过这本书，我对泛型思维有了更深的认识。学习STL对理解数据结构和算法底层原理实现方法帮助很大。
> 不要重新造轮子，要站在巨人的肩膀上。

### allocator
#### 定位new
```cpp
//placement new
new(p) T1(value);
```
详见 &rarr; [定位new](https://cirnoo.github.io/2018/12/16/cpp_basis/#11-%E5%AE%9A%E4%BD%8Dnew)

allocator 负责的是给STL容器分配空间，这个空间甚至可以是硬盘空间[p43],通常情况下我们不需要直接接触allocator，也没必要去使用它，当然要使用也是可以的。一般而言我们习惯的C++内存配置和释放操作是这样的：
```cpp
class Foo{...};
Foo * pf = new Foo;
delete pf;
```
new 包含了 ::operator new 配置内存和Foo::Foo() 构造对象的过程，delete包含Foo::~Foo() 将对象析构，调用::operator delete 来释放内存。  
而STL中的allocator将这些操作分开来了，内存配置由alloc::allocate()负责，释放操作由alloc::deallocate()负责，对象构造由::construct()负责，对象析构由::destory()负责。  

具体实现方式各个版本的STL都有所区别，但是大多时候是直接调用new了，根据侯大师视频里说的，在2.9版的GNU中使用了一个很棒的内存池，里面有一个free_list[16]的数组可以更有效地分配内存，但是在4.9版中这个设计被取消了，原因未知。  

对于allocator初学者而言简单了解即可，如果需要自己去设计一个STL的话就需要去深入思考了。
