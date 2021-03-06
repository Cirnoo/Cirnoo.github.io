---
layout: post
title:  "C#装箱与拆箱"
date:   2019-07-31 
categories: C#
tags: C#
excerpt: C#装箱与拆箱
---

装箱：将值类型（如 int ，或自定义的值类型等）转换成 object 或者接口类型的一个过程。当 CLR 对值类型进行装箱时，会将该值包装为 System.Object 类型，再将包装后的对象存储在`堆`上。 

拆箱就是从对象中提取对应的值类型的一个过程。

装箱是隐式的；拆箱必定是显式的。  

与简单的赋值操作相比，装箱和拆箱都需要进行大量的数据计算。对值类型进行装箱时，CLR 必须重新分配一个新的对象。拆箱所需的强制转换也需要进行大量的计算  

```cs
static void Main(string[] args)
        {
            var i = 123;    //System.Int32

            //对 i 装箱（隐式）
            object obj = i;

            //对 obj 进行拆箱（显式）
            i = (int)obj;
        }
```

注意装箱会生成新的对象，同时拆箱时是会引发`转换无效`的异常。要记住，拆箱时强转的值类型，应以装箱时的值类型一致。
