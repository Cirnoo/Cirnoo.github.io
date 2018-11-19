---
layout: post
title:  "GDI+图片处理（二）"
date:   2018-11-14 
categories: GDI+
tags: GDI+ C++ MFC
excerpt: 使用GDI+从素材中加载素材（图像裁剪）
---

* content
{:toc}

在使用游戏素材的时候我们往往会将一些小的游戏素材整理到一个图片里面，然后只需要将这样一张大图加载进游戏中，使用的时候就可以按需要截取了。  
这样的好处是：
1. 整张加载一般会比一张张加载更快。
2. 减少文件数量，不至于太乱。
3. 加载的时候更方便，也节省了一个个素材命名的时间。

但是GDI+中内置函数并没有提供这样的方法，所以只能自己实现一个啦。原理比较简单，就是将所需要的那部分画到一张新的图上。

```cpp
using namespace Gdiplus;
typedef  Image* pImage;
pImage CutImage(pImage imgSrc,int x,int y, int Width, int Height)
{
	Bitmap *bmPhoto = new Bitmap(Width, Height);
	bmPhoto->SetResolution(imgSrc->GetHorizontalResolution(), imgSrc->GetVerticalResolution());
	Graphics grPhoto(bmPhoto);
	grPhoto.Clear((ARGB)Color::Transparent);
	grPhoto.SetInterpolationMode(InterpolationModeHighQualityBicubic);
	grPhoto.DrawImage(imgSrc, 0, 0, x,y, Width,Height, UnitPixel);//关键代码，实现裁剪
	return bmPhoto;
}
```

