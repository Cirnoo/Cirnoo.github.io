---
layout: post
title:  "二分查找的两种方法比较"
date:   2019-04-06 
categories: C++
tags: 算法
excerpt: 在unity中使用C#动态编译
---

* content
{:toc}

### 三分支的二分查找  

假设在有序数组区间S[lo,hi]中查找目标元素e，以任一元素S[mi] = x为界，都可以将区间分为三部分，且根据此时的有序性必有  
`S[lo,mi)<=S[mi]<=S[mi+1,hi)`  
于是，只需要将目标元素e与X做一比较，
1. 若e<x,则目标元素如果存在，必然属于左侧区间S[lo,mi],故可以深入其中继续查找
2. 若e>x,则意味着必然属于右侧区间S[mi+1,hi),同样可以重复进行查找
3. 若e=x，则命中目标，查找结束

也就是说每经过之多两次比较操作，就可以将问题缩小为规模更小的问题。代码如下

```cpp
template<typename T>
static int BinSearch(T* A, T const& e, int lo, int hi)
{
    while(lo<hi)
    {
        int mi = hi - (hi-lo)/2;
        if(e<A[mi])
            hi = mi;
        else if(A[mi]<e)
            lo = mi+1;
        else
            return mi;
    }
    return -1;
}
```

### 二分支的二分查找  

三分支最多每次需要判断两次才能判断目标元素所在的区间。以下是二分支版本：

```cpp
template<typename T>
static int BinSearch(T* A, T const& e, int lo, int hi)
{
    while(1<hi-lo)
    {
        int mi = hi - (hi-lo)/2;
        if (e < A[mi])
             hi = mi;
        else
             lo = mi;
    }
    return A[lo] == e ? lo : -1;
}
```

首先每步迭代只需要判断是否e<A[mi],即可更新有效查找区域。另外，只有等到区间的宽度不足2个单元时迭代才会终止，最后再通过一次对比判断查找是否成功。  

尽管第二个版中中后端子向量需要加入A[mi]，但是得益于mi总是位于中央位置，整个算法的O(lgn)时间复杂度不收影响。在这个版本中，只有在向量有效区间宽度缩小至一个单元时算法才会终止，而不会像第一个版本那样，一旦命中就会直接返回。因此，最好情况下效率有所倒退，最坏情况下的效率相应提高。  

因此无论是成功查找或者是失败查找，二分支版本的各个分支的查找长度更接近，所以整体性能更稳定。  
