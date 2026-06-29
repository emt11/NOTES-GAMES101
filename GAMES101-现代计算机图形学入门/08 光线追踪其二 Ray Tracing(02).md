## 1 辐射度量学
首先：为什么需要引入辐射度量学？
+ 在实现 Blinn-Phong 着色模型时，会设置一个数作为“光照强度”，这个数的真实物理意义我们并不清楚(其实是 Irradiance 辐照度)
+ Whitted-Style Ray Tracing 不是一个物理正确的结果，其中有很多简化；辐射度量学可以帮助我们进行物理准确的光照计算，它同样也是“路径追踪”的基础
+ 辐射度量学给出了一系列度量方法和单位描述光照
+ 辐射度量学精确的定义了光在空间中的属性：辐射通量(Radiant flux)、强度(intensity)、辐照度(intensity)、辐射度(radiance)

### 1.1 辐射度量学相关物理量
#### 1.1.1 Radiant energy & flux(Power)
+ **Radiant energy** 是光源辐射的能量（在图形学中很少使用这个概念）
$$ Q[J = Joule]$$
+ **Radiant flux(power)** 是单位时间内发射、反射、传输或接收的能量
$$ \Phi = \frac{dQ}{dt} [W = Watt] $$
其中 Flux 可以看作单位时间内通过传感器的光子的数量
![[Pasted image 20260628102008.png|425]]

#### 1.1.2 Radiant Intensity
+ **Radiant Intensity** 是点光源发出的每单位立体角的能量
$$ I(\omega) = \frac{d\Phi}{d\omega} [\frac{W}{sr}][\frac{lm}{sr} = cd = candela]$$
+ **Solid Angle** 立体角，是球体上一定区域面积与半径平方的比
	+ 立体角 $\Omega = \frac{A}{r^2}$ ，立体角最大是 $4\pi$ 
	  ![[Pasted image 20260628103116.png|163]]
+ **Differential Solid Angles** 单位立体角，是球体上单位面积与半径平方的比，可以用球面坐标的 $\theta$ 和 $\phi$ 的变化得到
$$ \begin{matrix} dA = (r \space d\theta)(r \space sin\theta \space d\phi), dw = \frac{dA}{r^2} = sin\theta \space d\theta  \\
\end{matrix}
$$
> 	其中 $r d\theta$ 是 $d\theta$ 对应的弧长
> 	其中 $r sin\theta \space d\theta$ 是 $d\phi$ 对应的弧长，它对应的圆是以 $rsin\theta$ 为半径的纬线圆 
	![[Pasted image 20260628103606.png|375]]

#### 1.1.3 Irradiance
+ **Irradiance** 是入射在表面点上的每单位面积的能量（光线和单位面积不垂直要投影到垂直的方向上去）
$$ E(x) = \frac{d\Phi(x)}{dA} [\frac{W}{m ^ 2}][\frac{lm}{m^2} = lux] $$
	![[Pasted image 20260628133608.png|350]]
+ 兰伯特余弦定理(Lambert’s Cosine Law)
  Irradiance 与光照方向和表面法线之间夹角的余弦成正比 ![[Pasted image 20260628110418.png]]
+ 更正：之前我们假设假设能量集中在一个球壳上，随着球壳的增大，光的强度会有一个距离平方的衰减，这里的衰减其实也是 Irradiance 在衰减
   $E = \frac{\Phi}{4\pi}$ 是半径为 1 的球面上，单位面积接收到的 irradiance ![[Pasted image 20260628110608.png]]

#### 1.1.4 Radiance
+ **Radiance** 是为了用来描述环境中光线在空间中传播的一些属性，是**每单位立体角，每单位投影面积**发射、反射、发射或接收的能量
$$ 
L(p, w) = \frac{d^2\Phi(p,w)}{d\omega \space dA \space cos\theta} [\frac{W}{sr\cdot m^2}] [\frac{cd}{m^2} = \frac{lm}{r^2\cdot m^2} = nit]
$$

	![[Pasted image 20260628133412.png|575]]

其中 radiance 是 irradiance 在某个方向 $d\omega$ 上的分量，如下图，整个半球范围内单位面积接收到的各个方向的 radiance 积分起来即为接收到的 irradiance 
![[Pasted image 20260628133757.png|650]]

