---
layout: post
title:  "网易游戏实习生二面总结"
date:   2018-11-30 
categories: 面经
tags: C++
excerpt: 前几天网易游戏二面，问了挺多问题，答的不是很好，还没来消息，估计凉凉了QuQ
---

* content
{:toc}

### 洗牌算法
面试官问我项目中怎么实现洗牌的，我说STL的random_shuffle，然后面试官要我自己实现一下，虽然之前看过一点点洗牌算法，一紧张说的很乱，面试官就让过了，然后问洗牌算法的目标是什么，我答每种排列出现的概率应该相同。　　

下面是洗牌算法简单的实现方法，大概就是从后向前遍历，假设当前数下标为n，随机一个0到n-1的数x，交换arr[x]和arr[n]即可。
```cpp
void Random_Shuffle(vector<int> & arr)
{
	for (int i=arr.size()-1;i>=0;--i)
	{
		swap(arr[rand()%(i+1)],arr[i]);
	}
}
```

### 函数默认参数
面试官给了一个问题，要我输出什么并解释。
```cpp
class A
{
public:
	virtual void foo(int a=0)
	{
		cout<<a;
	}
};
class B : public A
{
public:
	virtual void foo(int a=1)
	{
		cout<<a;
	}
};

int main() 
{
	A * t = new B;
	t->foo();
	return 0;
}
```
我解释了一堆虚函数指针什么的，然后又说了函数怎么压栈，得出答案是走下面B的函数，输出1，面试官要我回去试试，果然错了，虽然走的是下面的函数，但是输出的却是0，很神奇。　　

父类指针调用虚成员函数是多态在代码层面的体现，这在运行过程中，父类指针将动态绑定（关联）到具体的某个虚函数。指的是在`运行期`根据父类指针的动态类型的虚函数表来绑定到具体的函数。但是默认参数是静态绑定的。这也就意味着对于一个虚函数，你可能会调用它在派生类中的定义，而默认参数值则采用基类中的值。　　

这个例子在 *effective C++* 第37条有说到，*第37条：避免对函数中继承得来的默认参数值进行重定义* ，果然我还是太naive了，对于C++，我仍一无所知。为什么不把默认参数也和虚函数一样采用动态绑定呢，因为那样效率低，编译器必须提供一整套方案，为运行时的虚函数参数确定恰当的默认值。

### 客户端发牌问题

斗地主游戏如果不通过第三方服务器发牌，仅由3个客户端自己处理，怎么保证游戏的公平性？即发牌的时候不能知道对方的牌。　　

答了一大堆也没答道点子上，最后面试官给了点提示，事先设计一个加密函数，一个人打乱牌由3个客户端对牌都加密一次，或者商量一个加密方案，然后3个客户端各取完自己的牌，最后3个客户端同时解密就行。我没反应过来可以加密，总想着怎么去避免看别人的牌去了。。挺笨的QuQ。估计是凉凉了啊。

### 哈希冲突的两种解决方案分别怎么删除元素

拉链法好办，直接把后面的接上去就行了，大概？开放地址法想了半天，想了几个方法，比如找到当前哈希值最后一个元素跟要删除的元素交换，然后再删除之类的，面试官说没你想的那么简单啊，面试结束后想到可以加一个变量标记下被删除的元素，如果标记为被删除了，下次插入的时候就把这个元素给替换掉，感觉有更好的方法。

## 总结
还有很多问题没答全的，都是只答到了表面，深一点就不会了，总之是去不了网易了。准备了很多 操作系统，算法题，linux，数据库，都没考，面试官是一个偏向底层的，面试结束的时候我提了一下以前用过HLSL语言，就问了我怎么判断三角形正反朝上和光线跟踪的问题，问我了不了解管线，当然我都不会，我说我做的游戏是2d的，早就忘了。  

再多补补基础吧，找个实习不容易啊。**effective c++** 和 **深度探索C++对象模型** 务必好好看。


_ _ _
2019.1.4号更新

最终拿到网易游戏的offer啦，还是很开心了，二面完差不多一个月后才通知我HR面，可能是考虑到我这边要放完寒假才能实习吧。  
从11月初开始投简历，一共面试了3家公司的实习生，面试都通过了，一直想抽时间把面试题总结下，但是考试实在太多。。电气太苦逼了，真不应该选这个专业。  

以下是补充的网易游戏二面面试题，之前只记录了几个主要的问题。

1. 说说平常玩什么游戏，我：忙于学习（后来说了几个以前玩的单机）
2. 说说游戏设计的大致流程
3. 指针和引用的区别，怎么获得一个引用的大小
4. 详细说明extern "C"的用法，并解释
5. TCP三次握手的每次传输的代号
6. hash表怎么实现的
7. 虚函数怎么实现的

大概就是这么些问题了，面试过程还是很在状态的，基本上每个问题都会深入问一下，二面是视频面试，面试了差不多一个小时，最后面试官建议我多看看代码的底层实现，可以学学汇编还有计算机图形学，网易游戏的面试是这几家公司体验最好的，最后还是决定去网易游戏了，希望能向猪场大佬们多学习学习。明天考电力电子技术，希望能过啊 \_(:з」∠)_ 