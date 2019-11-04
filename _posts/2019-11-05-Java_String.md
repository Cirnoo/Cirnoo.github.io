---
layout: post
title:  "String、StringBuffer与StringBuilder比较"
date:   2019-11-05 
categories: Java
tags: Java
excerpt: 那么他们到底有什么优缺点，到底什么时候该用谁呢？
---

### 执行速度

StringBuilder > StringBuffer > String  

String最慢的原因：String为字符串常量，而StringBuilder和StringBuffer均为字符串变量，即String对象一旦创建之后该对象是不可更改的，但后两者的对象是变量，是可以更改的。  

### StringBuilder与 StringBuffer比较

StringBuilder：线程非安全的  

StringBuffer：线程安全的

当我们在字符串缓冲去被多个线程使用是，JVM不能保证StringBuilder的操作是安全的，虽然他的速度最快，但是可以保证StringBuffer是可以正确操作的。  

当然大多数情况下就是我们是在单线程下进行的操作，所以大多数情况下是建议用StringBuilder而不用StringBuffer的，就是速度的原因。

### 总结

1. 如果要操作少量的数据用String
2. 单线程操作字符串缓冲区 下操作大量数据用StringBuilder
3. 多线程操作字符串缓冲区 下操作大量数据用StringBuffer