## 2 双向反射分布函数 BRDF(Bidirectional Reflectance Distribution Function)
反射的过程可以理解为：一道光打在一个表面上，这个表面吸收了这道光的能量，随后又将一部分能量向不同方向辐射出去
那么我们考虑一个微小面积 $dA$ 
+ $dE(\omega_i)$ 表示从某个方向 $\omega_i$ 射入的 radiance ，这个 radiance 被表面吸收后得到 irradiance
+ $dL(x, \omega_r)$ 表示从表面向某个方向 $\omega_r$ 辐射出去的 radiance
![[Pasted image 20260628134840.png]]

**BRDF** 就是定义了对于某一方向上单位面积接收的 irradiance ，如何分配到各个方向上的 radiance 的函数，即（任何一个出射方向的 radiance）/（单位面积接收到的 irradiance）
$$
f_r(\omega_i \rightarrow \omega_r) = \frac{dL_r(\omega_r)}{dE_i(\omega_i)} = \frac{dL_r(\omega_r)}{L_i(\omega_i)\cos(\theta_i)d\omega_i} [\frac{1}{sr}]
$$
![[Pasted image 20260628140341.png]]
**BRDF** 描述了物体和光线之间的相互作用。如果是镜面反射，那么只有反射方向上会有能量，其他所有方向上都没有能量；如果是漫反射，这个进来的能量会被均等的分布到各个方向上，即 BRDF 定义了材质

## 3 反射方程 ==> 渲染方程 The Reflection Equation ==> The Rendering Equation
### 3.1 反射方程
BRDF 描述了某个入射方向上的 $w_i$ 的微小 irradiance $dE_i(\omega_i)$  会被表面材质反射成多少某一出射方向 $\omega_r$ 上的 radiance $dL_r(\omega_r)$ ，实际上反射的过程中某一个点可以接收各个入射方向的 irradiance ，那么就需要将每个方向上的 irradiance 积分起来，计算贡献和
![[Pasted image 20260628141843.png]]
$$
L_r(p, \omega_r) = \int_{H^2}^{} f_r(p, \omega_i \rightarrow \omega_r)L_i(p, \omega_i)cos\theta_i d\omega_i
$$

### 3.2 渲染方程
反射方程本身是递归的：某个表面点 $x$ 接收到的入射 radiance 不一定只来自光源，也可能来自其他表面点 $x′$ 的出射 radiance。而某个表面点的 radiance 当然也包含这个表面点的自发光，因此渲染方程将当前点的出射 radiance 写成两部分：当前点自身的 emission $L_e(p, \omega_o)$ ，以及当前点对所有入射方向 radiance 的反射积分
$$
\newcommand{\w}{\omega}
L_o(p, \w_o) = L_e(p, \w_o) + \int_{\Omega^+}^{} L_i(p, \w_i) f_r(p, \w_i, \w_o) (n \cdot \w_i) d\w_i
$$

## 4 深入理解渲染方程
### 4.1 渲染方程
+ 如果有一个点光源，则 反射光 = 自发光 + 入射光 * BRDF * 入射光与法线夹角的余弦 ![[Pasted image 20260628143221.png]]
+ 如果有多个点光源，则多个点光源的影响结果需要累加起来 ![[Pasted image 20260628143402.png]]
+ 如果有面光源，可以将面光源理解为点光源的集合，所以可以改写为积分形式 ![[Pasted image 20260628143452.png]]
+ 如果还有其他物体反射的光，我们可以把其他物体的反射面当作光源，可以改写成一个递归的过程 ![[Pasted image 20260628143629.png]]
> The Rendering Equation (Kajiya 1986) 论文配图
> ![[Pasted image 20260628144436.png|400]]

### 4.2 全局光照 Global illumination
- 渲染方程（是标准的第二类 Fredholm 积分方程）![[Pasted image 20260628144627.png]]
- 通过将位置 $x$ 和角度 $\omega$ 概括成 $u, v$ ，对渲染方程进行简化 ![[Pasted image 20260628144823.png|450]]
- 写成算子的形式，进一步简化           ![[Pasted image 20260628144832.png|400]]
- 利用泰勒展开，求出 $L$ ![[Pasted image 20260628144947.png|575]]
- 渲染方程被拆解成以光的弹射次数为区分的很多项。用渲染方程来理解光栅化，光栅化能做的只有自发光与直接光照；全局光照是经过了多次反射后的结果，即把表示间接光照的项也加起来 ![[Pasted image 20260628145029.png|600]]
+ 光线经过多次弹射后结果会越来越亮，最终会收敛（第四次弹射时上方灯亮了，是因为这里玻璃时双层的，光线需要两次弹射进入两次弹射射出才能到达相机） ![[Pasted image 20260628145139.png]]

