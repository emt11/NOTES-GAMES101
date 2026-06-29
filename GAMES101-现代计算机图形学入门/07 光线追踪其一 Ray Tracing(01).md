## 1 阴影贴图 Shadow Mapping
### 1.1 如何使用光栅化实现阴影？
![[Pasted image 20260626110104.png]]

+ 目前在光栅化着色中只局部考虑着色点、光源和相机，对于全局的其他物体、甚至于物体的其他部分对于该着色点的影响都不考虑
+ 在光栅化着色中会阴影的一种方法是 Shadow Mapping
+ Shadow Mapping 本质是一种图像空间的做法，在生成阴影这一步不需要知道场景的几何信息，该方法会出现走样现象，经典的 shadow mapping 只能处理点光源的情况
+ Shadow Mapping 的核心思想是：如果有点不在阴影里，那么这个点可以被摄像机和光源都看到

### 1.2 阴影贴图的具体实现
1. 从光源看向场景 Render from Light
   + 得到光源视角的深度信息（Z-Buffer）![[Pasted image 20260626110753.png|450]]
2. 从相机视角看向场景 Render from Eye
   + 把摄像机视角看到的点投影回光源视角的投影平面上，比较深度值，如果深度一致，则说明该点可以被光源照射到；如果深度不一致，说明该点无法被光源照射到 ![[Pasted image 20260626111019.png|500]]

### 1.3 可视化阴影贴图 Visualizing Shadow Mapping
1. 从光源方向看向场景 The scene from the light’s point-of-view ![[Pasted image 20260626111139.png]]
2. 得到光源方向下的深度信息 ![[Pasted image 20260626111415.png]]
3. 与 shadow map 比较深度信息得到阴影区域 
   这里的物体表面绿色不连续，可能的原因：1. shadow map 分辨率有限，会有走样问题；2. 比较大小的浮点数的计算有误差； ![[Pasted image 20260626111445.png]]
4. 得到有阴影的场景 ![[Pasted image 20260626111514.png|600]]

### 1.4 阴影贴图的问题
- 只能处理硬阴影(点光源)
- 质量取决于阴影贴图分辨率（基于图像空间的技术的问题）
- 比较深度值是否相等涉及浮点数精度问题，会带来各种问题
- 即使有很多问题，阴影贴图的方法仍是早期3D动画和现在所有3D游戏中的基本阴影技术

### 1.5 软阴影 Hard shadows vs. soft shadows
- 有一定大小的光源照射物体会产生本影(Umbra)和半影(Penumbra)，会形成软阴影
- 如果形成了软阴影，一定是因为光源有一定大小，点光源不会形成软阴影
![[Pasted image 20260626112159.png]]


## 2 Why Ray Tracing?
为什么要引入光线追踪呢？
1. 光栅化不能很好的处理全局的效果，如软阴影、光泽反射、间接光照等 ![[Pasted image 20260626163848.png]]
2. 光栅化速度快，但是质量相对较低 ![[Pasted image 20260626163924.png]]
3. 光线追踪虽然很精准，但是很慢
	+ 光栅化：实时，而光线追踪：离线
	+ 光线追踪一帧画面需要 1w 个 CPU 时

## 3 基本的光栅追踪算法
### 3.1 光线 Ray Light
我们定义光线为：
+ 光线直线传播
+ 光线之间不会“碰撞”
+ 光线是从光源出来再到相机（实际应用时，由于光路的可逆性，我们采用从相机到光源的做法）

### 3.2 光线投射 Ray Casting
**Appel 1968 - Ray casting**
1. 先准备一个成像平面(image plane)，对屏幕中的每一个像素投射一条光线，找到这条光线与物体的交点
2. 通过交点和灯光的连线判断该位置是否处于阴影中
![[Pasted image 20260626165043.png|450]]

**Pinhole Camera Model**
1. 考虑眼睛是一个针孔相机，从眼睛开始，向成像平面投射光线，记录与场景中物体最近的交点，这里就已经解决的 Z-Buffer 的问题 ![[Pasted image 20260626165512.png]]
2. 从交点向光源连线来来判断该点是否能被光源照亮，如果该连线中间没有物体阻挡，则说明该点可以被光源照亮。有了法线、入射方向、出射方向等数据就可以利用着色模型(如 Blinn Phong 模型)计算该点的着色，写入该像素 ![[Pasted image 20260626165648.png]]

### 3.3 递归(Whitted-Style)光线追踪
上一节中，光线投射只考虑一次弹射，实际上光线可以被多次弹射 ![[Pasted image 20260626165859.png]]
1. 对于 **Recursive Ray Tracing** ，假设场景中是一个玻璃球，从相机穿过一个像素向场景内投射光线，会发生反射和折射现象 ![[Pasted image 20260626170102.png]]
2. 由于光线进行了多次弹射，我们需要对每个弹射点计算其着色值，并加权求和到最终的像素中 ![[Pasted image 20260626170301.png]]

