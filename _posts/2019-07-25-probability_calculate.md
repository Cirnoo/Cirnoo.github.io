---
layout: post
title:  "抽奖概率算法"
date:   2019-07-25 
categories: 算法
tags: 算法
excerpt: 游戏中常用到的抽奖算法
---

* content
{:toc}

### 背景  

在游戏中常常会遇到抽奖或者有爆装备的情况，每个物品有对应出现的概率，需要计算出获得的物品，比如，装备A掉下的可能为0.3，B为0.4，那么你的算法的结果中，重复足够多的次数，A和B掉下的比例必须接近3:4。  

### 一般的算法  

首先想到构造一个足够大的数组，然后把概率都等比例扩大成整数，把数组分割成不同大小的区间，这样可通过以判断随机数落在哪个区间从而得到获得的物品。  

但是这样的缺点是，当物品很多，而且有物品爆出的概率很小的时候，就会要申请很大的数组空间，显然是不合适的。  

### 计算前缀和的方法

假如有以下几种物品：存放在数组Nums中

序号 | 概率
:-:|:-:|
A|0.1|
B|0.2|
C|0.3|
D|0.4|

可以构建前缀和数组Arr

$$ Arr[k] = Nums[0]+Nums[1]+Nums[2]+...+Nums[k] $$

然后利用产生的随机数乘Arr.back(),既Nums中物品所有概率和（如果不为1也没关系），得到的数一个数Num，在Arr中大于Num的最小数的Index就是所求的物品Index了。  

此例子中Arr为 

name | 0 | 1 | 2 | 3
:-: | :-: | :-: | :-: | :-:
Arr | 0.1 | 0.3 | 0.6 | 1 |

如果Num=0.55,那么得到C物品。  

```cpp  
#include <iostream>
#include <ctime>
#include <algorithm>
using namespace std;

int main()
{
	const int NUM_CNT = 4;
	const int TEST_CNT = 100000;
	double nums[NUM_CNT] = { 0.1, 0.2, 0.3, 0.4 };
	double arr[NUM_CNT] = { 0.1 };
	for (int i = 1; i < NUM_CNT; ++i)
	{
		arr[i] = arr[i - 1] + nums[i];
	}
	int ans[NUM_CNT] = { 0 };
	srand(time(0));
	for (int i = 0; i < TEST_CNT; ++i)
	{
		double num = rand() % 1000 / (double)1001;
		int index = upper_bound(arr, arr + NUM_CNT, num) - arr;
		++ans[index];
	}
	for (int i = 0; i < NUM_CNT; ++i)
	{
		cout << i << "----" << ans[i] / (double)TEST_CNT << endl;
	}
	return 0;
}
```  

运行结果：
>0----0.10116  
>1----0.20054  
>2----0.3015  
>3----0.3968  
