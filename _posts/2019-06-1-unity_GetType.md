---
layout: post
title:  "C#动态编译"
date:   2019-05-25 
categories: 实习
tags: unity C#
excerpt: 在Unity中使用GetType()
---

* content
{:toc}

### 背景
在unity中有时候会需要获取一些私有的类或属性，这时候需要用反射，反射直接用Type.GetType()总是会返回null  
这是因为没有指定程序集  
一个简单的方法：  
使用typeof(xxx).Assembly 获取Assembly，在用assembly.GetType()即可。  

例子
```cs
var assembly = typeof(EditorWindow).Assembly;
var type = assembly.GetType("UnityEditor.AvatarSetupTool");
var fun = type.GetMethod("ClearAll",BindingFlags.Static);
fun.Invoke(null, new object[] { null, null });
```