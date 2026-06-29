## 1 为什么学习变换 Why study Transformation?
### 1.1 模型变换 Modeling
+ 场景展示 —— 描述摄像机的位置移动
  ![[Pasted image 20260315174552.png|550]]
+ 机器人跳舞 —— 物体的旋转
  ![[Pasted image 20260315174654.png|550]]
+ 皮克斯开场动画台灯压扁字母 I —— 物体的缩放
  ![[Pasted image 20260315174822.png|550]]

### 1.2 观测变换 Viewing
- 光栅化成像中涉及大量的视图变换
  ![[Pasted image 20260315174944.png|550]]


## 2 二维变换 2D Transformations
### 2.1 缩放变换矩阵 Scale Matrix
![[Pasted image 20260315175347.png|600]]
$$
\begin{bmatrix}
x' \\ y'
\end{bmatrix}
 = 
 \begin{bmatrix}
0.5 & 0 \\ 0 & 1
\end{bmatrix}
\begin{bmatrix}
x \\ y
\end{bmatrix}
$$

### 2.2 翻转变换矩阵 Reflection Matrix
![[Pasted image 20260315175709.png|600]]
$$
\begin{bmatrix}
x' \\ y'
\end{bmatrix}
 = 
 \begin{bmatrix}
-1 & 0 \\ 0 & 1
\end{bmatrix}
\begin{bmatrix}
x \\ y
\end{bmatrix}
$$

### 2.3 剪切变换矩阵 Shear Matrix
![[Pasted image 20260315180043.png|600]]
$$
\begin{bmatrix}
x' \\ y'
\end{bmatrix}
 = 
 \begin{bmatrix}
1 & a \\ 0 & 1
\end{bmatrix}
\begin{bmatrix}
x \\ y
\end{bmatrix}
$$

### 2.4 旋转变换矩阵 Rotation Matrix
![[Pasted image 20260315180228.png|600]]
$$
\begin{bmatrix}
x' \\ y'
\end{bmatrix}
 = 
 \begin{bmatrix}
\cos\theta & -\sin\theta \\ \sin\theta & \cos\theta
\end{bmatrix}
\begin{bmatrix}
x \\ y
\end{bmatrix}
$$
### 2.5 线性变换 = 矩阵 Linear Transforms = Matrices
$$
\begin{bmatrix}
x' \\ y'
\end{bmatrix}
 = 
 \begin{bmatrix}
a & b \\
c & d
\end{bmatrix}
\begin{bmatrix}
x \\ y
\end{bmatrix}
$$

## 3 齐次坐标 Homogeneous Coordinates
### 3.1 为什么学习齐次坐标  Why Homogeneous Coordinates
+ 平移变换不是线性变换，无法直接用矩阵表示 Translation cannot be represented in matrix form
  ![[Pasted image 20260315194621.png]]
$$
\begin{gather*}
x' = x + t_x \\ 
y' = y + t_y \\\\
\begin{bmatrix}
x' \\ y'
\end{bmatrix}
 = 
 \begin{bmatrix}
a & b \\
c & d
\end{bmatrix}
\begin{bmatrix}
x \\ y
\end{bmatrix}
 + \begin{bmatrix}
t_x \\ t_y
\end{bmatrix}
\end{gather*}
$$
### 3.2 解决：齐次坐标 Solution: Homogenous Coordinates
**添加第三个坐标 Add a third coordinate (w-coordinate)**
在齐次坐标中，二维坐标变为：(x, y, **w**)$^T$，对应普通坐标 $(\frac{x}{w}, \frac{y}{w}), (w \ne 0)$ 
我们定义：
+ 2D Point   = (x, y, **1**)$^T$
+ 2D vector = (x, y, **0**)$^T$
这样平移就可以表示为 Matrix representation of translations
$$
\begin{pmatrix}
x' \\ y' \\ w'
\end{pmatrix}
=
\begin{pmatrix}
1 & 0 & t_x \\
0 & 1 & t_y \\
0 & 0 & 1
\end{pmatrix}
\cdot
\begin{pmatrix}
x \\ y \\ 1
\end{pmatrix}
= 
\begin{pmatrix}
x + t_x \\ y + t_y \\ 1
\end{pmatrix}
$$
> 如果平移一个向量呢？ / 为什么向量的 w = 0 ？
> || 当然是没有变化，向量具有平移不变性 ||

