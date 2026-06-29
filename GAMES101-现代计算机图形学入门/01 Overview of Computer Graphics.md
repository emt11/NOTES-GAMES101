## 1 What is Computer Graphics?
+ 使用计算机合成与操作视觉信息 / The use of computers to synthesize and manipulate visual information

## 2 Why study Computer Graphics?
### 2.1 Application
+ Video Games （写实、风格化渲染）
  ![[Pasted image 20260302204049.png]]
+ Movies （特效）
  ![[Pasted image 20260302204104.png]]
+ Animations （疯狂动物城中对毛发的模拟）
  ![[Pasted image 20260302204121.png]]
+ Design （CAD、在虚拟环境中预览视觉效果：汽车）
+ Visualization 
+ Virtual Reality
+ Augmented Reality
+ Digital Illustration
+ Simulation
+ Graphical User Interfaces (GUI)
+ 字体 / Typography

## 2.2 Why Study Computer Graphics?
### 2.2.1 Fundamental Intellectual Challenges
- 创造并与现实的虚拟世界互动 / Creates and interacts with realistic virtual world
- 需要理解各种物理世界的各个方面 / Requires understanding of all aspects of physical world
- 新的计算方法、显示方法、科技进步 / New computing methods, displays, technologies

### 2.2.2 Technical Challenges
- （透视）投影、曲线、曲面的数学知识 Math of (perspective) projections, curves, surfaces
- 光线和阴影的物理学 Physics of lighting and shading
- 在三维中表现/操作形状 Representing / operating shapes in 3D
- 动画/模拟 Animation / simulation
- 本课程不包括硬件编程教学 ~~3D graphics software programming and hardware~~

## 3 Course Topics
四大模块：
+ 光栅化 Rasterization
+ 曲线和网格 Curves and meshes
+ 光线追踪 Ray Tracing
+ 动画 / 模拟 Animation/Simulation

### 3.1 光栅化 Rasterization
+ 将几何图元投影到屏幕上
+ 将投影图元分解为片段（像素）
+ 目前游戏的黄金标准（实时应用）

![[Pasted image 20260302205632.png]]

### 3.2 曲线和网格 Curves and meshes
+ 如何在计算机图形学中表示几何图形（曲面、表面细分时保持拓扑结构）

![[Pasted image 20260302205802.png]]

### 3.3 光线追踪 Ray Tracing
+ 从相机出发向每个像素发射射线（计算交点和阴影；继续反射光线直到击中光源）
+ 动画/电影的黄金标准（离线应用）目前已经有了实时光线追踪的算法

![[Pasted image 20260302205921.png]]

### 3.4 动画 / 模拟 Animation/Simulation
- 关键帧动画 Key frame Animation
- 质量弹簧系统 Mass-spring System

![[Pasted image 20260302210255.png]]

### 3.5 计算机图形学与计算机视觉的关系
+ 计算机图形学是从三维模型生成图像（渲染）或三维空间中的模拟（模拟）
+ 计算机视觉是通过图像语义分割进行建模或依赖深度学习由图像生成其他图像

![[Pasted image 20260302210427.png]]

> No clear boundaries

## 4 References
+ 推荐虎书
  Steve Marschner and Peter Shirley, "Fundamentals of Computer Graphics", 3rd or later edition.

