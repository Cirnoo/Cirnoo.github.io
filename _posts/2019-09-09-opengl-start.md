---
layout: post
title:  "初识OpenGL"
date:   2019-09-09 
categories: 图形学
tags: C++ OpenGL
excerpt: OpenGL中的几个函数
---

* content
{:toc}

### glutDisplayFunc

glutDisplayFunc()的使用方式是glutDisplayFunc(&display);

void display(void)函数为绘图函数。

glutDisplayFunc()是glut中很重要的函数，使用非常简单。

重点是对这个函数的理解：

glutDisplayFunc(&display)在程序运行时是自动调用的，即程序会自动调用display函数重绘窗口

调用的时机有这么几个：

1. 窗口内容绘制

2. 窗口大小改变

3. 窗口重绘

### glutReshapeFunc

目的：当窗口尺寸改变时，图形比例不发生变化  

思路：窗口宽高比改变时，通过改变窗口显示区域大小，并利用投影矩阵改变观测物体大小使之适应变化。

### glutKeyboardFunc

处理普通按键消息的函数的名称。如果传递NULL，则表示GLUT忽略普通按键消息。

### glutTimerFunc

glutTimerFunc(unsigned int millis, void (*func)(int value), int value);  

参数对应关系为：glutTimerFunc(毫秒数, 回调函数指针, 区别值)  

### glutMainLoop

glutMainLoop进入GLUT事件处理循环，让所有的与“事件”有关的函数调用无限循环。  

### 示例

```cpp
    glutInit(&argc, argv);
	glutInitDisplayMode(GLUT_RGBA | GLUT_DOUBLE | GLUT_DEPTH);
	glutInitWindowPosition(100, 100);
	glutInitWindowSize(800, 600);
	glutDisplayFunc(&myDisplay);
	glutReshapeFunc(&myReshape);
	glutKeyboardFunc(&myKeyboardFunc);
	glutTimerFunc(25, myTimerFunc, 0);
	glutMainLoop();
```

