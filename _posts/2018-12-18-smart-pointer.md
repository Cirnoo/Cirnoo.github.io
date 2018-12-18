---
layout: post
title:  "智能指针简单实现"
date:   2018-12-18 
categories: C++
tags: C++
excerpt: 智能指针简介与实现
---

* content
{:toc}

### 智能指针
智能指针实际上就是一个像指针的模板对象，在对象中保存了一个普通指针，通常还有一个引用计数，智能指针类将一个计数器与类指向的对象相关联，引用计数跟踪该类有多少个对象的指针指向同一对象。

### 原理
下面是一种简单的实现方式
>每次创建类的新对象时，初始化指针并将引用计数置为1；当对象作为另一对象的副本而创建时，拷贝构造函数拷贝指针并增加与之相应的引用计数；对>一个对象进行赋值时，赋值操作符减少左操作数所指对象的引用计数（如果引用计数为减至0，则删除对象），并增加右操作数所指对象的引用计数；调>用析构函数时，析构函数减少引用计数（如果引用计数减至0，则删除基础对象）。

由以上的思路很容易可以写出代码。

```cpp
class MyPointer
{
private:
	T * pval;
	int * cnt;
public:
	explicit MyPointer(T * ptr=nullptr):pval(ptr)
	{
		cnt=new int(ptr==nullptr?0:1);
	}
	~MyPointer()
	{
		if (--(*cnt)<=0)
		{
			delete cnt;
			delete pval;
		}
	}
	MyPointer(const MyPointer & p)
	{
		if (this!=&p)
		{
			this->pval=p.pval;
			this->cnt=p.cnt;
			++(*this->cnt);
		}
	}

	MyPointer & operator=(const MyPointer & p)
	{
		if (pval==p.pval)
		{
			return *this;
		}
		if (this->pval)
		{
			if(--(*this->cnt)<=0)
			{
				delete this->cnt;
				delete this->pval;
			}
		}
		this->cnt=p.cnt;
		this->pval=p.pval;
		++(*this->cnt);
		return *this;
	}
	T & operator *()
	{
		assert(this->pval!=nullptr);
		return *pval;
	}
	T * operator ->()
	{
		assert(this->pval!=nullptr);
		return pval;
	}
	int Count()
	{
		return *cnt;
	}

};
```

### 解析
#### private变量
```cpp
private:
	T * pval;	//实际指向对象的指针
	int * cnt;	//必须使用指针，因为可以有多个智能指针指向同一个对象
```
注意引用计数必须使用指针。

#### 构造函数
构造函数中根据传入的指针判断是否为空来决定引用计数的值

#### 析构函数
当对象离开作用域的时候会自动析构，这也是使用智能指针的方便之处。当只能指针被析构是减少引用计数，计数为0的时候析构实际所指向的对象即可。

#### 拷贝构造函数
拷贝构造函数在使用一个对象对另一个对象`初始化`的时候或作为函数的值传递的时候被调用。

#### 赋值构造函数
跟拷贝构造函数类似，只是当指针本身已经有指向的对象的时候要减去一个引用计数。

#### 操作符重载
让对象拥有和指针相似的用法。