**w 坐标为 0 或 1 才有效 Valid operation if w-coordinate of result is 1 or 0**
+ vector + vector = vector
+ point - point = vector
+ point + vector = point
+ point + point = ??
  || 两个点相加的结果为其中点，将 所有元素 / 2 即可 ||

### 3.3 仿射变换 Affine Transformations
- 仿射变换等于线性变换加平移：
$$
\begin{aligned}
x' &= ax + by + t_x, \\
y' &= cx + dy + t_y
\end{aligned}
$$
	也可以写成：
$$
\begin{pmatrix}
x' \\
y'
\end{pmatrix}
=
\begin{pmatrix}
a & b \\
c & d
\end{pmatrix}
\begin{pmatrix}
x \\
y
\end{pmatrix} +
\begin{pmatrix}
t_x \\
t_y
\end{pmatrix}
$$
- 使用齐次坐标（homogeneous coordinates）：
$$
\begin{pmatrix}
x' \\
y' \\
1
\end{pmatrix}
=
\begin{pmatrix}
a & b & t_x \\
c & d & t_y \\
0 & 0 & 1
\end{pmatrix}
\begin{pmatrix}
x \\
y \\
1
\end{pmatrix}
$$

### 3.4 使用齐次坐标的 2D 变换 2D Transformations
$$
\begin{gather*}
Scale: S(s_x, s_y) = 
\begin{pmatrix}
s_x & 0 & 0 \\
0 & s_y & 0 \\
0 & 0 & 1
\end{pmatrix}\\\\
Rotation: R(\theta) = 
\begin{pmatrix}
\cos\theta & -\sin\theta & 0 \\ \sin\theta & \cos\theta & 0 \\ 0 & 0 & 1 
\end{pmatrix} \\
\\
Translation: T(t_x, t_y) = 
\begin{pmatrix}
1 & 0 & t_x \\ 0 & 0 & t_y \\ 0 & 0 & 1 
\end{pmatrix}
\end{gather*}
$$


## 4 逆变换 Inverse Transform
$M^{-1}$ 是 $M$ 矩阵的逆，也是其逆变换
![[Pasted image 20260317104444.png]]

## 5 组合变换 Compose Transform
**变换的顺序会影响结果 Transform Ordering Matter**
![[Pasted image 20260317104953.png]]
+ 矩阵乘法不满足交换律
$$
R_{45} \cdot T_{(1, 0)} \ne T_{(1, 0)} \cdot  R_{45}
$$
+ 矩阵运算顺序是从右到左的
$$
T_{(1, 0)} \cdot  R_{45} 
\begin{bmatrix}
x \\ y \\ 1
\end{bmatrix}
=
\begin{bmatrix}
1 & 0 & 1 \\ 0 & 1 & 0 \\ 0 & 0 & 1
\end{bmatrix}
\begin{bmatrix}
\cos45^{\circ} & -\sin45^{\circ} & 0 \\ \sin45^{\circ} & \cos45^{\circ} & 0 \\ 0 & 0 & 1
\end{bmatrix}
\begin{bmatrix}
x & y & 1
\end{bmatrix}
$$

**一连串的仿射变换可以被矩阵乘法组成，这对性能来说很重要**
$$
A_n(...A_2(A_1(x))) = \underbrace{A_n \cdots A_2 \cdot A_1}_{\text{Pre-multiply of n matrices}}  \cdot
\begin{pmatrix}
x \\ y \\ 1
\end{pmatrix}
$$

## 6 分解组合组合变换
按给定 **点 c** 来旋转
1. 平移旋转中心到原点
2. 旋转
3. 平移回原位置
![[Pasted image 20260317123132.png]]
$$T(-c) \cdot R(\alpha) \cdot T(c)$$