## 5 简单的概率论回顾 Probability Review
### 5.1 离散型随机变量 $X$
+ 概率密度函数(probability density function (PDF)) $X \sim p(x)$
+ 概率：对于 n 个概率是 $p_i$ 的离散变量 $x_i$ ，其中 $p_i \leq 0 \text{ 且 } \sum_{i = 1}^{n}p_i = 1$，例如一个骰子，有 ![[Pasted image 20260628150408.png|675]]
+ 期望 $E[X]$：反复从随机分布中采样后得到的平均值，具体取值：（值 * 概率） ![[Pasted image 20260628150542.png]]

### 5.2 连续型随机变量
+ 连续型随机变量的概率密度函数 PDF 需要取非常小的一段长方形来作为近似
	![[Pasted image 20260628150943.png]]
+ 概率、期望$E[X]$ ，同离散型随机变量：（值 * 概率） ![[Pasted image 20260628150955.png]]
+ 随机变量的函数，定义如下
$$
X \sim p(x), Y = f(X) 
$$
+ 随机变量函数的期望
$$ E[Y] = E[f(X)] = \int f(x)p(x) dx $$


## 6 蒙特卡洛积分 Monte Carlo Integration
### 6.1 Why 为什么学习蒙特卡洛积分
蒙特卡洛积分可以用来计算复杂定积分的值

### 6.2 What 什么是蒙特卡洛积分
+ 举一个例子：如果在定义域区间 (a, b) 内随机取一点 x ，得到 f(x) 的值，则我们可以用一个高为 f(x) ，宽为 ab 的一个长方形面积来估计曲线下的围出的面积。并在 (a, b) 区间采样，求长方形的平均值来作为一个近似的积分值
+ 蒙特卡洛积分是一种通过随机采样然后平均函数值来估计整个函数积分的方法
![[Pasted image 20260628152522.png]]

### 6.3 How 如何进行蒙特卡洛积分
+ 定义一个函数 $f(x)$ ，用一定概率密度在 $(a, b)$ 区间中采样，估计其在定义域内的积分 ![[Pasted image 20260628152655.png]]
+ 一种特殊情况是概率密度函数 PDF 为常数 C ，即在函数定义域内均匀采样，这样得到的蒙特卡洛积分的形式很符合直觉 ![[Pasted image 20260628153407.png]]![[Pasted image 20260628153514.png]]
+ 对于不同采样频率，更通用的形式：
$$
\int f(x)dx = \frac{1}{N} \sum_{i = 1}^{N} \frac{f(X_i)}{p(X_i)} X_i \sim p(x)
$$
- 蒙特卡洛积分采样的样本越大（N越大），越准确

## 7 路径追踪 Path Tracing
### 7.1 Whitted-Style Ray Tracing 的问题
Whitted-Style Ray Tracing 会在光线打到光滑表面会进行镜面反射，打到漫反射表面会停止
+ Whitted-Style Ray Tracing 无法做出 Glossy 的材质（不是完全的镜面反射）![[Pasted image 20260628153915.png]]
+ Whitted-Style Ray Tracing 不考虑漫反射的效果，但实际上光线也会在漫反射表面弹射，如右图中有 Color Bleeding 效果，而左图没有 ![[Pasted image 20260628153947.png]]
+ Whitted-Style Ray Tracing 是错的，渲染方程是正确的，但是渲染方程的求解涉及到了半球上的积分和递归运算

