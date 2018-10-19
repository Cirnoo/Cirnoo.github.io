---
layout: post
title:  "MFC中的 WM_MOUSEHOVER 与 WM_MOUSELEAVE 消息"
date:   2018-10-19 
categories: MFC
tags: MFC
excerpt: 本文将介绍在MFC中如何响应 WM_MOUSEHOVER 和 WM_MOUSELEAVE 消息
---

* content
{:toc}

在MFC中当鼠标停留在程序窗口一定时间后，会产生一个`WM_MOUSEHOVER`消息；当鼠标离开程序窗口后，会产生一个`WM_MOUSELEAVE`消息。MFC为我们提供了`OnMouseHover()`和`OnMouseLeave()`两个函数来分别响应鼠标停留一段时间和鼠标离开对话框/控件的消息。  
但是默认情况下控件是不发送这两个消息的，所以直接在类属性中添加这两个处理函数是无法正常被调用的。需要通过`_TrackMouseEvent `才可以将这两个消息发送出去。

## 使用方法
1. 在需要被操作的控件中声明一个成员变量`m_is_track`，并初始化为false.当鼠标进入控件时置为true，离开控件时置为false。
2. 在控件的添加OnMouseMove()函数，并调用`_TrackMouseEvent `函数。
	```cpp
    void CBaseControl::OnMouseMove(UINT nFlags, CPoint point)
{
	if(!m_is_track) 
	{ 
		  TRACKMOUSEEVENT csTME;
          csTME.cbSize = sizeof (csTME);
          csTME.dwFlags = TME_LEAVE|TME_HOVER; //需要发送的消息，可选
          csTME.hwndTrack = GetSafeHwnd(); ;// 指定要 追踪 的窗口 
          csTME.dwHoverTime = 100;  // HOVER消息触发时间 单位ms
          ::_TrackMouseEvent (&csTME); // 开启 Windows 的 WM_MOUSELEAVE ， WM_MOUSEHOVER 事件支持
          m_is_track=ture; //避免重复执行
	}
	CWnd::OnMouseMove(nFlags, point);
}
    ```

3. 在`OnMouseLeave()`函数中将`m_is_track`置为false。

## 题外话

通过以上操作就可以让控件响应鼠标停留和离开消息了，事实上我们可以将 if 函数中的代码写进虚函数OnTrack()中，这样子类就可以通过重写OnTrack()函数来实现响应鼠标的进入事件了，不要忘了调用父类的OnTrack()函数。