## 4 光线与物体表面相交 Ray-Surface Intersection
### 4.1 射线方程 Ray Equation
+ 光线可以被 一个起点(Origin) 和 方向(Direction) 确定 ![[Pasted image 20260626170528.png|450]]
+ 光线上的任意一点可以被以 t(time) 为自变量的函数来表示 ![[Pasted image 20260626170627.png|550]]

### 4.2 光线与球面相交 Ray Intersection With Sphere
+ 列出光线与球面方程，自然可以求解 t 
$$
\left\{\begin{array}{l}
\text{ Ray : } r(t) = o + td, \quad 0 \leq t < \infty \\
\text{ Sqhere : } (p - c) ^ {2} - R ^ 2 = 0
\end{array} 
\Longrightarrow
(o + td - c ^ 2) - R ^ 2 = 0 \Longrightarrow t=\ldots\right.
$$
![[Pasted image 20260626171440.png]]

扩展到更一般的隐式平面中：
$$
\left\{\begin{array}{l}
\text{ Ray : } r(t) = o + td, \quad 0 \leq t < \infty \\
\text{ Sqhere : } f(p) = 0
\end{array}
\Longrightarrow 
f(o + td) = 0
\Longrightarrow t = \ldots
\right.
$$


### 4.3 光线与显示标签相交 Ray Intersection With Triangle Mesh
#### 4.3.1 光线与三角形相交
+ 对于显式表面的渲染，光线如何与三角形求交很重要
+ 通过这个方法也可以判断一个点是否在物体内（点如果在封闭形状内，向外打一条光线，得到的交点数量一定是奇数）

当然，最简单的实现就是：遍历所有的三角形，分别求交点即可
1. 求光线与三角形所在平面的交点
2. 判断该点是否在三角形内
![[Pasted image 20260626172553.png|315]]

#### 4.3.2 光线与平面相交
+ 一个平面可以被平面的法向量和平面上的任意一点 $p^{'}$ 表示 ![[Pasted image 20260626172951.png]]
+ 这样平面上任意一点 $p$ 都可以被以下方程所表示 ![[Pasted image 20260626173042.png]]
+ 对于光线与平面相交的情况，列出交点方程
$$
\begin{matrix}
\left\{\begin{matrix}
\text{ Ray : } r(t) = o + td, 0 \leq t < \infty \\
\text{ Plane : } (p - p^{'}) \cdot N = 0
\end{matrix}\right.
\Longrightarrow (o + td - p^{'}) \cdot N = 0 \\
\Longrightarrow \frac{(p^{'} - o) \cdot N}{d \cdot N}, \space check: 0 \ leq t < \infty
\end{matrix}
$$

#### 4.3.3 MT算法 Möller Trumbore Algorithm
+ 一种更快的方法，直接求出光线与三角形所在平面交点的重心坐标，通过重心坐标判断是否在三角形内
+ 该方程组有3个式子，3个变量，对于有N个式子N个变量的线性方程组可以使用克莱姆法则(Cramer's Rule)求解
![[Pasted image 20260626174701.png]]

## 5 轴对齐包围盒 Axis-Aligned Bounding Box
### 5.1 体积盒
+ 一种加速光线与表面求交的方法 将复杂的物体包裹在简单的形状内
+ 计算时先计算光线是否与包围盒相交，如果相交再计算与物体的相交
![[Pasted image 20260626175702.png]]

### 5.2 轴对齐包围盒 Axis-Aligned Bounding Box(AABB)
+ 包围盒是三组对面形成的交集
+ 我们经常用的一种包围盒是轴对齐包围盒，该包围盒任何一个轴都是沿着坐标轴的
![[Pasted image 20260626175743.png|375]]

### 5.3 光线与轴对齐包围盒相交
在二维平面上（三维同理），我们分别记录光线进入和离开 x、y 平面的时间 ($t_{min}, t_{max}$)，则光线在这两组对面的长方形内部对应 x、y 平面的交集 ![[Pasted image 20260626180108.png]]
**Key ideas**:
+ 光线只有在进入所有成对的平板时，才进入盒子
+ 只要光线离开任何一对平板，光线就会离开盒子
因此对应三位包围盒，$t_{enter} = max\{t_{min}\}, t_{exit} = min\{t_{max}\}$
考虑到光线是一条射线而不是直线，那么
+ 如果 $t_{exit} < 0$ ，则包围盒在光线之后 --> 没有交点
+ 如果 $t_{enter} \leq 0 \space 且 \space t_{exit} < 0$ ，则光线的原点在包围盒内 --> 有交点
**即当 $t_{enter} < t_{exit} \space 且 \space t_{exit} \leq 0$ ，光线与轴对齐包围盒有交点**

使用轴对齐包围盒时，求 t 可以只用某一个轴的信息，而不用整个坐标，计算量更小
![[Pasted image 20260626181320.png|725]]

## 6 使用轴对齐包围盒加速光线追踪 Using AABBs to accelerate ray tracing
### 6.1 空间均匀划分 Uniform Spatial Partitions (Grids)
1. 空间划分其实是一个预处理的过程 ![[Pasted image 20260626190603.png]]
2. 预处理完成后，在光线追踪的过程中，只需要判断光线与相交的格子中是否包含物体即可 ![[Pasted image 20260626190843.png|450]]
>1. 如何知道一条光线会碰到哪一个盒子？最简单的想法，如果光线向右上方打，那么下一个经过的格子一定在右边或上边，只需要验证这两个格子即可，三维情况下也类似
>2. 不同的格子数加速效果不同，既不能太密也不能太稀疏
>3. 使用格子的划分方法在大量均匀分布的物体上比较有效 ![[Pasted image 20260626191035.png]]

### 6.2 其他不同的空间划分方法
+ 八叉树 Oct-Tre
	+ 将三位空间划分成八份（如果是二维的话则划分为四份），对于每个子节点，递归继续划分
	+ 通过制定好的规则来终止递归，如当一个格子中有足够少的物体时就停止
	+ 八叉树的维度高时，空间复杂度会很高，所以并不常用
+ KD 树 KD-Tree
	+ KD 树空间的划分复杂程度和维度无关
	- KD 树和八叉树的划分方法几乎完全相同，只不过它每次沿着某一个轴砍开，并且只砍一刀
	- 空间被划分成类似二叉树的结果，每次节点的划分底下都只有两个子节点
	- 划分时为了均匀起见，xyz 依次划分
+ BSP树 BSP-Tree
	+ BSP树是一种对空间二分的划分方法，它每次选一个方向砍开，它和KD树的区别是它不是横平竖直的划分，而且它会有越高维越不好计算的问题（砍开二维用线，砍开三维用面，维度越高越复杂）
![[Pasted image 20260626191220.png]]

### 6.3 KD-Tree
+ 预处理：将空间沿着 xyz 的顺序进行划分，并且划分时也按照（顺劈、横劈）顺序进行划分
  预处理之后所以的物体都分布在叶子节点上 ![[Pasted image 20260626191758.png]]
+ 适合于 KD-Tree 的数据结构
	+ 中间节点储存：沿哪一个轴划分、划分平面的位置、两个子节点的指针
	+ 叶子节点储存：物体
+ KD-Tree 的遍历过程 ![[Pasted image 20260626192947.png]]
+ KD-Tree 的问题
	+ 给出一个包围盒很难判定它和哪些三角形有交集，也是这个原因最近 10 年渐渐不用 KD 树的方法了
	+ 一个物体和很多包围盒都有交集的情况下，会存在多个叶子节点内

### 6.4 物体划分和 BVH 结构 Object Partitions & Bounding Volume Hierarchy (BVH)
从**物体划分**可以避免空间划分的一些问题，所以现在普遍采用 BVH 结构（BVH 不涉及三角形和包围盒求交，避免了一个物体存在多个叶子节点内，但是 BVH 的划分并没有划分开，包围盒可能会相交，不过做到尽可能重叠少就好，这也是当今一个研究的方向）
- 预处理：找到场景包围盒，递归地将物体划分为两个子集，划分到一定条件下停止（如每个子集不多于五个三角形），在叶子节点存储物体 ![[Pasted image 20260626194003.png]]
- 划分节点：
	- 选一个维度进行划分，如选择最长的轴进行划分
	- 取中间的物体(第n/2个三角形，可以使用快速选择算法)
- 适合于 BVH 的数据结构
	- 中间节点储存：包围盒、叶子节点的指针
	+ 叶子节点储存：包围盒、物体
+ BVH 的遍历过程
```c++
Intersect(Ray ray, BVH node) {              
	if (ray misses node.bbox) return;       //光线和整个BVH节点不相交，什么也不发生
	 
	if (node is a leaf node)                //如果光线与BVH的叶子节点相交
		test intersection with all objs;      //和节点内所有物体都求交
		return closest intersection;          //返回最近的交点
		
		hit1 = Intersect(ray, node.child1);   //如果相交的不是叶子节点，那光线与两个子节点都可能相交
		hit2 = Intersect(ray, node.child2);   //分别与两个子节点求交

		return the closer of hit1, hit2;      //返回最近的交点
}
```

### 6.5 空间划分和物体划分的对比
- 以KD树为例的空间划分 Spatial partition (e.g. KD-tree)
	- 空间与空间之间没有交集
	- 一个对象可能包含在多个区域中
- 以BVH为例的物体划分 Object partition (e.g. BVH)
	- 将对象集划分为不相交的子集
	- 每个集合的边界框可能在空间上重叠
![[Pasted image 20260626194542.png|400]]