### 7.2 求解渲染方程
#### 7.2.1 使用蒙特卡洛积分求解渲染方程
+ 假设有一个有面光源、着色点、其他物体挡住光的的场景，首先我们先考虑直接光照 ![[Pasted image 20260628154442.png]]
+ 在不考虑自发光的情况下，我们可以忽略渲染方程中的自发光项（仍然默认向量方向朝外）
$$
\newcommand{\w}{\omega}
L_o(p, \w_o) = \int_{\Omega^+}^{} L_i(p, \w_i) f_r(p, \w_i, \w_o) (n \cdot \w_i) d\w_i
$$
+ 我们想要计算 p 点到相机的 Radiance ，由于这里渲染方程的形式是积分的形式，我们可以使用蒙特卡洛积分进行求解，最简单的我们可以假设在半球面均匀的采样，则 PDF 值 $p(\omega_i) = \frac{1}{2\pi}$ （这里是因为半球面积是 $2\pi$） ![[Pasted image 20260628155317.png]]
+ 使用蒙特卡洛积分求解渲染方程（这里上面的 p 是 p 点，下面的 p 是 PDF 
$$
newcommand{\w}{\omega}
L_o(p, \w) \approx \frac{1}{N} \sum_{i = 1}^{N} 
\frac{L_i(p, u_i) f_r(p, \w_i, \w_o) (n \cdot \w_i)}{p(\w_i)}
$$
对应的伪代码：
```c++
shade(p, wo)                                    // 着色点p，沿wo方向到达观测点
	Randomly choose N directions wi~pdf         // 以某一 pdf 采样 N 个方向的光线
	Lo = 0.0                                      
	For each wi                               
		Trace a ray r(p,wi)
		If ray r hit the light                  // 如果打出的光线打到了光源，则利用渲染方程着色
			Lo += (1 / N) * L_i * f_r cosine / pdf(wi) // 渲染方程
	Return Lo                                    
```

+ 考虑全局光照，从 P 点连线没有直接射向光源，而是结果 Q 反射到光源。因此我们可以递归计算 Q 沿该方向返回的 radiance ![[Pasted image 20260628155755.png]]
```c++
shade(p, wo)                                    // 着色点p，沿wo方向到达观测点
	Randomly choose N directions wi~pdf         // 以某一 pdf 采样 N 个方向的光线
	Lo = 0.0                                      
	For each wi                               
		Trace a ray r(p,wi)
		If ray r hit the light                  // 如果打出的光线打到了光源，则利用渲染方程着色
			Lo += (1 / N) * L_i * f_r cosine / pdf(wi) // 渲染方程
		Else If ray r git an object at q        // 递归计算 q 点发射给 p 的 radiance
			Lo += (1 / N) * shade(q, -wi) * f_r * cosine / pdf(wi)
	Return Lo    
```

但目前仍然存在几个问题

#### 7.2.2 问题一：光线数量问题
+ 在之前我们使用采样计算蒙特卡洛积分，但是如果采样数 N 过大时，多次反射之后数据集会爆炸 ![[Pasted image 20260628160711.png]]
+ 要想处理这个问题，只能将 N 设为 1 
```c++
shade(p, wo)                                    // 着色点p，沿wo方向到达观测点
	Randomly choose N directions wi~pdf         // 以某一 pdf 采样 N 个方向的光线
	Lo = 0.0                                      
	For each wi                               
		Trace a ray r(p,wi)
		If ray r hit the light                  // 如果打出的光线打到了光源，则利用渲染方程着色
			Lo += L_i * f_r cosine / pdf(wi) // 渲染方程
		Else If ray r git an object at q        // 递归计算 q 点发射给 p 的 radiance
			Lo += shade(q, -wi) * f_r * cosine / pdf(wi)
	Return Lo    
```

+ 但是 N = 1 误差过大，这里只需要同一个像素多取几个路径，然后再平均他们的 radiance 就可以了
```c++
ray_generation(camPos,pixel)                                  
	Uniformly choose N sample positions within the pixel      // 在像素内均匀的取 N 个不同的位置
	pixel_radiance 0.0
	For each sample in the pixel
		Shoot a ray r(camPos, cam_to_sample)                  
		If ray r hit the scene at p                           
			pixel_radiance += 1 / N * shade(p, sample_to_cam) 
	Return pixel_radiance
```

#### 7.2.3 问题二：递归没有终止条件
真实世界中光线确实不会停止，限制光的弹射次数会造成能量损失，而没有终止条件的无限的递归无法计算
为了解决上述问题，引入俄罗斯轮盘赌的概念（决定一定的概率停止光线往下追踪）
	![[Pasted image 20260628164118.png]]
