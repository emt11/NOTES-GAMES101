## 1 视口变换-将规范立方体转换为屏幕空间 Canonical Cube to Screen
### 1.1 什么是屏幕 What is a screen
+ 在图形学中，抽象的认为屏幕是一个二维数组，数组中每一个元素是一个像素 An array of pixels
+ 这个数组的大小：分辨率 Size of the array: resolution
+ 屏幕是一种典型的光栅成像设备 A typical kind of raster display

> Raster == screen in German
> Rasterize == drawing onto the screen


### 1.2 像素 Pixel (FYI, short for "picture element")
+ 暂时理解为：一个有统一颜色的小方块，是屏幕上显示的最小单位
  For now: A pixel is a little square with uniform color
+ 颜色用 红绿蓝 三种颜色表示 (red, green, blue)
  Color is a mixture of (red, green, blue)


### 1.3 屏幕空间的定义 Defining the screen space
+ 像素索引是 $(x, y)$ 形式，并且 $x, y$ 都是整数
+ 像素索引范围从 $(0, 0)$ 到 $(width - 1, height - 1)$
+ 像素 $(x, y)$ 的中心是 $(x + 0.5, y + 0.5)$
+ 屏幕覆盖区从 $(0, 0)$ 到 $(width, height)$

![[Pasted image 20260323133719.png|475]]


### 1.4 视口变换 Canonical Cube to Screen
+ 无关 z 
+ 变换：$xy\space平面的\space[-1, -1]^2 \rightarrow [0, width] \times [0, height]$
+ 变换矩阵：
$$
M_{viewport} = 
\begin{pmatrix}
\frac{width}{2} & 0 & 0 & \frac{width}{2} \\ 
0 & \frac{height}{2} & 0 & \frac{height}{2} \\ 
0 & 0 & 1 & 0 \\ 
0 & 0 & 0 & 1
\end{pmatrix}
$$

![[Pasted image 20260323132912.png|675]]

## 2 光栅化三角形到像素 Rasterizing Triangles into Pixels
### 2.1 绘制机器 Drawing Machines
+ 绘图机 CNC Sharpie Drawing Machine![[Pasted image 20260323134516.png]]
+ 激光切割机 Laser Cutters ![[Pasted image 20260323134740.png]]


### 2.2 不同的光栅展示设备 Different Raster Displays
+ 示波器 Oscilloscope![[Pasted image 20260323134931.png]]
+ 阴极射线管 Cathode Ray Tube
	+ 早期CRT显示器 Raster Display CRT
	  右图：隔行扫描 (Interlaced) 减少了带宽，现在这种思想也被用于一些视频压缩算法
	    ![[Pasted image 20260323135500.png]]

> 帧缓存器（独立显存 GPU 内存中）：屏幕所显示画面的存储区域，用来存储每个像素的颜色值
> ![[Pasted image 20260323135736.png]]

**平板显示设备 Flat Panel Displays**
+ 液晶显示器 LCD (Liquid Crystal Display)
  通过液晶扭曲偏振来阻挡或透射光
  ![[Pasted image 20260323140552.png|425]]
   
+ 发光二极管 LED (Light emitting diode) Array Display
  ![[Pasted image 20260323140919.png|475]]

+ 电子墨水屏 Electrophoretic (Electronic Ink) Display
  通过控制黑色和白色颗粒上下变化来组成画面，缺点是刷新率低
  ![[Pasted image 20260323141035.png|600]]


### 2.3 光栅化：在光栅展示器上绘制 Rasterization: Drawing to Raster Displays
+ 多边形网格![[Pasted image 20260323134302.png]]
+ 三角形网格![[Pasted image 20260323151249.png]]![[Pasted image 20260323151257.png]]

#### 2.3.1 三角形-基本的形状元素 Triangles - Fundamental Shape Primitives
+ 为什么用三角形？ Most basic polygon
	+ 最基础的多边形，可以拆分其他多边形为三角形 Break up other polygons