## 7 3D变换 3D Transforms
### 7.1 使用齐次坐标描述三维空间中的点和向量
**再一次使用齐次坐标 Use homogeneous coordinates again**
在齐次坐标中，三维坐标变为：(x, y, z, **w**)$^T$，对应普通坐标 $(\frac{x}{w}, \frac{y}{w}, \frac{z}{w})), (w \ne 0)$ 
我们定义：
+ 3D Point   = (x, y, z, **1**)$^T$
+ 3D vector = (x, y, z, **0**)$^T$

**使用 4 $\times$ 4 矩阵来表示仿射变换 Use 4×4 matrices for affine transformation**
$$
\begin{pmatrix}
x' \\ y' \\ z' \\ 1
\end{pmatrix}
=
\begin{pmatrix}
a & b & c & t_x \\
d & e & f & t_y \\
g & h & i & t_z \\
0 & 0 & 0 & 1
\end{pmatrix}
\cdot
\begin{pmatrix}
x \\ y \\ z \\ 1
\end{pmatrix}
$$

### 7.2 三维空间中的缩放、平移和旋转
+ 缩放
$$
S(s_x, s_y, s_z) = 
\begin{pmatrix}
s_x & 0 & 0 & 0 \\
0 & s_y & 0 & 0 \\
0 & 0 & s_z & 0 \\
0 & 0 & 0 & 1
\end{pmatrix}
$$
+ 平移
$$
T(t_x, t_y, t_z) = 
\begin{pmatrix}
1 & 0 & 0 & t_x \\
0 & 1 & 0 & t_y \\
0 & 0 & 1 & t_z \\
0 & 0 & 0 & 1
\end{pmatrix}
$$
+ 旋转
  ![[Pasted image 20260318203816.png|275]]
$$
\begin{gather*}
R_x(\alpha) = 
\begin{pmatrix}
1 & 0 & 0 & 0 \\
0 & \cos\alpha & -\sin\alpha & 0 \\
0 & \sin\alpha & \cos\alpha & 0 \\
0 & 0 & 0 & 1
\end{pmatrix}
\\ \\
R_y(\alpha) = 
\begin{pmatrix}
\cos\alpha & 0 & \sin\alpha & 0 \\
0 & 1 & 0 & 0 \\
-\sin\alpha & 0 & \cos\alpha & 0 \\
0 & 0 & 0 & 1
\end{pmatrix}
\\ \\
R_z(\alpha) = 
\begin{pmatrix}
\cos\alpha & -\sin\alpha & 0 & 0 \\
\sin\alpha & \cos\alpha & 0 & 0 \\
0 & 0 & 1 & 0 \\
0 & 0 & 0 & 1
\end{pmatrix}
\end{gather*}
$$
> 这里绕 Y 轴旋转 相当于从 xoz 平面做逆旋转，所以需要取逆（旋转的逆就是转置）

**任意一个旋转可以分解为 绕 X、Y、Z 轴的组合 Compose any 3D rotation from Rx, Ry, Rz** 
$$
R_{xyz}(\alpha,\beta,\gamma) = R_x(\alpha) R_y(\beta) R_z(\gamma)
$$
- 三个一组的描述物体旋转的参量叫欧拉角 Euler angles
- 在飞行模拟器中一般有滚转、俯仰、偏航 Often used in flight simulators: roll, pitch, yaw
![[Pasted image 20260318204317.png|550]]

**绕 n 轴旋转 $\alpha$ 角度 Rotation by angle α around axis n**
$$
R(n,\alpha) = \cos(\alpha)\text{I} + (1-\cos{\alpha})nn^T + sin(\alpha)
\underbrace{\begin{pmatrix}
0 & -n_z & n_y \\ n_z & 0 & -n_x \\ -n_y & n_x & 0
\end{pmatrix}}_{N}
$$

## 8 观测变换 Viewing transformation
### 8.1 视图变换 View/Camera transformation
#### 8.1.1 什么是视图变换 What is view transformation
我们想象一下如何拍照
+ 找到一个好位置并放好要拍照的对象（model transformation）
+ 找到一个好的角度去放置相机（**view transformation**）
+ "茄子!"（projection transformation）

