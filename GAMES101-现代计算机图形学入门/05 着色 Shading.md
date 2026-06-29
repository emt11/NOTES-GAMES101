## 0 从头回顾
1. 在空间中放置一个摄像机，并通过**模型变换**使得模型摆好姿势
2. 通过**观测变换**使得摄像机放至原点，得到标准的 $[-1, -1]^3$ 的空间
3. 通过**视口变换**将三维空间映射到二维屏幕上
4. 对屏幕空间中的三角形进行**光栅化**
![[Pasted image 20260606145808.png]]

## 1 着色的定义
+ 字典中的定义：通过平行线或色块在插画、图像上进行加深或上色处理
+ 在本课程中的定义：对一个物体应用不同材质的处理

## 2 一个简单的模型：Blinn-Phong 反射模型
> Blinn-Phong 是一个经验模型，并不完全符合物理

### 2.1 感知观察
![[Pasted image 20260606151207.png]]
对于上图而言：
+ **高光(Specular highlights)**：光滑表面镜面反射处附近形成高光
+ **漫反射(Diffuse reflection)**：在整个杯子上变化比较柔和，粗糙表面形成漫反射
+ **环境光(Ambient lighting)**：上图中背光处仍然有颜色，因为其接受了周围环境的间接光照，为了简化模型，我们会使用一个环境光常量来表示环境光对物体的影响

### 2.2 着色具有局部性
着色点(Shading Point)在局部上是一个平面，我们定义：
+ 观看方向(Viewer direction): v
+ 法线方向(Surface normal): n
+ 光照方向(Light direction): l
+ 表面参数 颜色、光泽度等(color, shininess)
![[Pasted image 20260606151746.png]]
注意，着色阶段不涉及阴影的生成
![[Pasted image 20260606151817.png]]

### 2.3 漫反射 Diffuse Reflection
**定义**：光线打到某个点时，光线会均匀地向各个方向散射，表面颜色在各个观测方向都相同
![[Pasted image 20260606152113.png]]

有多少光线被接收与面和光线的夹角有关，根据兰伯特余弦定理(Lambert’s cosine law)，单位面积上接收到的光的能量等于光线与法线的夹角的余弦
- 太阳能板在板面垂直于光照接受光能最大
- 地理上冬夏之分与光线照射方向与地面夹角有关（太阳高度角）
![[Pasted image 20260606152426.png]]

**光线衰减(Light Falloff)**
从能量守恒的角度来看，单位面积上光的强度与距离的平方成反比
![[Pasted image 20260606152643.png]]

**兰伯特漫反射模型 Lambertian (Diffuse) Shading**
$L_d$ 表示的是某个表面点最终向观察者反射出去的漫反射光强
+ $k_d$: 漫反射系数，可以理解为 物体本身的颜色，由三通道 RGB 值组成。因为漫反射会吸收一部分光，如白光 $RGB = (1, 1, 1)$ 照射到红色物体 $RGB = (1, 0, 0)$ ，相乘会得到红色（观察到的）
+ $(I/r^2)$: 光源能量/着色点到光源的距离，具体理解为光源反射到该点的光能量
+ $(n \cdot l)$: 光照方向与法线的夹角余弦，具体理解为有多少光能量能够被接收
![[Pasted image 20260606152903.png]]
![[Pasted image 20260606152938.png]]

### 2.4 镜面反射/高光 Specular Term(Blinn-Phong)
+ 高光的强度取决于观察方向，观察方向与镜面反射方向相近时能看见高光
+ 观察方向与镜面反射方向的接近程度可以由半程向量 $h$ 和法线 $n$ 的接近程度来衡量，半程向量更容易计算
+ 在 Blinn-Phong 模型中镜面反射忽略了计算着色点吸收能量的比例
+ 使用观察方向和镜面反射方向判断是否接近是 Phong 模型
- 增加p的值来限制高光范围(小高光 p 大约在 100 到 200)
![[Pasted image 20260606164546.png]]
![[Pasted image 20260606164609.png]]

