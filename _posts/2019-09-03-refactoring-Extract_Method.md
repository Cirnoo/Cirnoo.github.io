---
layout: post
title:  "重构-函数"
date:   2019-09-03 
categories: 笔记
tags: C++ 笔记
excerpt: 重新组织你的函数
---

* content
{:toc}

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

### Inline Method

经常以简短的函数表现动作意图，这样会使代码更清晰易读。但有时候你会遇到某些函数，其内部代码和函数名称同样清晰易读。也可能你重构了该函数，使得其内容和其名称变得同样清晰。果真如此，你就应该去掉这个函数，直接使用其中的代码。间接性可能带来帮助，但非必要的间接性总是让人不舒服。

```cpp
bool MoreThanFiveLateDliveries()
{
	return _numberOfLaterDeliveries > 5;
}
int GetReating()
{
	return (MoreThanFiveLateDliveries()) ? 2 : 1;
}

```

```cpp
int GetReating()
{
	return (_numberOfLaterDeliveries > 5) ? 2 : 1;
}
```