#### 8.1.2 如何确定视图变换 How to perform view transformation
定义一个相机
+ 位置 Position $\vec{e}$
+ 拍照方向 Look-at / gaze direction $\hat{g}$
+ 向上方向（来确定相机左右倾斜方向） Up direction $\hat{t}$
![[Pasted image 20260320090759.png|400]]  

#### 8.1.3 标准位置 Key observation
+ 如果相机和所有对象都一起移动，那么它们之间的相对位置不变，拍出来的“照片”也不变
  If the camera and all objects move together, the “photo” will be the same
  ![[Pasted image 20260320091115.png]]
+ 我们通常将相机的位置移动到原点、向上方向为Y轴方向、看向-Z轴方向，物体与相机同时变换
  The origin, up at Y, look at -Z 
  And transform the objects along with the camera
  ![[Pasted image 20260320091658.png|450]]
  这个变换被称为视图变换
$$
M_{view} = R_{view}T_{view}
$$
	1. 移动 e 到 原点
$$
T_{view} = 
\begin{bmatrix}
1 & 0 & 0 & -x_e \\
0 & 1 & 0 & -y_e \\
0 & 0 & 1 & -z_e \\
0 & 0 & 0 & 1
\end{bmatrix}
$$
	2. 旋转 g 到 -Z, t 到 Y,  (g $\times$ t) 到 X
$$
\begin{gather*}
R_{view}^{-1} = 
\begin{bmatrix}
x_{\hat{g}\times\hat{t}} & x_t & x_{-g} & 0 \\
y_{\hat{g}\times\hat{t}} & y_t & y_{-g} & 0 \\
z_{\hat{g}\times\hat{t}} & z_t & z_{-g} & 0 \\
0 & 0 & 0 & 1
\end{bmatrix}
\\ \\ 
R_{view} = 
\begin{bmatrix}
x_{\hat{g}\times\hat{t}} & y_{\hat{g}\times\hat{t}} & z_{\hat{g}\times\hat{t}} & 0 \\
x_t & y_t & z_t & 0 \\
x_{-g} & y_{-g} & z_{-g} & 0 \\
0 & 0 & 0 & 1
\end{bmatrix}
\end{gather*}
$$ 
> 因为旋转矩阵是正交矩阵，其 $R^{-1} = R^T$ ，所以先求出逆变换（更好求），再取转置即可
> 其中 $$R_{view}^{-1} \underbrace{\begin{bmatrix} 1 & 0 & 0 \\ 0 & 1 & 0 \\ 0 & 0 & 1 \end{bmatrix}}_{\text{标准轴 } (X, Y, -Z)} = \underbrace{\begin{bmatrix} \hat{g}\times\hat{t} & \hat{t} & -\hat{g} \end{bmatrix}}_{\text{相机轴 } {(t \times g, t, g) }}$$

### 8.2 投影变换 Projection transformation
#### 8.2.1 正交投影和透视投影 Orthographic projection & Perspective projection
![[Pasted image 20260321092341.png]]
![[Pasted image 20260321092349.png]]

#### 8.2.2 正交投影 Orthographic Projection
**一个简单的理解**
+ 相机位于原点，朝向 -Z，上指 Y 方向
+ 去掉 Z 坐标
+ 将结果矩阵平移并缩放到 $[-1, 1]$
 ![[Pasted image 20260321092823.png|650]]

**正式理解**
+ 我们想要将立方体空间 $[l, r] \times [b, t] \times [f, n]$ 映射成一个标准的正方体 $[-1, 1]^3$
+ 在变换时，先平移（中心移到原点）后缩放（长宽高缩放为 2）
![[Pasted image 20260321093231.png]]

**变换矩阵**
$$
M_{ortho} = 
\begin{bmatrix}
\frac{2}{r - l} & 0 & 0 & 0 \\ 
0 & \frac{2}{t - b} & 0 & 0 \\
0 & 0 & \frac{2}{n - f} & 0 \\
0 & 0 & 0 & 1
\end{bmatrix}
\begin{bmatrix}
1 & 0 & 0 & -\frac{r + l}{2} \\ 
0 & 1 & 0 & -\frac{t + b}{2} \\
0 & 0 & 1 & -\frac{n + f}{2} \\
0 & 0 & 0 & 1
\end{bmatrix}
$$

