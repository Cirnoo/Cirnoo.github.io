---
layout: post
title:  "dynamic_cast实现原理"
date:   2019-08-12 
categories: C++
tags: C++
excerpt: dynamic_cast的一种底层实现方式
---

* content
{:toc}

### dynamic_cast的使用原则

除子类指针使用dynamic_cast转换成父类这种情况以外，dynamic_cast要求操作数必须是多态类型。如：

```cpp
class A
{
	
};
class B :public A
{
	
};

int main()
{
	A * b = new B;
	B * c = dynamic_cast<B*>(b);
	return 0;
}
```

这样使用会报错，因为dynamic_cast是根据多态性来判断类型的。  

### 三种情况

1. 如果source和dest没有父子关系，那么结果是NULL，即使两个类定义成一模一样。
2. 从子类到父类，这是必然是成功的。
3. 从父类到子类，这也是dynamic_cast最常用的情况，这依赖于父类指针是否真实的指向了对应的子类类型，标示这是不是一个真实的子类对象。

### 原理

![对象模型](https://i.loli.net/2019/08/12/BF1asnkMeNmrVEJ.png)

根据C++对象模型，对象的type_info被存在了虚表的首部，所以要使用dynamic_cast，对象必须有多态，然后运行时期比对要转换的类型是否和type_info中记录的类型相同即可。