### 2.5 环境光 Ambient Term
在 Blinn-Phong 模型中环境光不取决于任何东西，它是一个常数。这是一个大胆的简化
![[Pasted image 20260606164839.png]]

### 2.6 Blinn-Phong 反射模型
![[Pasted image 20260606165031.png]]

## 3 着色频率 Shaping Frequencies
![[Pasted image 20260606165148.png]]
从左到右：
+ **逐三角形着色 Shade each triangle (flat shading)**
	+ 每个三角形内部着色没有变化，一个平面一个法向量
	+ 对于光滑平面的效果并不好
+ **逐顶点着色 Shade each vertex (Gouraud shading)**
	+ 从三角形的顶点插入颜色
	+ 每个顶点都有一个法向量
	+ 三角形内部（除去顶点）的颜色由插值计算出来
+ **逐像素着色 Shade each pixel (Phong shading)**
	+ 对每个三角形插入一个法向量
	+ 在三角形内部为每一个像素插值出对应的法向量

下图是几种着色频率的对比图
![[Pasted image 20260606165835.png]]
+ 在几何模型较为复杂时，用简单的 Flat Shading 一样能得到很好的效果
+ 当然在几何模型较为简单时，使用 Phong Shading 的效果是最好的

**补充**
**定义逐顶点的法线 Defining Per-Vertex Normal Vectors**
+ 如果能知道具体表示的几何体是怎样的，如下图中是一个球体，则每个顶点的法线方向就是球心到顶点 
  ![[Pasted image 20260606170351.png]]

+ 否则必须从相邻三角形面推断出顶点法线，一个简单的方法是求相邻面法线的平均法线 ![[Pasted image 20260606170608.png]]

**定义逐像素的法线 Defining Per-Pixel Normal Vectors**
+ 可以使用顶点法线的重心插值![[Pasted image 20260606170740.png]]

## 4 图形（实时渲染）管线 Graphics (Real-time Rendering) Pipeline
### 4.1 图形管线 Graphics Pipeline
+ Vertex Processing: Model, View, Projection transforms
+ Sampling triangle: Rasterization
+ Fragment Processing: Z-Buffer
+ Vertex & Fragment Processing: Shading, Texture mapping
![[Pasted image 20260606170921.png]]

> 注意：关于哪几个顶点组成三角形是在一开始输入前模型就已经定义好的