**注意**
+ 相机朝向 -Z 导致近和远反直觉（n > f）
+ 这就是为什么 OpenGL 使用左手坐标系

#### 8.2.3 透视投影 Perspective Projection
**什么是透视投影**
+ 在计算机图形学、艺术、视觉非常常见
+ 远处的物体看起来更小
+ 平行线会相交与一点
![[Pasted image 20260321095026.png|600]]


**如何实现透视投影**
+ 先将锥体挤压成一个长方体（$M_{persp \to ortho}$）
+ 再做正交投影
![[Pasted image 20260321095426.png]]

**挤压变换的矩阵推导过程**
1. 由相似三角形得：![[Pasted image 20260321095744.png]]
$$
y' = \frac{n}{z}y, \space x' = \frac{n}{z}x
$$
2. 在齐次坐标中：
$$
\begin{pmatrix}
x \\ y \\ z \\ 1
\end{pmatrix}
\Rightarrow
\begin{pmatrix}
nx/z \\ ny/z \\ unknown \\ 1
\end{pmatrix}
\overbrace{==}^{\text{multi by z}}
\begin{pmatrix}
nx \\ ny \\ still \space unknown \\ z
\end{pmatrix}
$$
3. 那么挤压操作：
$$
M_{persp \to ortho}^{(4 \times 4)}
\begin{pmatrix}
x \\ y \\ z \\ 1
\end{pmatrix}
=
\begin{pmatrix}
nx \\ ny \\ unknown \\ z
\end{pmatrix}
$$
4. 那么显然：
$$
M_{persp \to ortho} = 
\begin{pmatrix}
n & 0 & 0 & 0 \\ 0 & n & 0 & 0 \\ ? & ? & ? & ? \\ 0 & 0 & 1 & 0
\end{pmatrix}
$$
5. 观察得任何在近处平面上的点坐标都不变：
$$
\begin{gather*}
M_{persp \to ortho}^{(4 \times 4)}
\begin{pmatrix}
x \\ y \\ z \\ 1
\end{pmatrix}
= 
\begin{pmatrix}
nx \\ ny \\ unknown \\ z
\end{pmatrix}
\xrightarrow{\text{replace z with n}}
M_{persp \to ortho}^{(4 \times 4)}
\begin{pmatrix}
x \\ y \\ n \\ 1
\end{pmatrix}
= 
\begin{pmatrix}
x \\ y \\ n \\ 1
\end{pmatrix}
\\
 =
\begin{pmatrix}
nx \\ ny \\ n^2 \\ n
\end{pmatrix}
= 
\begin{pmatrix}
nx \\ ny \\ unknown \\ z
\end{pmatrix}
\end{gather*}
$$
   由于 $n^2$ 和 $x, y$ 没有关系，所有 $M_{persp \to ortho}$ 的第三行一定是 $\text{(0 0 A B)}$
$$
\begin{pmatrix}
n & 0 & 0 & 0 \\
0 & n & 0 & 0 \\ 
0 & 0 & A & B \\
0 & 0 & 1 & 0
\end{pmatrix}
\begin{pmatrix}
x \\
y \\ 
n \\
1
\end{pmatrix}
= 
\begin{pmatrix}
nx \\
ny \\ 
n^2 \\
n
\end{pmatrix}
\Longrightarrow
\begin{pmatrix}
0 & 0 & A & B
\end{pmatrix}
\begin{pmatrix}
x \\ y \\ n \\ 1
\end{pmatrix}
= n ^ 2
\Longrightarrow
An + B = n^2
$$
6. 任何在远处平面上的点 Z 轴坐标都不变，且远处中心点变换前后都为 $(0, 0, f, 1)$
$$
\begin{gather*}
M_{persp \to ortho}^{(4 \times 4)}
\begin{pmatrix}
x \\ y \\ z \\ 1
\end{pmatrix}
= 
\begin{pmatrix}
nx \\ ny \\ unknown \\ z
\end{pmatrix}
\xrightarrow{(x,y,z,1)to(0,0,f,1)}
M_{persp \to ortho}^{(4 \times 4)}
\begin{pmatrix}
0 \\ 0 \\ f \\ 1
\end{pmatrix}
=
\begin{pmatrix}
0 \\ 0 \\ f \\ 1
\end{pmatrix}
\\
=
\begin{pmatrix}
0 \\ 0 \\ f^2 \\ f
\end{pmatrix}
=
\begin{pmatrix}
nx \\ ny \\ unknown \\ z
\end{pmatrix}
\end{gather*}
$$
   第三行为 $(0, 0, A, B)$ 时，可得到：
