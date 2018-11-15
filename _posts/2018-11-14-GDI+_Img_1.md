---
layout: post
title:  "GDI+图片处理（一）"
date:   2018-11-14 
categories: GDI+
tags: GDI+ C++ MFC
excerpt: 使用GDI+从资源文件中加载图片素材
---

* content
{:toc}

# GDI+与游戏素材系列
这个系列是MFC中GDI+的学习笔记,主要是关于游戏素材的处理。

使用MFC制作游戏是一件痛苦的事，虽然MFC中内置了GDI+库，能比GDI操作简单不少，但是由于GDI+绘图效率低，功能也少，使用GDI+的人并不多。
我在这两个多月学习使用的GDI和GDI+过程中，踩了不少坑，查阅了网上很多资料和文档，整理了几个图像处理函数，总之，MFC和GDI+都不怎么好用。


## GDI+加载素材的方法
GDI+的Bitmap提供了几个加载素材的方法，从文件路径中加载，从流中加载，唯独没有提供从资源文件中加载的方法。  

### 为什么要从Resource中加载素材  

在最初我使用的是从文件路径中（FromFile函数）加载素材的方法，结果在release的时候发现所有的图片都无法显示了，后来才知道是由于release编译的文件路径变了，导致相对路径没能成功加载到素材，对于未找到的素材，FromFile函数并不会报错而是会变成全黑色，所以导致所有图片都显示不出来了，由于GDI+自带函数不支持从Resource中加载，这对小型程序的开发带来了麻烦。我尝试了下使用GDI的方法加载素材后再转换成GDI+的格式，发现图片变成了灰度图，原因未知。

以下是从网上找到的从Resource加载的方法，经测试能正常加载。

```cpp
using namespace Gdiplus;
Image * LoadPNGFormResource(int nID)
{
	HINSTANCE hInst = GetModuleHandle(NULL);  
	HRSRC hRsrc = ::FindResource (hInst,MAKEINTRESOURCE(nID),_T("png")); // type  
	if (!hRsrc)  
		return FALSE;  
	// load resource into memory  
	DWORD len = SizeofResource(hInst, hRsrc);  
	BYTE* lpRsrc = (BYTE*)LoadResource(hInst, hRsrc);  
	if (!lpRsrc)  
		return FALSE;  
	// Allocate global memory on which to create stream  
	HGLOBAL m_hMem = GlobalAlloc(GMEM_FIXED, len);  
	BYTE* pmem = (BYTE*)GlobalLock(m_hMem);  
	memcpy(pmem,lpRsrc,len);  
	IStream* pstm;  
	CreateStreamOnHGlobal(m_hMem,FALSE,&pstm);  
	// load from stream  
	auto pImg=Gdiplus::Image::FromStream(pstm);  
	// free/release stuff  
	GlobalUnlock(m_hMem);  
	pstm->Release();  
	FreeResource(lpRsrc); 
	return pImg;
}

```
**珍爱生命，远离MFC**