### 4.2 着色器程序 Shader Programs 
这里描述了顶点和片元处理过程的操作，并且着色器会对每个顶点或片元执行一次，只写出单次操作即可
```C++
uniform sampler2D myTexture;                   //全局变量 固定的纹理
uniform vec3 lightDir;                         //全局变量 固定的光照方向
varying vec2 uv;                               //每个片元插值得到的uv坐标
varying vec3 norm;                             //每个片元插值得到的法线方向

void diffuseShader()
{  
  vec3 kd;                                      //Eigen库定义三维向量kd
  kd = texture2d(myTexture, uv);                //进行纹理查找以获取此时表面材质颜色
  kd *= clamp(dot(–lightDir, norm), 0.0, 1.0);  //漫反射系数、光照都为常数的最简单的漫反射
  gl_FragColor = vec4(kd, 1.0);                 //将得到的值返回到gl_GragColor
}
```
练习shader：[https://www.shadertoy.com](https://www.shadertoy.com)

## 5 当下实时渲染的发展
**目标**：实时高度复杂的 3D 场景
![[Pasted image 20260606172219.png]]

**用于执行图形管线的硬件：GPUs**
GPU 分为独立显卡(Discrete GPU Card)与集成显卡(Integrated GPU)，GPU的并行度高，适合处理图形学的相关计算
![[Pasted image 20260606172316.png]]
![[Pasted image 20260606172324.png]]

## 6 纹理映射 Texture Mapping
如何实现在不同位置有不同的颜色？
![[Pasted image 20260607095107.png]]

### 6.1 纹理是二维图像
就像地球可以被剪开为一张二维世界地图一样，在三维空间上的物理也可以被一张二维纹理图片覆盖，每个三维物体表面上的一点对应二维纹理图像上的一个坐标
![[Pasted image 20260607095159.png]]

### 6.2 纹理映射 Texture Applied to Surface
纹理映射：纹理坐标 uv (u, v约定俗称范围是0~1), 首先我们只有三角形顶点的纹理坐标，需要利用光栅化中插值求出的纹理坐标（每一个像素点），再将纹理坐标映射到物体表面
> 这里三角形顶点到纹理坐标的关联以及纹理图像的设计都无关计算机图形学，是由美工或程序自动化来实现的

![[Pasted image 20260607095935.png]]![[Pasted image 20260607100227.png]]
同样一个纹理可以被重复使用，当然前提是纹理可以被无缝连接(tileable)
![[Pasted image 20260607100342.png]]

## 7 重心坐标插值 Interpolation Across Triangles: Barycentric Coordinates
### 7.1 为什么要进行插值
+ 为了在三角形中进行平滑的过渡
+ 这里的插值是在光栅化时进行的，一般是对坐标、纹理坐标、法线、深度、颜色等进行插值

### 7.2 重心坐标 Barycentric Coordinates
#### 7.2.1 什么是重心坐标
+ 三角形所在平面上任意一点都可以被三角形顶点坐标的线性组合所表示
+ 重心坐标为 $(\alpha,\beta,\gamma)$，其中 $\alpha + \beta + \gamma == 1$
+ 如果 $\alpha \space \beta \space \gamma$ 都是非负的，则该点在三角形内
![[Pasted image 20260607101252.png]]

#### 7.2.2 利用面积确定系数 Geometric viewpoint — proportional areas
![[Pasted image 20260607101345.png]]
其中质心的坐标为 $(\frac{1}{3}, \frac{1}{3}, \frac{1}{3})$ 
![[Pasted image 20260607101449.png]]

#### 7.2.3 三角形坐标计算公式
利用叉积的模等于其形成三角形大小算出面积，得到简化公式
![[Pasted image 20260607101537.png]]
>PS: 不用掌握

#### 7.2.4 重心坐标的使用
+ $V_A, V_B, V_C$ 可以是位置、纹理坐标、颜色、法线、深度、材质等属性
+ 重心坐标在投影变换下会发生变换，如果想利用重心坐标插值三维中的属性，应该在投影前就插值，具体的操作是对投影后的三角形应用逆变换再插值
![[Pasted image 20260607101653.png]]
> 如果顶点里有颜色，就插值颜色；如果顶点里没有颜色（现代纹理映射最常见的情况），就主要插值 UV、法线、位置等属性，而最终颜色是在纹理采样和光照计算之后才得到的。

## 8 纹理应用 Applying Textures
```C++
for each rasterized screen sample (x,y):
	(u,v) = evaluate texture coordinate at (x,y)  //利用重心坐标算出每个采样点uv坐标
	texcolor = texture.sample(u,v);               //通过uv坐标查询纹理贴图
	set sample’s color to texcolor;               //通常定义的是漫反射系数Kd
```

## 9 纹理放大 Texture Magnification
### 9.1 问题
设想这样的情况：当屏幕分辨率过大，而纹理分辨率过小，那么在进行查询纹理贴图时，就会出现多个像素(pixel)映射到了同一个纹素(texel)上，这样如果强行取对应最近的纹理坐标，结果如最左侧图所示
![[Pasted image 20260607102242.png]]
那么如何解决该问题呢？

### 9.2 双线性插值 Bilinear Interpolation
对于当前的采样点（红点），我们找到临近的四个点，在水平竖直分别投影出 s, t 长度(<=1)
先在水平方向上插值得到 $u_0, u_1$ ，然后在 $u_0, u_1$ 垂直方向上插值得到采样值，这样采样点就综合考虑了临近四个点的纹理属性值
![[Pasted image 20260607102937.png]]
$$
\begin{gather*}
Linear interpolation(1D): \space lerp(x, v_0, v_1) = v_0 + x(v_1 - v_0) \\
Two helper lerps: \space u_0 = lerp(s, u_{00}, u_{10}), \space u_1 = lerp(s, u_{01}, u_{11}) \\
Final vertical lerp: \space f(x, y) = lerp(t, u_0, u_1)
\end{gather*}
$$

### 9.3 双三次插值 Bicubic Interpolation
- 取周围16个点做竖直和水平的插值
- 每次用4个，做三次的插值，不是用线性的差值
- 双向三次插值运算量大，但是效果好

> 注意，这里讨论的插值和第7章 [[05 着色 Shading#7 重心坐标插值 Interpolation Across Triangles Barycentric Coordinates]] 不同，
> - 重心坐标插值是对顶点属性的插值：如坐标、法线、纹理坐标UV、深度、颜色等
> - 纹理插值是对纹理属性的插值：颜色
>   一般认为重心插值（光栅化）没有颜色，纹理插值后才有了颜色，再进行着色


## 10 纹理过大（特殊情况） Texture Magnification (hard case)
### 10.1 问题
当像素覆盖纹理过大时，此时直接用像素点 UV 坐标最接近的纹素(texel)，会出现走样现象
![[Pasted image 20260607115850.png]]
![[Pasted image 20260607115903.png]]

- 利用超采样反走样可行吗 —— 可行但是性能会明显降低
- 采样会引起走样，如果我们不采样，只得到一定范围的平均值就能解决问题
	- 点查询 Point Query
	- 范围查询 Range Query （本质上还是滤波？）

### 10.2 Mipmap
**Mipmap (L. Williams 83)**
+ Mipmap 只能进行快速的、近似的、正方形的范围查询
+ "Mip" 来自拉丁语 "multum in parvo" ，意思是在一个小空间内有很多的东西
+ 相比只存储一张原图，多占用了 1/3 的空间
![[Pasted image 20260607122031.png]]

**计算 Mipmap 等级 Computing Mipmap Level D**
- 任何一个像素会在纹理上对应一片区域，可以通过将像素的中点映射到纹理上再计算距离的方法来得到一个像素近似的正方形区域边长L是多少，然后取2的对数得到等级D(即为求在第几层是该像素映射的纹理也是一个像素大小的区域)，此时拿等级D上的坐标即可
![[Pasted image 20260607122314.png]]

**三线性插值 Trilinear Interpolation**
- 简单通过这种方法只能查询整数层数上的情况，在实际应用纹理时不连续，因此在查询时可以先用双线性插值分别得到相邻两个整数层级上的值，再用得到的值再做一次插值(三线性插值)得到连续的值
- 三线性插值在实时渲染有着广泛应用，因为它能得到连续的结果，并且只需要做两次查询和一次插值，开销较小
![[Pasted image 20260607122724.png]]
![[Pasted image 20260607122748.png]]

### 10.3 各向异性过滤 Anisotropic Filtering
**使用 Mipmap 的问题**
与超采样结果相比，使用mipmap得到的结果在远处出现了过渡模糊的现象(Overblur)
![[Pasted image 20260607123125.png]]
这是因为 mipmap 只能查询正方形区域，映射得到的结果近似为正方形太过勉强，另外三线性插值本身也是近似
![[Pasted image 20260607123208.png]]

**各向异性过滤 Anisotropic Filtering**
各项异性过滤相比于 Mipmap（对角线） 将图片在 X、Y 分别方向上进行了放缩，能够很好的解决映射在纹理上的竖直或横向的矩形区域，但仍不能很好的解决斜向的长条状区域
![[Pasted image 20260607123332.png]]
> 游戏设置中多少X就是分别在两个方向压缩几次，随着X不断变大，最终会收敛到原图3倍的空间，所以这里X开最高也不会占用太多性能

**EWA过滤 EWA filtering**
EWA过滤可以把任意不规则的形状拆成很多个的圆形去覆盖这个形状，但是多次查询会增加耗时
![[Pasted image 20260607123811.png]]


## 11 纹理的应用 Application of Texture
### 11.1 纹理的总类
在现代 GPUs 中，纹理就是一块可以快速做点查询、范围查询的内存区域，不仅限于图像
应用：
+ 环境光照 Environment lighting
- 存储微观几何细节（如凹凸） Store microgeometry
- 程序化纹理 Procedural textures
- 实体建模 Solid modeling
- 体渲染 Volume rendering

### 11.2 环境光贴图 Environment Map
- 环境光贴图把来自各个方向的光照记录下来，假设环境光来自无限远，只记录方向，来表示环境光(右侧为经典模型犹他茶壶 Utahpot) ![[Pasted image 20260607140639.png]]
+ 球面环境映射(Spherical Environment Map)可以类比为地球仪，但是贴图上下部分存在拉伸和扭曲问题 ![[Pasted image 20260607140724.png]]
+ Cube Map把环境光记录在立方体的表面上，沿法线方向把正方体映射到球面上，缺点是多了计算方向判断在正方体哪个面上 ![[Pasted image 20260607140908.png]]![[Pasted image 20260607140926.png]]

### 11.3 凹凸贴图 Bump Mapping
贴图不仅可以存储颜色，还能存储高度/法线移动，但是它不改变几何信息，因此可以得到假的着色效果，产生凹凸的感受
![[Pasted image 20260607141209.png]]

在着色计算过程中逐像素地扰动表面法线，由此定义每个纹素高度偏移
+ **二维下扰动法线**的具体过程：定义切线然后算出法线的偏移
	- 原本的表面法线 $n(p) = (0, 1)$ 
	- p 的导数 $dp = c * [h(p+1) - h(p)]$ 
	- 扰动法线是 $n(p) = (-dp, 1).normalized()$ 
	![[Pasted image 20260607141637.png|450]]
+ **三维下扰动法线**的具体过程：
	+ 原本的表面法线
	+ p 的导数 $dp/du = c1 * [h(u+1) - h(u)], dp/dv = c2 * [h(v+1) - h(v)]$ 
	+ 扰动法线是 $n = (-dp/du, -dp/dv, 1)$ 
	+ 注意以上都是在局部坐标中进行

### 11.4 位移贴图 Displacement mapping
- 相比于凹凸贴图更真实，使用相同的原理和纹理使顶点发生移动
- 代价是要求模型本身面数足够多，需要顶点间间隔比定义的采样频率高(DirectX提供了动态的曲面细分，仅在需要的时候提供足够的面数)
![[Pasted image 20260607142535.png]]

### 11.5 程序化纹理 3D Procedural Noise + Solid Modeling
- 定义了三维空间中的噪声函数，并没有真正生成一张图像
- 函数可以经过各种处理变成自己想要的样子，如使用纹理表示山脉起伏的信息
![[Pasted image 20260607142605.png]]

### 11.6 预计算着色 Provide Precomputed Shading
- 环境光遮蔽(Ambient occlusion)可以提前计算好储存在贴图中，使用时将其他着色结果乘以环境光遮蔽的结果得到带有环境光遮蔽的结果
![[Pasted image 20260607142629.png]]

### 11.7 三维纹理和体渲染 Solid modeling and Volume rendering
+ 三维纹理广泛应用到体渲染之中，比如在医学领域使用核磁共振得到三维空间中的密度信息进行渲染
![[Pasted image 20260607142712.png]]