$$
\begin{pmatrix}
n & 0 & 0 & 0 \\
0 & n & 0 & 0 \\ 
0 & 0 & A & B \\
0 & 0 & 1 & 0
\end{pmatrix}
\begin{pmatrix}
0 \\
0 \\ 
f \\
1
\end{pmatrix}
= 
\begin{pmatrix}
0 \\
0 \\ 
f^2 \\
f
\end{pmatrix}
\Longrightarrow
\begin{pmatrix}
0 & 0 & A & B
\end{pmatrix}
\begin{pmatrix}
0 \\
0 \\ 
f \\
1
\end{pmatrix}
= 
f^2
\Longrightarrow
Af + B = f^2
$$
7. 联立 5、6 求解：
$$
\left\{
\begin{matrix}
An + B = n^2 \\
Af + B = f^2
\end{matrix}
\right.
\Longrightarrow
\left\{
\begin{matrix}
A = n + f \\
B = -nf
\end{matrix}
\right.
$$
8. 则 $M_{persp \to ortho}$：
$$
M_{persp \to ortho} = 
\begin{pmatrix}
n & 0 & 0 & 0 \\
0 & n & 0 & 0 \\ 
0 & 0 & n + f & -nf \\
0 & 0 & 1 & 0
\end{pmatrix}，
(\text{n 为近处平面的 Z 轴值，f 为远处平面的 Z 轴值})
$$

**最后，透视投影为 先挤压后正交投影**
$$
\begin{gather*}
M_{persp} = M_{ortho}M_{persp \to ortho}
\\
= 
\begin{bmatrix}
\frac{2}{r - l} & 0 & 0 & 0 \\ 
0 & \frac{2}{t - b} & 0 & 0 \\
0 & 0 & \frac{2}{n - f} & 0 \\
0 & 0 & 0 & 1
\end{bmatrix}
\begin{bmatrix}
1 & 0 & 0 & -\frac{r + l}{2} \\ 
0 & 1 & 0 & -\frac{t + b}{2} \\
0 & 0 & 1 & -\frac{n + f}{2} \\
0 & 0 & 0 & 1
\end{bmatrix}
\begin{bmatrix}
n & 0 & 0 & 0 \\
0 & n & 0 & 0 \\ 
0 & 0 & n + f & -nf \\
0 & 0 & 1 & 0
\end{bmatrix}
\end{gather*}
$$

> 在挤压的过程中，对于远近平面的中间的某一个点，经过变化后会被推向远处
> $a < b$
> 证明见：哔哩哔哩评论区
> ![[Pasted image 20260321111319.png]]

> 这里可以看出，经过透视投影之后，原来坐标其实会受到 z 的影响，因为透视投影最后一行为 (0, 0, 1, 0) 。所以还需要进行透视除法 $vec /= vec.w()$ ，这样远处 Z 大的在经过透视除法后其 X, Y 才会变得更小
> ![[Pasted image 20260614155543.png]]

**如何定义锥体中较近的面**
+ 垂直可视角度 field-of-view (fovY)
+ 宽高比 aspect ratio
![[Pasted image 20260322105413.png|625]]

**如何将 垂直可视角度和宽高比 转化为 锥体较近的面**
![[Pasted image 20260322105732.png|625]]
$$
\tan\frac{fovY}{2} = \frac{t}{|n|},
\space \space
aspect = \frac{r}{t}
$$
