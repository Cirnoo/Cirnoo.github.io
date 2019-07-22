---
layout: post
title:  "顶点缓存与索引缓存"
date:   2019-07-22 
categories: 图形学
tags: 图形学
excerpt: 利用顶点缓存和索引缓存画矩形
---

* content
{:toc}
  

### 索引缓存的好处  

1. 减少了顶点缓存的顶点个数(不需重复顶点)。
2. 灵活修改图形形状(通过修改索引序列而不是顶点序列)。
3. 提高渲染效率，runtime将多次用到的顶点数据放置在相近的地方Cache中(local reference)。

### 三角形的顶点缓存  

画一个三角形需要3个顶点，先建立一个大小为3个顶点的缓存大小  

```cpp
Device->CreateVertexBuffer(
		3 * sizeof(Vertex), // size in bytes
		D3DUSAGE_WRITEONLY, // flags
		Vertex::FVF,        // vertex format
		D3DPOOL_MANAGED,    // managed memory pool
		&Triangle,          // return create vertex buffer
		0);                 // not used - set to 0

	//
	// Fill the buffers with the triangle data.
	//

	Vertex* vertices;
	Triangle->Lock(0, 0, (void**)&vertices, 0);

	vertices[0] = Vertex(-1.0f, 0.0f, 2.0f);
	vertices[1] = Vertex( 0.0f, 1.0f, 2.0f);
	vertices[2] = Vertex( 1.0f, 0.0f, 2.0f);

	Triangle->Unlock();

```  

在display函数中设定图元等信息

```cpp
bool Display(float timeDelta)
{
	if( Device )
	{
		Device->Clear(0, 0, D3DCLEAR_TARGET | D3DCLEAR_ZBUFFER, 0xffffffff, 1.0f, 0);
		Device->BeginScene();

		Device->SetStreamSource(0, Triangle, 0, sizeof(Vertex));
		Device->SetFVF(Vertex::FVF);

		// Draw one triangle.
		Device->DrawPrimitive(D3DPT_TRIANGLELIST, 0, 1);

		Device->EndScene();
		Device->Present(0, 0, 0, 0);
	}
	return true;
}
```  
<img src = 'https://i.loli.net/2019/07/22/5d35626b7af0710270.png'  width=400></img>  

### 四边形的顶点缓存  

如果要画一个四边形，我们可以建立6个顶点，
```cpp 
Vertex* vertices;
	Triangle->Lock(0, 0, (void**)&vertices, 0);

	vertices[0] = Vertex(0.0f, 0.0f, 2.0f);
	vertices[1] = Vertex(0.0f, 1.0f, 2.0f);
	vertices[2] = Vertex(1.0f, 0.0f, 2.0f);
	vertices[3] = Vertex(1.0f, 0.0f, 2.0f);
	vertices[4] = Vertex(0.0f, 1.0f, 2.0f);
	vertices[5] = Vertex(1.0f, 1.0f, 2.0f);
	Triangle->Unlock();
```

<img src = 'https://i.loli.net/2019/07/22/5d35643b0c53477999.png'  width=400></img> 

但是这样有2个重复的顶点，浪费了2个的顶点的空间，所以我们可以用索引缓存。


### 四边形的索引缓存  

```cpp
Vertex rect[6] = {v0, v1, v2, //triangle 0
v0,v2,v3}; //triangle 1

``` 

![QQ截图20190722152902.png](https://i.loli.net/2019/07/22/5d3565c7b3ca336364.png)  

<img src = '(https://i.loli.net/2019/07/22/5d3565c7b3ca336364.png'  width=400></img> 

这样只需要4个顶点就能实现同样的效果了。注意顶点是逆时针排序的。