+ 特性
	+ 一个三角形一定能表示一个平面，而其他多边形不行 Guaranteed to be planar
	+ 清晰的内部定义 Well-defined interior
	+ 有在三角形顶点处插值的明确方法（重心插值）

#### 2.3.2 采样 Sampling
在光栅化（Rasterization）过程中，如何确定哪些像素值能够近似表示一个三角形？
![[Pasted image 20260323152604.png]]

**采样 Sampling**

+ 在一个函数上求不同位置处对应的值分别是多少，这个过程就是采样
  Evaluating a function at a point is sampling.
+ 采样是一个将函数离散化的过程
  We can discretize a function by sampling.

```c++
for (int x = 0; x < xmax; ++x) 
	output[x] = f(x);
```

Sampling is a core idea in graphics.
We sample time (1D), area (2D), direction (2D), volume (3D)...

**光栅化：二维采样**

光栅化本质上就是一个对连续二维信号进行离散采样（Sampling）的过程
+ 判断一个点是否在三角形内（可以靠叉乘判断）
+ 如果一个点恰巧在三角形的边界，要么不处理，要么特殊处理，图形API中一般有着严格规定

![[Pasted image 20260323153337.png]]

定义二维采样方法
$$
inside(t, x, t) = 
\left\{\begin{array}{l}
1 \space\space\space Point(x,y) \text{ in triangle t} \\
0 \space\space\space Otherwise
\end{array}\right.
$$
```c++
for(int x = xmin; x <= xmax; x++)
    for(int y = ; y <= ymax; y++)
        image[x][y] = inside(tri, x + 0.5, y + 0.5);
```

**采样优化**
+ 使用包围盒 Use a Bounding Box!
  ![[Pasted image 20260323154800.png|425]]
+ 增量三角形遍历 Incremental Triangle Traversal
  更适用于瘦且旋转的三角形
  ![[Pasted image 20260323154932.png|425]]


#### 2.3.3 光栅化到真正的显示设备上 Rasterization on Real Displays
+ 真正的液晶屏幕像素（特写） Real LCD Screen Pixels (Closeup)
  不同LCD屏幕一个像素中 RGB 排列方式也不同，右图中的 拜尔阵列(bayer pattern) 绿色像素较多是因为人眼对绿色较为敏感
  ![[Pasted image 20260323155343.png]]
+ 其他的显示方法 Aside: What About Other Display Methods?
  打印出的颜色是减色系统，为了节省墨水，打印通常使用半调的方式打印
  ![[Pasted image 20260323155717.png]]


### 2.4 采样 Sampling
#### 2.4.1 采样在图形学中很常见 Sampling is Ubiquitous in Computer Graphics
+ 光栅化 = 采样二维平面上的点 Rasterization = Sample 2D Positions![[Pasted image 20260606091543.png]]
+ 摄影 = 采样感光元件上的信息(将光学信息离散为图像上的像素) Photograph = Sample Image Sensor Plane![[Pasted image 20260606091626.png]]
+ 视频 = 采样时间![[Pasted image 20260606091651.png]]

#### 2.4.2 采样失真 Sampling Artifacts(Errors / Mistakes / Inaccuracies) in Computer Graphics
+ 锯齿 Jaggies(Staircase Pattern)
  采样频率对于信号来说不够高，造成了信号走样![[Pasted image 20260606091815.png]]
+ 摩尔纹 Moiré Patterns in Imaging
  左图跳过奇数行和奇数列后得到右图 ![[Pasted image 20260606092134.png]]
  + 车轮错觉效应 Wagon Wheel Illusion (False Motion)
    对于高速运动的物体，人眼采样跟不上物体的运动速度，导致看起来有的环在倒转 ![[Pasted image 20260606092321.png]]
> 总结来说：走样的原因就是
> 信号变换得太快，而采样太慢
> Signals are changing too fast (high frequency), but sampled too slowly

### 2.5 抗锯齿的原理 Antialiasing
先引出一个抗锯齿的解决方法：先模糊后采样 Blurring (Pre-Filtering) Before Sampling
在光栅化中，如果对一个三角形直接采样，每个像素值要么是纯白要么是纯红 ![[Pasted image 20260606092943.png]]
但是如果我们先进行一个滤波（模糊）然后再采样，三角形边缘的像素值可以取中间值 ![[Pasted image 20260606093257.png]]
+ 点采样 Point Sampling
![[Pasted image 20260606093412.png]]
+ 抗锯齿 Antialiasing
![[Pasted image 20260606093458.png]]
但是不能采用先采样后模糊的做法
![[Pasted image 20260606093659.png]]
BUT WHY?
接下来介绍抗锯齿的原理
#### 2.5.1 时域与频域 Spatial/Time Domain & Frequency Domain
- **时域**是真实世界，是惟一实际存在的域。因为我们的经历都是在时域中发展和验证的，已经习惯于事件按时间的先后顺序地发生。例如我们去音乐会听一段音乐，这段音乐会以声波的形式传入我们的耳中，随着时间的流逝，我们才能完整的欣赏这段音乐，这是在时域中发生的事。
- 在**频域**中，这段音乐只是由特定的音符组成的乐谱而已，这是永恒的，与时间无关。![[Pasted image 20260606094456.png]]
- **傅里叶变换**可以把信号从时域转化到频域，因为从时域或空间域的角度看，该信号是由多种频率不同的正弦波的叠加，而从频域角度看，该信号的频谱却如此的简单，这便于我们的观测和计算。 ![[Pasted image 20260606094559.png]]
- 将时域的信号（信号可以是周期与非周期信号）变成频域形式并加以分析的方法称为**频谱分析**。其目的是把复杂的时域波形，经过某种变换分解为若干单一的谐波分量来研究，以获得信号的频率结构以及各谐波和相位信息。这某种变换可以是傅里叶级数，也可以是傅里叶变换进行变换.这两者目的都一样，都是把时域信号变成频域以便于信号分析。
  
#### 2.5.2 傅里叶变换 Fourier Transform
+ 傅里叶变换可以将函数表示为正弦和余弦的加权和 ![[Pasted image 20260606094849.png]]
+ 时域和频域可以通过傅里叶变换和逆傅里叶变换相互转化 ![[Pasted image 20260606094954.png]]

#### 2.5.3 走样 Aliases
采样的频率小于信号的频率，还原出的函数就不准确 
![[Pasted image 20260606095414.png]]
对于曲线 $f(x)$ 以及虚线 $g(x)$ ，在给定的采样频率下无法区别两个不同的函数，即走样

#### 2.5.4 滤波（去掉特定频率的信息） Filtering = Getting rid of certain frequency contents
图像经过傅里叶变换后得到频率图，这里信息大多集中在低频，少量分布在高频
![[Pasted image 20260606095926.png]]
+ 如果应用高通滤波，得到的是边界信息 ![[Pasted image 20260606100137.png]]
+ 如果应用低通滤波，得到的是模糊效果 ![[Pasted image 20260606100122.png]]

#### 2.5.5 卷积 Convolution
滤波 = 卷积 = 平均 Filtering = Convolution (= Averaging)
卷积的简化理解：
![[Pasted image 20260606100421.png]]

卷积定理：时域的卷积等于频域的乘积，频域的卷积等于时域的乘积
通过卷积定理实现对图像的处理
+ 方法一
	+ 在时域空间域中通过卷积过滤
+ 方法二
	+ 通过傅里叶变换将时域转化为频域
	+ 乘以卷积核的傅里叶变换
	+ 通过逆傅里叶变换将频域转化回时域
![[Pasted image 20260606101122.png]]

图中的小正方体对应低通滤波
![[Pasted image 20260606101228.png]]

更大的正方体对应只允许更低频率通过的滤波器
![[Pasted image 20260606101330.png]]

#### 2.5.6 采样稀疏造成走样的原因
+ 采样就是在重复原始信号频率的内容，即 在时域进行采样实际上就是在频率进行周期延展 ![[Pasted image 20260606101544.png]]
+ 从频率上来说，采样如果较为稀疏，在信号的频率重复时就会发生重叠，重叠后的信息是原始频率混合在一起，无法从该采样结果恢复成原来的信号，就会发生走样现象 ![[Pasted image 20260606101844.png]]
> PS: 感觉还是有很多疑惑？具体应该需要去学习信号相关的知识...

### 2.6 反走样 Antialiasing
上一节我们分析了走样的原理：即**采样频率过慢导致频域中发生频率重叠**
#### 2.6.1 如何进行反走样
+ 增加采样率
	+ 本质上就是增加了采样时频谱中副本之间的距离
	+ 使用更高分辨率的显示器、传感器、更高规格的帧缓冲区等
	+ 代价是成本高
+ **反走样**
	+ 采样重复频谱前是其中的副本内容更窄，即在采样之前滤波高频信息 ![[Pasted image 20260606113837.png]]

#### 2.6.2 反走样的具体方法
+ 先模糊后采样
	+ 先使用一个像素大小的滤波器去求像素内的平均
	+ 在光栅化一个三角形时，一个像素区域内的平均值等于该三角形在该区域的**覆盖率**
![[Pasted image 20260606114217.png]]
![[Pasted image 20260606114235.png]]

+ 通过超采样测算**覆盖率**的一种近似方法 —— MSAA(Multi-Sample Anti-Aliasing)
  将一个像素点再细分为多个采样点，然后再求平均。实现一个近似 1 像素的效果
![[Pasted image 20260606115205.png|800]]

**总结**
- No free lunch！
	- MSAA会增加性能开销，但是在实际计算会让点的分布会更加有效、一些点还会被周围像素复用，所以并没有成倍增加性能开销
- 其他两个重要的抗锯齿方案
	- FXAA (Fast Approximate AA) —— 对有锯齿的图像通过图像匹配，将边缘锯齿处替换，速度快
	- TAA (Temporal AA) —— 与时间变化有关，通过检测，上一帧的一些结果会在下一帧得到复用
- 超分辨率
	- 强行从低分辨率到高分辨率，但是这样会空缺很多像素点信息
	- DLSS(Deep Learning Super Sampling)，强行拉高分辨率，再通过深度学习去填充预测像素点信息

## 3 可见度/遮挡 Visibility/occlusion
### 3.1 画家算法 Painter's Algorithm
+ 由远到近绘画物体，近处的物体会覆盖掉远处的物体
+ 为了知道物体的远近，我们需要先进行排序($O(n\log n)$的时间复杂度)
+ 但是无法解决互相遮挡的情况
![[Pasted image 20260606140714.png]]


### 3.2 深度缓冲 Z-Buffer
**原理**
+ 为每一个采样点（像素）存储离相机最近距离的深度值
+ 最终需要输出两个缓冲
	+ Frame Buffer stores color values
	+ Depth Buffer(z-buffer) stores depth
![[Pasted image 20260606141437.png]]

**Z-Buffer算法的实现**
> 注意：以下为了简化，深度值z永远是正的，越小表示越近，越大表示越远
```C++
// Initialize depth buffer to ∞
for (each triangle T) 
	for (each sample (x,y,z) in T) 
		if (z < zbuffer[x,y]) // closest sample so far 
			framebuffer[x,y] = rgb; // update color 
			zbuffer[x,y] = z; // update depth 
		else ; // do nothing, this sample is occluded
```
![[Pasted image 20260606141802.png]]

**总结**
+ 时间复杂度 $O(N)$ for n triangles (assuming constant coverage)
+ 与三角形进入缓冲的顺序无关
+ Z-Buffer 算法是最重要的算法，几乎在所有的 GPU 上都有实现
+ Z-Buffer 不能处理透明物体（需要特殊处理）