> A Swift and Brutal Introduction to Linear Algebra

## 1 Vectors
向量 $\vec{a} \space or \space \mathbf{a}$ 只有方向和距离，没有起始结束点
![[Pasted image 20260306171536.png|#pic_left|450]]

### 1.1 Vector Normalization
+ Magnitude (length) of a vector written as $\left\| \hat{a} \right\|$
+ Unit vector
	+ A vector with Magnitude of 1
	+ Finding the unit vector of a vector(normalization): $\hat{a} = \vec{a}/\left\|\vec{a}\right\|$

### 1.2 Vector Addition 
+ 几何理解 Geometrically（平行四边形法则和三角形法则）
  ![[Pasted image 20260306173630.png|#pic_left|450]]
+ 代数 Algebraically
  我们可以使用笛卡尔坐标（Cartesian Coordinates）来表示一个向量
  ![[Pasted image 20260306174238.png|#pic_left|450]]
$$
A = \binom{4}{3}，\space\space\space A^T = (4, 3)，\space\space\space A = \sqrt{x^2 + y^2}
$$

### 1.3 Vector Multiplication
#### 1.3.1 点积 Dot product
+ 定义和使用
$$
\begin{gather*}
\vec{a} \cdot \vec{b} = \left\| \vec{a} \right\| \left\| \vec{b} \right\| \cos\theta 
,\space\space\space
\cos\theta = \tfrac{\vec{a} \cdot \vec{b}}{\left\| \vec{a} \right\| \left\| \vec{b} \right\|}
\\
对于单位向量：\cos\theta = \vec{a} \cdot \vec{b}
\end{gather*}
$$
+ 性质
$$
\def\va{\vec{a}}
\def\vb{\vec{b}}
\def\vc{\vec{c}}
\begin{aligned}
&\va \cdot \vb = \vb \cdot \va\\
&\va \cdot (\vb \cdot \vc) = \va\ \cdot \vb + \va \cdot \vc\\
&(k\va) \cdot \vb = \va \cdot (k\vb) = k(\va \cdot \vb)
\end{aligned}
$$
+ 笛卡尔坐标系下的点积 Dot Product in Cartesian Coordinates
  In2D:
$$
% 定义宏
\def\va{\vec{a}}
\def\vb{\vec{b}}
\begin{aligned}
&\va \cdot \vb = \binom{x_a}{y_a} \cdot \binom{x_b}{y_b} = x_a x_b + y_a y_b
\end{aligned}
$$
  In3D:
$$
% 定义宏
\def\va{\vec{a}}
\def\vb{\vec{b}}
\begin{aligned}
&\va \cdot \vb = 
\begin{pmatrix} x_a \\ y_a \\ z_a \end{pmatrix} 
\cdot 
\begin{pmatrix} x_b \\ y_b \\ z_b \end{pmatrix} 
= x_a x_b + y_a y_b + z_a z_b
\end{aligned}
$$
+ **图形学中的点积 Dot Product for Graphics**
	+ 求两个向量之间的角度（例如光源在表面之间角度的余弦）
	+ 计算一个向量在另一个向量上的投影 
	  ![[Pasted image 20260306210059.png|#pic_left|250]]
$$
\def\vb{\vec{b}}
\vb_\bot = k \hat{a}\\
\space
(k = \left\| \vb_\bot \right\| = \left\| \vb \right\| \cos \theta)
$$
	+ 分解向量
	  ![[Pasted image 20260306211605.png|325|#pic_left|300]]
	+ 测量两个方向有多接近 && 判断朝向
	  $\vec{a} \space 与 \space \vec{b}$ 越接近，点积越接近1
	  ![[Pasted image 20260306211716.png|#pic_left|300]]
> 具体应用：如光线照射到金属表面，只有视线在金属折射线范围内才能看到金属的光泽（方向接近）

#### 1.3.2 叉乘 Cross product
+ 定义和使用
	+ 叉乘结果向量垂直于两个向量
	+ 叉乘结果由右手法则确定
	+ 可以用来构建三维坐标系
$$
\left\| \vec{a} \times \vec{b} \right\| = \left\| \vec{a} \right\|\left\| \vec{b} \right\| \cos\theta
$$
  ![[Pasted image 20260311185836.png|#pic_left|200]]
  + 性质
$$
\def\va{\vec{a}}
\def\vb{\vec{b}}
\def\vc{\vec{c}}
\begin{aligned}
&\va \times \vb = -\vb \times \va\\
&\va \times \va = \vec{0}\\
&\va \times (\vb + \vc) = \va \times \vc + \vb \times \vc\\
&\va \times (k\vb) = k(\va \times \vb)
\end{aligned}
$$
+ 笛卡尔坐标系下的叉乘 Dot Product in Cartesian Coordinates
$$
\def\va{\vec{a}}
\def\vb{\vec{b}}
\def\vc{\vec{c}}
\begin{aligned}
\va \times \vb = 
\begin{pmatrix}
 y_a z_b - y_b z_a \\ 
 z_a x_b - x_a z_b \\ 
 x_a y_b - y_a x_b
\end{pmatrix}
\end{aligned}
$$
  Later in this lecture
$$
\def\va{\vec{a}}
\def\vb{\vec{b}}
\va \times \vb = A^*b = 
\begin{pmatrix}
0 & -z_a & y_a \\
z_a & 0 & -x_a \\
-y_a & x_a & 0
\end{pmatrix}
\begin{pmatrix}
x_b\\
y_b\\
z_b
\end{pmatrix}
$$
+ **图形学中的叉乘 Cross Product for Graphics**
	+ 判断左右 Determine left/right
	+ 判断内外 Determine inside/outside
	  ![[Pasted image 20260311192332.png]]
	> 如左图所示，应用右手法则可得 $\vec{a} \times \vec{b} > 0 \Longrightarrow \vec{b}$ 在 $\vec{a}$ 左侧
	> 如右图所示，当 $\overrightarrow{AB} \times \overrightarrow{AP},\space \overrightarrow{BC} \times \overrightarrow{BP} ,\space \overrightarrow{CA} \times \overrightarrow{CP}$ 都同号时，P 在 ABC 内侧

#### 1.3.3 正交坐标系 Orthonormal Bases / Coordinate Frames
如果由三个向量满足 
$$
\def\vu{\vec{u}}
\def\vv{\vec{v}}
\def\vw{\vec{w}}
\def\norm#1{\left\| #1 \right\|}
\begin{aligned}
&\norm u  = \norm w = \norm v = 1\\
&\vu \cdot \vv = \vv \cdot \vw = \vu \cdot \vw = 0\\
&\vw = \vu \cdot \vv \space (right-handed)
\end{aligned}
$$
则
$$
\def\vp{\vec{p}}
\def\vu{\vec{u}}
\def\vv{\vec{v}}
\def\vw{\vec{w}}
\vp = (\vp \cdot \vu)\vu + (\vp \cdot \vv)\vv + (\vp \cdot \vw)\vw
$$
## 2 Matrix
在图形学中，矩阵常用来代表 **变换**
### 2.1 What is matrix
+ 一个数字数组（m * n, m 行 n 列）
$$
\begin{pmatrix}
1 & 2 \\
2 & 1 \\ 
3 & 1
\end{pmatrix}
$$

### 2.2 Matrix-Matrix Multiplication
- A的列数必须等于B的行数才能进行乘法运算 The number of columns in A must equal to the number of rows in B
$$
(M \times N)(N \times P) = (M \times P)
$$
+ 乘积中元素 (i, j) 是 A 中第 i 行和 B 中第 j 列的点积结果
$$
\begin{pmatrix}
1 & 2 \\
2 & 1 \\ 
3 & 1
\end{pmatrix}
\times
\begin{pmatrix}
1 & 2 \\
2 & 1 \\ 
\end{pmatrix} = 
\begin{pmatrix}
5 & 4 \\
4 & 5 \\ 
5 & 7
\end{pmatrix}
$$

### 2.3 Properties
- 没有交换律  Non-commutative(AB and BA are different in general)
- 有结合律和分配律 Associative and distributive
$$
(AB)C = A(BC), \space A(B+C) = AB + AC
$$

### 2.4 Matrix-Vector Multiplication
- 将向量视为列矩阵 Treat vector as a column matrix (m×1)
- 是变换的核心 Key for transforming points
$$
 关于 y 轴的变换：
 \begin{pmatrix}
-1 & 0 \\
0 & 1
\end{pmatrix}
\begin{pmatrix}
x\\y
\end{pmatrix} =
\begin{pmatrix}
-x\\y
\end{pmatrix}
$$

### 2.5 Transpose of a Matrix
- 将矩阵的行和列互换 Switch rows and columns (i, j -> j, i)
$$
\begin{pmatrix}
1 & 2 \\
2 & 1 \\ 
3 & 1
\end{pmatrix} ^ T
= 
\begin{pmatrix}
1 & 2 & 3 \\
2 & 1 & 1
\end{pmatrix}
$$
+ 转置的性质 Property
$$ (AB)^T = B^T A^T $$

### 2.6 Identity Matrix and Inverses
+ 单位矩阵 Identity Matrix
  对角线都为 1 
$$ 
I_{3\times3} = 
\begin{pmatrix}
1 & 0 & 0 \\
0 & 1 & 0\\ 
0 & 0 & 1
\end{pmatrix}
$$
- 逆矩阵 Inverses
	- 两个矩阵乘积为单位矩阵，则两个矩阵互逆
$$ AA^{-1} = A^{-1}A = I $$
	- 逆矩阵的性质
$$ (AB)^{-1} = B^{-1}A^{-1} $$

# 3 Vector multiplication in Matrix form
### 3.1 Dot product
$$
% 定义宏
\def\va{\vec{a}}
\def\vb{\vec{b}}
\begin{aligned}
&\va \cdot \vb = 
\begin{pmatrix} x_a \\ y_a \\ z_a \end{pmatrix} 
\cdot 
\begin{pmatrix} x_b \\ y_b \\ z_b \end{pmatrix} 
= x_a x_b + y_a y_b + z_a z_b
\end{aligned}
$$

###  3.2 Cross product
$$
\def\va{\vec{a}}
\def\vb{\vec{b}}
\va \times \vb = A^*b = 
\begin{pmatrix}
0 & -z_a & y_a \\
z_a & 0 & -x_a \\
-y_a & x_a & 0
\end{pmatrix}
\begin{pmatrix}
x_b\\
y_b\\
z_b
\end{pmatrix}
$$
