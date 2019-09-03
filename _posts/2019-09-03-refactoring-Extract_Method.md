---
layout: post
title:  "重构-函数"
date:   2019-09-03 
categories: 笔记
tags: C++ 笔记
excerpt: 重新组织你的函数
---

### Extract Method

* 创造一个新函数,根据这个函数的意图来对它命名

```cpp
void PrintOwing(double amount)
{
	PrintBanner();
	
	//print details
	cout << "name:" << _name;
	cout << "amount:" << amount;
}
```

```cpp
void PrintDetails(double amount)
{
	cout << "name:" << _name;
	cout << "amount:" << amount;
}

void PrintOwing(double amount)
{
	PrintBanner();
	PrintDetails(amount);
}
```

一个过长的函数或者一段需要注释才能让人理解用途的代码,我应该将这段代码放进一个独立函数中.  

如果每个函数的粒度都小，那么函数被复用的机会就更大，其次,这会使高层函数读起来就像一系列注释，再次,如果函数都是细粒度,那么函数的覆写也会更容易些。  