首先，我们虽然会在中间停掉追踪，但我们期望最终得到的积分结果还 $L_o$ ，我们假设以一定概率 $P (0 < P < 1)$ 往外打一条光线，着色结果是 $\frac{L_o}{P}$ ，同时有 $(1 - P)$ 的概率不会打出光线，着色结果是 0 
```c++
shade(p,wo)                                        

   Manually specify a probability P_RR
   Randomly select ksi in a uniform dist. in [0, 1]
   If (ksi > P_RR) return 0.0;                     // [0,1]取一个数，大于规定概率则不能跳出递归

   Randomly choose ONE directions wi~pdf(w)        // 这里只采样一个方向
   Trace a ray r(p,wi)
   If ray r hit the light                          // 如果打出的光线打到了光源，则利用渲染方程着色
	   Return L_i * f_r cosine / pdf(wi) / P_RR
   Else If ray r git an object at q                // 如果打出的光线打到了q点，计算p点看q点的着色再作为光源着色p点
	   Return shade(q, -wi) * f_r * cosine / pdf(wi) / P_RR
```

### 7.3 对光源采样
我们已经得到了一个正确的 Path Tracing 的方法，但是效率并不高 ![[Pasted image 20260628164529.png]]
光源较小的时候，如果使用之前的方法对着色点半球范围内的光线均匀采样，则很多光线就会被浪费掉，能打到光源的很少 ![[Pasted image 20260628164607.png]]
蒙特卡洛积分允许我们用任何方式采样，所以我们可以只对光源采样，这样光线就不会浪费  
假设我们在光源上采样，则有 $pdf = \frac{1}{A}$
但是渲染方程的积分是定义在立体角上的，而蒙特卡洛积分要求积分和采样在一个域上，现在是在光源的面积采样，在着色点半球积分，并不在一个域上
所以我们需要进行换域，$d\omega$ 和 $dA$ 的关系是：
	三维里，球面小面积和半径关系是：$dA = r^2 * d\omega$ ，考虑到角度和半径，则有：下图公式
![[Pasted image 20260628164814.png]]
代入到原公式中，有：
![[Pasted image 20260628170320.png]]

至此，我们可以对光源进行采样
此时我们在着色时应该考虑两部分
1. 来自光源的贡献直接采样光源，不需要使用俄罗斯轮盘（因为光源就是终点）
2. 来自其他表面的间接光照，需要用俄罗斯轮盘
```c++
shade(p, wo)
	# Contribution from the light source.
	L_dir = 0.0
	Uniformly sample the light at x' (pdf light = 1 / A)
	L_dir = L_i * f_r * cosθ * cosθ / |x' - p| ^ 2 / pdf_light
	
	# Contribution from other reflectors.
	L_indir = 0.0
	Test Russian Roulette with probability P_RR
	Uniformly sample the hemisphere toward wi (pdf_hemi 1 / 2pi)
	Trace a ray r(p, wi)
	If ray r hit a non-emitting object at q
		L_indir = shade(q, -wi) * f_r * cos θ / pdf_hemi / P_RR
		
	Return L_dir + L_indir
```

+ 一个小问题：我们还需要考虑光源与物体之间是否有遮挡，在光源和物体间连线，判定中间有没有物体遮挡
```c++
# Contribution from the light source.
L_dir = 0.0
Uniformly sample the light at x' (pdf light = 1 / A)

Shoot a ray from p to x'
If the ray is not blocked in the middle

	L_dir = ...
```

## 8 其他 Some Side Notes
+ Path tracing (PT) is indeed difficult 
	+ Consider it the most challenging in undergrad CS 
	+ Why: physics, probability, calculus, coding
	+ Learning PT will help you understand deeper in these

+ Is it still “Introductory”?
	+ Not really, but it’s “modern" :) 
	+ And so learning it will be rewarding also because …

+ 路径追踪的正确吗？如图 ![[Pasted image 20260628171009.png]]

+ 早期的光线追踪概念和现在的光线追踪概念
	+ 早期的光线追踪说的就是Whitted-Style Ray Tracing
	+ 现在的光线追踪是所有光线传播方法的大集合
		+ 单向/双向路径追踪 (Unidirectional & bidirectional) path tracing
		+ 光子映射 Photon mapping
		+ Metropolis light transport
		+ VCM / UPBP

+ 我们没有涉及/不会涉及的内容
	+ 怎样让方向均匀分布在半球上，怎样采样一个函数
	+ 蒙特卡洛积分可以用任意的 pdf ，选择什么样的 pdf 是最好的（重要性采样理论）
	+ 随机数有质量的好坏（低差异序列）
	+ 采样光源和采样半球两者可以结合起来
	+ 为什么一个像素所有路径的 radiance 平均起来就是这个像素的 radiance 
	+ radiance 和像素的颜色不是线性对应的，需要经过伽马矫正？