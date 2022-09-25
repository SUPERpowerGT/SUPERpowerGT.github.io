---
layout: default
title: shader学习笔记
description: 记录学习shader入门精要的笔记
---

## shader 入门精要

## 1 基础篇

### 1.1 初识shader

#### 1.1.1 三大浪漫

本节主要讲述图形学的学习不同于语言的学习，而是应当在基于渲染管线的逻辑架构上学习

万万没想到这个说法是指编译原理，操作系统以及图形学（孩子操作系统只考了72，心累）

#### 1.1.2 本书结构

本节介绍书本目录以及对应章节内容。（基本按照原理加实际操作顺序来讲解）

### 1.2 渲染管线

#### 1.2.1 综述

1.流水线

提高单位时间的生产量

2.渲染流水线

将流水线运用到计算机图形学渲染当中：将三维物体渲染出二维图像（CPU+GPU）

三阶段：应用阶段—几何阶段—光栅化阶段

应用阶段：开发者主导，通常由CPU实现，包含三个任务：准备场景数据（包括：MVP，以及光源材质等），进行粗粒度剔除（z_brush：深度筛选），设置渲染状态。这一阶段输出渲染所需的几何信息，可以理解为输出渲染图元（点，线，面）

几何阶段：通常由GPU实现，进行逐顶点，多边形的操作，重要任务是将应用阶段的顶点坐标变换到空间当中，给光栅处理器处理，输出屏幕空间的二维坐标，深度值，着色等信息，传递给下一阶段

光栅化阶段：渲染最终图像，在GPU实现，插值，渲染处理等

#### 1.2.2 CPU 和 GPU 的通信

CPU：应用阶段：

- 把数据加载到显存当中：

  从硬盘（HDD）加载到内存（RAM），网格纹理数据加载到显卡上的存储空间—显存（VRAM）。

  **原因：显卡对显存（VRAM）的访问速度快，且大多数显卡不能访问内存（RAM）。**

  当所有数据加载好后，内存（RAM）的数据可以移除（但存在数据CPU需要访问内存：当要进行碰撞检测时候）。

  通过CPU设置渲染状态，指导GPU进行渲染

- 设置渲染状态

  一般是指场景的网格是如何被渲染的：使用哪个顶点着色器，片元着色器等，光源属性，材质等

- 调用draw call

  开始渲染：由CPU发起渲染命令，接收方是GPU，通过材质纹理等信息进行渲染。

#### 1.2.3 GPU流水线

GPU渲染过程就是流水线

 几何阶段：                           

顶点数据—顶点着色器（完全可编程：空间变换，顶点着色等）—曲面细分着色器（可选：细分图元）—几何着色器（可选：逐图元着色，或产生图元）—裁剪（可配置：将不在视线内的顶点裁剪，以及面片）—屏幕映射（可配置编程：实现坐标变换到屏幕坐标系当中）—

- 顶点着色器

  处理单位为顶点，CPU每输入一个顶点，都会调用一次顶点着色器。**注意：顶点着色器不可以创造删除顶点以及不能得知顶点之间的关系！！！**具有较高的独立性，这也意味着GPU处理速度会很快。

  主要工作：坐标变换：把顶点坐标从模型空间转换到齐次裁剪空间，通过透视除法，得到归一化设备坐标（NDC）一般xyz是（-1，1）的区间

  ​					逐顶点光照：

- 裁剪

  将视线范围外的顶点裁剪（不可以编程）

- 屏幕映射

  三位坐标转换到二维坐标系下（不同编辑器窗口坐标的原点位置可能不同）

光栅化阶段：

三角设置（固定函数）—三角形遍历（固定函数）—片元着色器（可编程：逐片元着色）—逐片元操作（不是可编程，但是可配置：修改颜色，深度缓冲，混合等）—屏幕图像

- 三角形设置

  光栅化的第一步，我们要清楚光栅化的两个重要目标：计算图元覆盖那些像素，并为这些像素计算它们的颜色

  屏幕映射结束后，我们得到图元像素点的相关信息（位置，深度，法线方向等），为了得到整个三角形网络的情况，我们需要计算各个顶点之间组成的三角形边上顶点的像素信息

- 三角形遍历

  检查是否所有的像素都被一个三角形网格覆盖，被覆盖的则生成片元。这个阶段也成为扫描变换，片元（不仅仅是指像素，同时包括像素的其他信息）

- 片元着色器

  可编程着色器阶段

  将插值数据输出颜色值：采用多种重要的渲染技术，如纹理采样

- 逐片元操作（或者输出合并阶段）：高度可配置

  1. 决定片元的可见性：深度测试，模板测试（渲染阴影或者轮廓渲染）等
  2. 将颜色合并（混合）
  3. 双置缓冲（后置缓冲：加载，前置缓冲：将后置缓冲的图像交换到前置呈现在屏幕上，实现动画效果） 

#### 1.2.4 对一些概念定义的理解

1. 什么是OPENGL/DirectX

   简单理解为是图相应用编程的接口

   显卡：GPU+显存（VRAM）

2. 什么是HLSL,GLSL,CG

   简单理解为高级渲染编程语言，高级是相对于汇编语言来说的，也可以称作为中间语言

3. 如何减少Draw call的开销？

   避免使用大量很小的网络

   避免使用过多的材质

#### 1.2.5 什么是shader

GPU流水线上的一些可以高度编程的阶段

存在一些特定的着色器，顶点，片元等

通过着色器可以控制顶点的渲染

### 1.3 unity shader基础

在没有编辑器的帮助下，我们需要自己设置相关条件来帮助我们实现图像的渲染

现在我们开始学习在unity的帮助下，如何渲染

#### 1.3.1 Unity Shader概述

1. 材质和shader

   - 创建材质
   - 创建shader，并将shader和材质绑定
   - 把材质和物体绑定
   - 在材质面板调整shader属性，得到满意效果

   shader来定义相关代码，属性，指令，材质来让我们调节这些属性

2. 材质

   材质要结合物体的mesh（网格）或者particle systems（粒子系统）组件来工作

   ![image-20220714102952652](C:\Users\XZY\AppData\Roaming\Typora\typora-user-images\image-20220714102952652.png)

   

3. shader

   unity shader和shader是存在区别的，unity中shader包括五类：

   - standard surface shader

     包含标准光照模型的表面着色器模板

   - unlit shader

     不包含光照的基本的顶点片元着色器

   - image shader

     为实现屏幕后处理效果提供一个模板

   - compute shader

     产生一个特殊文件，利用GPU来并行性进行一些计算（不在本书考虑内）

   我们通过选择

   ![image-20220714104731451](C:\Users\XZY\AppData\Roaming\Typora\typora-user-images\image-20220714104731451.png)

   来选择合适的shader模板，对应不同的shader参数调节

   shader其实就是文本编辑，我们也可以设置对应默认的纹理，和标签设置等

   show generated code：进行代码编写

   compile（编译） and show code：可以选择不同接口来编译检测是否存在问题

   渲染队列，批处理，属性列表等信息

#### 1.3.2 ShaderLab

shaderlab：语言

基本结构：

```shaderlab
Shader "ShaderName"{
    Properties{
    //属性
    }
    SubShader{
    //显卡A使用的子着色器
    }
    SubShader{
    //显卡B使用的子着色器
    }
    Fallback"VertexLit"
}
```



#### 1.3.3 Unity Shader的结构

1. 起个名字

   Shader "ShaderName"{

   ​	}

2. 材质和unity shader桥梁properties

   properties的意义是在于用shaderlab定义GUI中面板的属性

   Properties{
       //属性
       }

3. 重量级成员：SubShader

   每个Shader当中必须包含至少一个SubShader，Subshader语义快是用来匹配不同显卡的不同能力需求。

   注意：全局tages和rendersetup对所有的pass有影响

   ```
   SubShader{
   
   //可选
   
   [Tages]
   
   //可选
   
   [RenderSetup]
   
   Pass1{
   
   ​	[Name]
   
   ​	[Tage]
   //用来告诉引擎我们希望怎样渲染该物体
   //LightMode：定义该渲染的角色
   //requireoptions：设置渲染条件
   ​	[RenderSetup]
   
   ​	......
   
   }
   
   Pass1{
   
   }
   
   ......
   
   }
   ```

   Tages:

   和unity渲染引擎进行沟通

   - queue

     控制渲染顺序

   - rendertype

     对着色器进行分类

   - disablebatching

     是否使用批处理

   - forcenoshadowcasting

     是否使用投影

   - ignoreprojector

     是否会受projector影响

   - canusespriteatlas

     用于sprites时，标签置为False

   - previewtype

     指明材质面板预览类型（plane 或者 skybox）

   RenderSetup:

   状态设置，用于设置相关状态

   pass：

   存在特殊pass

   usepass：复用其他shader的pass

   grabpass：负责抓取屏幕并将结果存储在一张纹理，并用于后续pass处理

   fallback：就是说fallback当上述的shader都不满足条件，默认用最低端的shader

   我们也可以使用customeditor语义来扩展编辑界面，也可以使用category语义来对shader命令分组

#### 1.3.4 Unity Shader的形式

我们可以在subshader语义快中对表面着色器编写代码，也可以在pass语块中编写点、片元着色器以及固定函数着色器编写代码

1. unity的宠儿：表面着色器

   是unity对顶点，片元着色器的抽象，我们只用较少的编程就可以完成

   ```
   shader"Custom/Simple Surface Shader"{
   	SubShader{
   		Tags{"RenderType"="Opaque"}
   		CGPROGRAM
   		
   		表面着色器代码编写
   		
   		ENFCG
   	}
   }
   ```

   是用CG/HLSL编写，嵌套

2. 顶点/片元着色器

   是用CG/HLSL编写，嵌套，更加复杂，灵活，注意是写在pass里面

3. 固定函数着色器

   完全使用shaderlab语法

### 1.4 unity shader数学基础

不懂数学者不得入内！！！

#### 1.4.1 背景

妞妞牛！

#### 1.4.2 笛卡尔坐标系

如何得到苍蝇的运动轨迹？

如何得到牛的位置？

用笛卡尔坐标系：确定位置

二维，三维

左手系用左手法则旋转，右手系用右手法则旋转

unity使用的是左手系，但对于观察空间来说使用的是右手系，因为摄像机的视角是朝着z轴的负方向看去，当z轴的坐标减小，意味着场景的深度增加

#### 1.4.3 点和矢量

矢量：包含模和方向

矢量是相对量

- 矢量乘法/除法

- 矢量加法/减法：三角形定则

- 矢量的模

- 单位矢量（方向）=归一化矢量

- 点积

  投影，判断向量之间的夹角

  点积自身的值是模的平方

- 叉积

  或者叫做外积

  公式：记忆

  叉乘满足反交换定律

  叉乘得到的是向量

  叉乘得到的是平行四边形的面积

  叉乘得到的方向垂直于两个边的方向

  叉乘的方向通过左手或者右手定则判断

#### 1.4.4 矩阵

不幸的是没有人告诉你母体是什么，你需要自己发现它

矩阵定义

和矢量联系，矩阵其实也是数组

- 矩阵标量乘法

- 矩阵矩阵乘法

  在shader的计算中，我们通常使用4*4矩阵计算

- 特殊矩阵

  1. 方块矩阵

     对角元素，当除了对角元素都为零的情况下，称这矩阵为对角矩阵

  2. 单位矩阵

     特殊的对角矩阵，当对角元素都为1时候的矩阵，等于数字当中的1

  3. 转置矩阵

     行列交换

  4. 逆矩阵

     只有方矩阵才有逆矩阵

     即矩阵和矩阵的逆相乘为单位矩阵

     可逆矩阵=非奇异矩阵

     不可逆矩阵=奇异矩阵

     矩阵的行列式不为0，则是可逆的

     逆矩阵的逆矩阵是原矩阵

     单位矩阵的本身为单位矩阵

     转置矩阵的逆矩阵是逆矩阵的转置

  5. 正交矩阵

     正交是一种属性

     矩阵的转置等于矩阵的逆，这个矩阵就是正交矩阵

     转置矩阵比逆矩阵好求解，可以节省时间

     正交矩阵的性质：

     矩阵的每一行是单位矢量，矩阵的每一行所代表的矢量相垂直，标准正交基满足这个条件

  6. unity中我们通常把矢量放在右侧，所以矢量为列矩阵，意味着我们的矩阵乘法一般是右乘，我们的阅读顺序一般是从右边开始向左边乘

  7. ![image-20220715195552575](C:\Users\XZY\AppData\Roaming\Typora\typora-user-images\image-20220715195552575.png)

     行列式的计算公式

#### 1.4.5 矩阵的几何意义：变换

1. 什么是变换

   线性变换

   - 旋转
   - 缩放
   - 错切
   - 镜像
   - 正交投影

   平移变换

   仿射变换：将线性变换和平移变换转变成统一形式，即四维矩阵

2. 齐次坐标

   齐次坐标空间

   方向矩阵的平移仍是方向矩阵

3. 分解基础变换矩阵

4. 缩放矩阵

   方向矩阵的缩放则要乘以缩放系数

5. 旋转矩阵

   旋转矩阵要注意，对于y轴进行变换时候，带入的是-西塔角度，因为是左手系

6. 复合变换

   一般来说我们约定变换顺序是先缩放，再旋转，再平移

   在unity中旋转顺序是按照zxy的顺序旋转，即Mz Mx My 矢量

#### 1.4.6 坐标空间

这一章主要讲解顶点着色器的功能，即是如何将顶点画到我们的屏幕上

1. 为什么要有不同的坐标空间

   理想很丰满，现实很骨感

2. 坐标空间的变换

   父子空间的转换

   其实就是求解变换矩阵

   - 如何求解空间坐标下点的坐标？
   - 求出通项公式

3. 顶点坐标空间的变换过程

4. 模型空间

   对象空间或者局部空间，模型空间存在自然方向前后左右上下，在unity中，x正方向为右，y正方向为上，z正方向为前

5. 世界空间

   世界空间的原点放在游戏空间的中心

   模型变换：将顶点从模型空间变换到世界空间M

6. 观察空间（三维）

   摄像机空间，摄像机朝向z轴的负方向

   观察变换：将顶点从世界空间变换到三维空间VP

7. 裁剪空间（二维）

   裁剪矩阵或者投影矩阵：为投影做准备

   正交投影&透视投影

   近裁剪平面小于远裁剪平面即为透视投影，近裁剪平面=远裁剪平面为正交投影

8. 透视投影

   通过FOV改变张开角度CP中near和far控制裁剪平面之间的距离，可以求出裁剪平面的高度，横向信息通过设置的相机长宽比来计算

   矩阵最后一行第三个是-1，其他为0

9. 正交投影

10. 齐次除法

    就是归一化的设备坐标（NDC）

#### 1.4.7 法线变换

乘以矩阵的逆转置矩阵

#### 1.4.8 unity shader的内置变量

Cg使用的是行优先变量



## 2 初级篇

### 2.5 开始Unity的学习之旅

#### 2.5.1 本书使用的软件和环境

本书基于mac编写的

#### 2.5.2 最简单的顶点/片元着色器

1. 顶点片元着色器的基本结构

   ```
   Shader"MyShaderName"{
       Properties{
       //属性
       }
       SubShader{
           Pass{
               CGPROGAM
               ......
               ENDCG
           }
       }
   
       fALLBACK"VertexLit"
   }
   ```

   最重要的是pass语义快

   这里做一个实例

   分析代码块：

   我们没有在shader里面设置任何tages和rendersetup，在pass里面也没有设置，采取的是默认tages和rendersetup，

               #pragma vertex vert
               #pragma fragment frag

   告诉unity哪一个函数包括顶点着色器或者片元着色器的代码

   ```
   float4 vert(float4 v :POSITION) : SV_POSITION{
                   return UnityObjectToClipPos(v);
                   //float4 m1 = UNITY_MATRIX_MVP;
                   //return mul (m1,v);
               }
   ```

   如上是vert即是顶点着色器的代码，逐顶点执行，这一句的含义是指把顶点坐标从模型空间转换到裁剪空间，float4类型的变量，position语义是指告诉unity把模型的顶点坐标输入到v中，而sv_position语义是指告诉unity顶点着色器函数是输出裁剪空间中的顶点坐标。

   ```
   fixed4 frag() : SV_Target{
                   return fixed4(1.0,1.0,1.0,1.0);
               }
   ```

   这里输出一个fixed4类型的变量，sv_target语义是指告诉渲染器把用户的输出颜色存储入一个渲染目标中，这里默认存储在帧缓存中，（0，0，0，0）表示黑色，（1，1，1，1）表示白色


   	Shader "Custom/Chapter5-SimpleShader"
   	{
   	        SubShader{
   	            Pass{
   	                CGPROGRAM
   	            #pragma vertex vert
   	            #pragma fragment frag
   	
   	            float4 vert(float4 v :POSITION) : SV_POSITION{
   	                return UnityObjectToClipPos(v);
   	            }
   	            fixed4 frag() : SV_Target{
   	                return fixed4(1.0,1.0,1.0,1.0);
   	            }
   	            ENDCG
   	        }
   	    }
   	}

   ![image-20220716095813506](C:\Users\XZY\AppData\Roaming\Typora\typora-user-images\image-20220716095813506.png)

   如图，设置纹理

2. 模型数据从哪里来

   我们需要知道更多模型的数据，上述例子我们通过position得知模型的顶点坐标，我们现在定义结构体来实现多属性的访问。

   ```
   Shader "Custom/Chapter5-SimpleShader"
   {
   	SubShader{
   		Pass{
   			CGPROGRAM
   
   			#pragma vertex vert
   			#pragma fragment frag
   
   			//用一个结构体来定义顶点着色器的输入
   			struct a2v {
   			//告诉unity用模型空间的顶点坐标填充vertex变量
   			float4 vertex : POSITION;
   			//告诉unity用模型空间的法线方向填充normal变量
   			float3 normal : NORMAL;
   			//告诉unity用模型的第一套纹理坐标填充texcoord变量
   			float4 texcoord : TEXCOORD0;
   
   			};
   
   			float4 vert(a2v v) : SV_POSITION{
   			return  UnityObjectToClipPos(v.vertex);
   			}
   			fixed4 frag() : SV_Target{
   				return fixed4(1.0,1.0,1.0,1.0);
   			}
   			ENDCG
   		}
   	}
   }
   ```

   我们定义结构体，利用结构体来访问模型空间内的相关属性，对于顶点着色器的输入，unity支持的语义有：position，tangent，normal，texcood0，texcood1，texcood2，texcood3，color等。

   结构体的通项公式

   ```
   struct structname{
   	type name : semantic;
   	type name : semantic;
   	        
   	        ......
   }
   ```

   语义是不可以缺少的

   a2v是什么意思？

   a是指application，是指应用，v是指vertex shader，这里是指把数据从应用阶段传入到顶点着色器当中，这些数据是从哪里来的捏，是由mesh render提供的，每一帧调用draw call时候，mesh render会把负责渲染的模型数据发送给unity shader。

3. 顶点着色器和片元着色器的通信

   在实际操作中，我们希望顶点着色器可以把模型的法线，纹理坐标传递给片元着色器，这就涉及到了顶点着色器和片元着色器之间的通信。

   

   ```
   // Upgrade NOTE: replaced 'mul(UNITY_MATRIX_MVP,*)' with 'UnityObjectToClipPos(*)'
   
   // Upgrade NOTE: replaced 'mul(UNITY_MATRIX_MVP,*)' with 'UnityObjectToClipPos(*)'
   
   // Upgrade NOTE: replaced 'mul(UNITY_MATRIX_MVP,*)' with 'UnityObjectToClipPos(*)'
   
   // Upgrade NOTE: replaced 'mul(UNITY_MATRIX_MVP,*)' with 'UnityObjectToClipPos(*)'
   
   Shader "Custom/Chapter5-SimpleShader"
   {
   	SubShader{
   		Pass{
   				
   			CGPROGRAM
   					
   			#pragma vertex vert
   			#pragma fragment frag
   
   
   			//用一个结构体来定义顶点着色器的输入
   			struct a2v {
   			//POSITION告诉unity用模型空间的顶点坐标填充vertex变量
   			float4 vertex : POSITION;
   			//NORMAL告诉unity用模型空间的法线方向填充normal变量
   			float3 normal : NORMAL;
   			//TEXCOORD0告诉unity用模型的第一套纹理坐标填充texcoord变量
   			float4 texcoord : TEXCOORD0;
   
   			};
   
   			//使用一个结构体来定义顶点着色器的输出
   	struct v2f {
   		//SV_POSITION告诉unity在pos中包含顶点在裁剪空间中的信息
   		float4 pos : SV_POSITION;
   		//COLOR0用于存储颜色信息
   		fixed3 color :COLOR0;
   	};
   
   			v2f vert(a2v v) {
   				//声明输出结构
   				v2f o;
   				o.pos = UnityObjectToClipPos(v.vertex);
   				// v.normal 包含了顶点的法线方向，其分量范围在[-1.0,1.0]
   				//下面的代码把分量范围映射到[0.0,1.0]
   				//存储到o.color中传递给片元着色器
   				o.color = v.normal * 0.5 + fixed3(0.5, 0.5, 0.5);
   				return o;
   			}
   
   			fixed4 frag(v2f i) : SV_Target{
   				return fixed4(i.color,1.0);
   			}
   			ENDCG
   		}
   	}
   }
   
   ```

   ![image-20220716152141908](C:\Users\XZY\AppData\Roaming\Typora\typora-user-images\image-20220716152141908.png)

   注意，这里我们通过定义v2f来定义从顶点着色器到片元着色器的转换过程，同时也需要定义结构体来满足变量的语义要求，片元和顶点的区别在于片元把顶点的数据进行了插值。

4. 如何使用属性

   在properties中定义属性，并在pass中同样定义同名称同类型的属性

#### 2.5.3 强大的援手：unity提供的内置文件和变量

unityCG.cginc包含许多结构体以及函数方便我们调用

#### 2.5.4 unity提供的CG/HLSL语义

1. 什么是语义

   语义简单理解为让shader知道从哪里读取数据，并把数据输出到哪里，unity并不包含所有的shader

   系统数值语义，以SV开头，这种语义是不能随便赋值，**不同平台对语义存在不同的兼容性，**这一点需要我们注意。

2. unity支持的语义

   - 应用阶段传递数据给顶点着色器

     - position：模型空间顶点的位置:float4
     - normal：顶点法线:float3
     - tangent：顶点切线:float4
     - texcoordn：顶点纹理坐标:float2/float4
     - color：顶点颜色fixed4/float4

   - 顶点着色器到片元着色器传递数据

     - sv_position：裁剪空间的顶点坐标
     - color0：通常用于输出第一组顶点颜色
     - color1：常用于输出第二组顶点颜色
     - texcoordn：通常用于输出纹理坐标

     一般来说我们使用texcoord来存储自定义的变量

   - 片元着色器输出语义

     sv_target：输出值将会存储到渲染目标中，类似于color

3. 如何定义复杂的变量模型

   

#### 2.5.5 Debug

unity shader的调试方法

1. 使用假彩色图像

   通过将假彩色图像映射到（0，1），区间然后和真彩色逐像素进行对比，来实现debug

2. 利用visual studio

   Graphics debugger

3. 帧调试器

#### 2.5.6 渲染平台的差异

1. 渲染纹理坐标差异

   一般来说unity帮助我们处理了反转情况，但是当我们打开抗锯齿的时候，并且需要同时渲染多张图像的时候，我们需要自己在顶点着色器主动开启翻转技术

   ```
   #if UNITY_UV_STARTS_AT_TOP
   if (_MainTex_TexelSize.y < 0)
     uv.y = 1-uv.y;
     #endif
   ```

   噪声纹理一般不需要进行反转处理

#### 2.5.7 shader的整洁之道

float：32位

half：16位 -6000到6000

fixed：11位 -2.0到2.0

PC端上GPU处理后的精度很难看出区别，所以我们一般通过观察移动端来判断精度，fixed和half基本相同目前来说，尽可能使用精度较低的模型，这样有助于优化shader的性能，一般使用fixed来存储颜色和单位矢量

我们要避免不必要的计算

谨慎使用循环选择语句

- 分支判断语句中使用条件变量最好是常数，在shader运行当中不会发生变化
- 分支当中包含的操作指令数尽量减少
- 分支的嵌套尽可能减少



不要 除于零

### 2.6 Unity中的基础光照

- 像素的可见性
- 像素的光照计算

#### 2.6.1 如何看到世界

- 光线是从光源发射出来
- 光线和物体相交，一些被吸收，一些被反射
- 摄像机吸收一些光，产生图像

1. 光源

   辐照度：垂直于光线方向的单位面积上单位时间穿过的能量来描述

   利用光源方向和面的法线方向的角度余弦值来描述

2. 吸收和散射

   散射改变光线的方向不改变光线的其他属性：包括折射以及反射

3. 着色

   根据材质属性以及光源信息等，使用一个等式来计算出射度过程，即为光照模型

4. BRDF模型

   

#### 2.6.2 标准光照模型

- 自发光：不使用全局光照技术，自发光将不会照亮周围物体
- 高光反射：phong模型，通过计算高光的反射方向，结合高光反射颜色等性质计算高光反射。blinn phong模型改善phong，不用计算反射角度，通过计算入射光线和视线之合的1/2,来取代更复杂就计算。blinn模型适合静态计算，phong模型适合动态计算
- 漫反射：符合兰伯特定律：反射光线的强度和表面法线与光源方向的夹角的cos正比
- 环境光：描述所有间接光照

片元着色器：逐像素计算

顶点着色器：逐顶点计算，在顶点计算光照，渲染图元内部插值，是非线性的会容易出现棱角现象，因为图元内部的颜色一般会暗于顶点颜色

菲涅尔反射无法用blinn phong来表示，其次blinn phong模型是各项同性

#### 2.6.3 unity中的环境光和自发光

![image-20220719113700366](C:\Users\XZY\AppData\Roaming\Typora\typora-user-images\image-20220719113700366.png)

#### 2.6.4 在unity shader中实现漫反射光照模型

四个变量

入射光线的颜色和强度，材质的漫反射系数，表面法线以及光源方向

Cg提供了函数来实现漫反射

**函数：**saturate（x）  饱和的意思

**参数：**x：用于操作的标量或者矢量

**描述：**把x截取在[0,1]范围，如果x是一个矢量，那么会对它的每一个分量进行这样的操作

- 实践：逐顶点光照

  ```
  // Upgrade NOTE: replaced '_World2Object' with 'unity_WorldToObject'
  
  // Upgrade NOTE: replaced '_World2Object' with 'unity_WorldToObject'
  
  Shader "Custom/Chapter6-DiffuseOixelLevel"
  { Properties
      {
         _Diffuse("Diffuse",Color) = (1.0,1.0,1.0,1.0)
  }
  SubShader
      {
          Pass{
          Tags { "LightMode" = "ForwardBase" }
  
          CGPROGRAM
  
          #pragma vertex vert
          #pragma fragment frag
  
          # include "Lighting.cginc"
          fixed4 _Diffuse;
  
      struct a2v {
          float4 vertex : POSITION;
          float3 normal : NORMAL;
      };
  
  
      struct v2f {
          float4 pos : SV_POSITION;
          float3 worldNormal : TEXCOOD0;
      };
  
      v2f vert(a2v v) {
          v2f o;
          o.pos = UnityObjectToClipPos(v.vertex);
  
          o.worldNormal = mul(v.normal, (float3x3)unity_WorldToObject);
  
          return o;
      }
  
      fixed4 frag(v2f i) : SV_Target{
  
          fixed3 ambient = UNITY_LIGHTMODEL_AMBIENT.xyz;
  
          fixed3 worldNormal = normalize(i.worldNormal);
  
          fixed3 worldLightDir = normalize(_WorldSpaceLightPos0.xyz);
  
  
          fixed3 diffuse = _LightColor0.rgb * _Diffuse.rgb * saturate(dot(worldNormal, worldLightDir));
  
          fixed3 color = ambient + diffuse;
  
          return fixed4(color, 1.0);
      }
          ENDCG
          }
  
  }
  FallBack "Diffuse"
  }
  
  ```

  

- 实践：逐像素光照

  ```
  // Upgrade NOTE: replaced '_World2Object' with 'unity_WorldToObject'
  // Upgrade NOTE: replaced 'mul(UNITY_MATRIX_MVP,*)' with 'UnityObjectToClipPos(*)'
  
  Shader "Custom/Chapter6-Diffuse VertexLevel"
  {
      Properties
      {
         _Diffuse("Diffuse",Color) = (1,1,1,1)
      }
          SubShader
      {
          Pass{
          Tags { "LightMode" = "ForwardBase" }
  
          CGPROGRAM
  
          #pragma vertex vert
          #pragma fragment frag
  
          # include "Lighting.cginc"
          fixed4 _Diffuse;
  
      struct a2v {
          float4 vertex : POSITION;
          float3 normal : NORMAL;
      };
  
  
      struct v2f {
          float4 pos : SV_POSITION;
          fixed3 color : COLOR;
      };
  
      v2f vert(a2v v) {
          v2f o;
          o.pos = UnityObjectToClipPos(v.vertex);
  
          fixed3 ambient = UNITY_LIGHTMODEL_AMBIENT.xyz;
  
          fixed3 worldNormal = UnityObjectToWorldNormal(v.normal);
  
          fixed3 worldLight = normalize(_WorldSpaceLightPos0.xyz);
  
          fixed3 diffuse = _LightColor0.rgb * _Diffuse.rgb * saturate(dot(worldNormal, worldLight));
  
          o.color = ambient + diffuse;
  
          return o;
      }
  
      fixed4 frag(v2f i) : SV_Target{
          return fixed4(i.color, 1.0);
      }
          ENDCG
          }
         
      }
      FallBack "Diffuse"
  }
  
  ```

  ![image-20220720154347181](C:\Users\XZY\AppData\Roaming\Typora\typora-user-images\image-20220720154347181.png)



半勃兰特模型

```
Shader "Custom/333"
{
	
		Properties
		{
			 _Diffuse("Diffuse",Color) = (1,1,1,1)
		}
			SubShader
		{


			Pass
			{
			  Tags{"LightMode" = "ForwardBase"}

				CGPROGRAM
				#pragma vertex vert
				#pragma fragment frag

				#include "Lighting.cginc"

			//定义和properties语义块中属性类型相匹配的变量
			fixed4 _Diffuse;

			struct a2v
			{
				float4 vertex : POSITION;
				//定义变量，并通过语义告知unity将法线信息存储到normal变量中
				float3 normal : NORMAL;
			};

			struct v2f
			{
				//SV_POSITION语义告诉Unity，pos 里包含了顶点在裁剪空间中的位置信息
				float4 pos : SV_POSITION;
				float3 worldNormal : TEXCOORD0;
			};

			v2f vert(a2v v)
			{
				v2f o;

				o.pos = UnityObjectToClipPos(v.vertex);//把顶点位置从模型空间转到裁剪空间

				o.worldNormal = UnityObjectToWorldNormal(v.normal);//法线转换到世界空间

				return o;
			}

			fixed4 frag(v2f i) : SV_Target
			{
				fixed3 ambient = UNITY_LIGHTMODEL_AMBIENT.xyz;//获得环境光

				fixed3 worldNormal = normalize(i.worldNormal);//法线转换到世界空间

				fixed3 worldLight = normalize(_WorldSpaceLightPos0.xyz);//获得光源方向

				fixed halfLambert = dot(worldNormal, worldLight) * 0.5 + 0.5;

				//使用半兰伯特模型计算光照 
				fixed3 diffuse = _LightColor0.rgb * _Diffuse.rgb * halfLambert;//saturate防止点积为负

				fixed3 color = ambient + diffuse;

				return fixed4(color,1.0);
			}

				ENDCG

		 }



		}
			Fallback "Diffuse"
	}


```



![image-20220720154809304](C:\Users\XZY\AppData\Roaming\Typora\typora-user-images\image-20220720154809304.png)



#### 2.6.5 在unity shader 中实现高光反射光照模型

四个要素

入射光线的颜色强度，材质的高光反射系数，视角方向以及反射方向

逐顶点光照

```
// Upgrade NOTE: replaced '_Object2World' with 'unity_ObjectToWorld'

Shader "Custom/1"
{
    properties{
       _Diffuse("Diffuse",Color) = (1,1,1,1)
       _Specular("Specular",Color) = (1,1,1,1)
       _Gloss("Gloss",Range(8.0,256)) = 20
    }

        subshader{
        pass{
        Tags {"LightMode" = "ForwardBase"}
       
            CGPROGRAM

        #pragma vertex vert
        #pragma fragment frag
        #include "Lighting.cginc"

        fixed4 _Diffuse;
        fixed4 _Specular;
            float _Gloss;

            struct a2v {
                float4 vertex :POSITION;
                float3 normal :NORMAL;

        };
            struct v2f {
                float4 pos : SV_POSITION;
                fixed3 color : COLOR;
            };

            v2f vert(a2v v) {
                v2f o;
                o.pos = UnityObjectToClipPos(v.vertex);

                fixed3 ambient = UNITY_LIGHTMODEL_AMBIENT.xyz;

                fixed3 worldNormal = UnityObjectToWorldNormal(v.normal);

                fixed3 worldLightDir = normalize(_WorldSpaceLightPos0.xyz);

                fixed3 diffuse = _LightColor0.rgb * _Diffuse.rgb * saturate(dot(worldNormal, worldLightDir));

                fixed3 reflectDir = normalize(reflect(-worldLightDir, worldNormal));

                fixed3 viewDir = normalize(_WorldSpaceCameraPos.xyz - mul(unity_ObjectToWorld, v.vertex).xyz);

                fixed3 specular = _LightColor0.rgb * _Specular.rgb * pow(saturate(dot(reflectDir, viewDir)), _Gloss);

                o.color = ambient + diffuse + specular;

                return o;
                
            }

            fixed4 frag(v2f i) : SV_Target{
                return fixed4(i.color,1.0);
            }
                ENDCG
    }
    }
        Fallback"Specular"
}

```

逐像素光照

// Upgrade NOTE: replaced '_Object2World' with 'unity_ObjectToWorld'
// Upgrade NOTE: replaced '_World2Object' with 'unity_WorldToObject'

Shader "Custom/2"
{
        properties{
           _Diffuse("Diffuse",Color) = (1,1,1,1)
           _Specular("Specular",Color) = (1,1,1,1)
           _Gloss("Gloss",Range(8.0,256)) = 20
        }

            subshader{
            pass {
            Tags {"LightMode" = "ForwardBase"}
    
                CGPROGRAM
    
            #pragma vertex vert
            #pragma fragment frag
            #include "Lighting.cginc"
    
            fixed4 _Diffuse;
            fixed4 _Specular;
                float _Gloss;
    
                struct a2v {
                    float4 vertex :POSITION;
                    float3 normal :NORMAL;
    
            };
                struct v2f {
                    float4 pos : SV_POSITION;
                    float3 worldNormal :TEXCOORD0;
                    float3 worldPos : TEXCOOD1;
                };
    
                v2f vert(a2v v) {
                    v2f o;
                    o.pos = UnityObjectToClipPos(v.vertex);
    
                    o.worldNormal = mul(v.normal, (float3x3)unity_WorldToObject);
    
                    o.worldPos = mul(unity_ObjectToWorld, v.vertex).xyz;
    
                    return o;


                }
    
                fixed4 frag(v2f i) : SV_Target{
                    
                    fixed3 ambient = UNITY_LIGHTMODEL_AMBIENT.xyz;
    
                    fixed3 worldNormal = normalize(i.worldNormal);
    
                    fixed3 worldLightDir = normalize(_WorldSpaceLightPos0.xyz);
    
                    fixed3 diffuse = _LightColor0.rgb * _Diffuse.rgb * saturate(dot(worldNormal, worldLightDir));
    
                    fixed3 reflectDir = normalize(reflect(-worldLightDir, worldNormal));
    
                    fixed3 viewDir = normalize(_WorldSpaceCameraPos.xyz - i.worldPos.xyz);
    
                    fixed3 specular = _LightColor0.rgb * _Specular.rgb * pow(saturate(dot(reflectDir, viewDir)), _Gloss);
    
                    return fixed4 (ambient + diffuse + specular,1.0) ;
                }
                    ENDCG
        }
        }
            Fallback"Specular"
    }

blinn phong光照模型

![image-20220722161136946](C:\Users\XZY\AppData\Roaming\Typora\typora-user-images\image-20220722161136946.png)



#### 2.6.6 使用Unity内置函数

内置函数：

float3 WorldSpaceViewDir(float4 v):输入模型空间中顶点的位置，返回世界空间该点到摄像机观察的方向

float3 UnityWorldSpaceViewDir(float4 v):输入世界空间顶点位置，返回世界空间该点到摄像机观察的方向

float3 ObjSpaceViewDir(float4 v):输入模型空间顶点位置，返回模型空间该点到摄像机观察的方向



float3 WorldSpaceLightDir(float4 v):仅可以用于前向渲染当中。输入模型空间顶点位置，返回世界空间该点到光源的方向（没有归一化）

float3 UnityWorldSpaceLightDir(float4 v):仅可以用于前向渲染当中。输入世界空间顶点位置，返回世界空间该点到光源的方向（没有归一化）

float3 ObjSpaceLightDir(float4 v):仅可以用于前向渲染当中。输入模型空间顶点位置，返回模型空间该点到光源的方向（没有归一化）



float3 UnityObjectToWorldNormal:(float3 norma):把法线方向从模型空间转换到世界空间当中

float3 UnityObjectToWorldDir:(float3 dir):把方向矢量从模型空间转换到世界空间当中

float3 UnityWorldToObjectDir:(float3 dir):把方向矢量从世界空间转换到模型空间当中

### 2.7 基础纹理

纹理的含义就是我们希望用一张图片来贴在物体表面，逐纹素控制模型的颜色

利用纹理展开技术来把纹理坐标存储在每一个点上，纹理映坐标定义了该顶点在纹理中对应的2

d坐标（u，v）通常把纹理坐标称其为UV坐标，通常我们都会归一化纹理映射坐标，但不归一化有时十分好用，后续章节会详细讨论

#### 2.7.1 单张纹理

使用blinn phong模型来计算光照

![image-20220723161723599](C:\Users\XZY\AppData\Roaming\Typora\typora-user-images\image-20220723161723599.png)

```
// Upgrade NOTE: replaced '_Object2World' with 'unity_ObjectToWorld'
// Upgrade NOTE: replaced 'mul(UNITY_MATRIX_MVP,*)' with 'UnityObjectToClipPos(*)'

Shader "Custom/Chapter7"
{
    Properties
    {
        _Color("Color Tint",Color) = (1,1,1,1)
        _Specular("Specular",Color) = (1,1,1,1)
        _Gloss("Gloss",Range(8.0,256)) = 20
        _MainTex("MainTex",2D) = "white"{}
    }
    SubShader
    {
        Pass{
            Tags{"LightMode"="ForwardBase"}
             CGPROGRAM
        #pragma vertex vert
        #pragma fragment frag 
        #include "Lighting.cginc"

        fixed4 _Color;
        sampler2D _MainTex;
        float4 _MainTex_ST;
        fixed4 _Specular;
        float4 _Gloss;

        struct a2v{
        float4 vertex : POSITION;
        float3 normal : NORMAL;
        float4 texcoord : TExCOORD0;
        };

        struct v2f{
        float4 pos : SV_POSITION;
        float3 worldNormal : TEXCOORD0;
        float3 worldPos : TEXCOORD1;
        float2 uv :TEXCOORD2;
        };

        v2f vert(a2v v){
        v2f o;
        o.pos = UnityObjectToClipPos(v.vertex);
        o.worldNormal = UnityObjectToWorldNormal(v.normal);
        o.worldPos = mul(unity_ObjectToWorld, v.vertex).xyz;

        o.uv = v.texcoord.xy *_MainTex_ST.xy +_MainTex_ST.zw;

        return o;
        }

        fixed4 frag(v2f i) : SV_Target{
        fixed3 worldNormal = normalize(i.worldNormal);
        fixed3 worldLightDir = normalize(UnityWorldSpaceLightDir(i.worldPos));

        fixed3 albedo = tex2D(_MainTex,i.uv).rgb*_Color.rgb;
        fixed3 ambient = UNITY_LIGHTMODEL_AMBIENT.xyz * albedo;
        fixed3 diffuse = _LightColor0.rgb * albedo * max(0, dot(worldNormal, worldLightDir));
		fixed3 viewDir = normalize(UnityWorldSpaceViewDir(i.worldPos));
		fixed3 halfDir = normalize(worldLightDir + viewDir);
		fixed3 specular = _LightColor0.rgb * _Specular.rgb * pow(max(0, dot(worldNormal, halfDir)), _Gloss);
 
		return fixed4(ambient+diffuse + specular, 1.0);
        }
        ENDCG
}
       
    }
    Fallback "Specular"
}

```



#### 2.7.2 凹凸映射

使用纹理来修改模型表面的法线，让模型提供更多细节

1. 高度纹理

   模拟表面位移，得到修改后的法线值，也称作高度映射
   高度图存储强度值，表示模型表面的海拔高度：颜色越浅，向外凸起，颜色越深，向里凹。但是计算复杂

2. 法线纹理

   直接存储表面法线，法线映射


#### 2.7.3 渐变纹理

略（已经看完）

#### 2.7.4 遮罩纹理

略（已经看完）

### 2.8 透明效果

```
Shader "Custom/Chapter8-AlphaBlend"
{
    Properties
    {
       _Color ("Main Tint",Color)=(1,1,1,1)
       _MainTex("Main Tex",2D)="white"{}
       _AlphaScale("Alpha Scale",Range(0,1))=1

    }
    SubShader
	{

	        Tags{"Queue" = "Transparent" "IgnoreProjector" = "True" "RenderType" = "Transparent"}

			

		Pass
		{
		    Tags{"LightMode" = "ForwardBase"}
			Cull Front 
			Zwrite Off
			Blend SrcAlpha OneMinusSrcAlpha
                        
			CGPROGRAM
			#pragma vertex vert
			#pragma fragment frag
			#include "LIghting.cginc"

			fixed4 _Color;
			sampler2D _MainTex;
			float4 _MainTex_ST;
			fixed _AlphaScale;

			struct a2v
			{
				float4 vertex : POSITION;
				float3 normal : NORMAL;
				float4 texcoord : TEXCOORD0;
			};

			struct v2f
			{
			        float4 pos : SV_POSITION;
			        float3 worldNormal : TEXCOORD0;
			        float3 worldPos : TEXCOORD1;
			        float2 uv : TEXCOORD2;
			};		

			v2f vert (a2v v)
			{
				v2f o;
				o.pos = UnityObjectToClipPos(v.vertex);
				o.worldNormal = UnityObjectToWorldNormal(v.normal);
				o.worldPos = mul(unity_ObjectToWorld, v.vertex).xyz;
				o.uv = TRANSFORM_TEX(v.texcoord, _MainTex);
				return o;
			}
			
			fixed4 frag (v2f i) : SV_Target
			{
				fixed3 worldNormal = normalize(i.worldNormal);

				fixed3 worldLightDir = normalize(UnityWorldSpaceLightDir(i.worldPos));

				fixed4 texColor = tex2D(_MainTex, i.uv);
				
				fixed3 albedo = texColor.rgb * _Color.rgb;
	
				fixed3 ambient = UNITY_LIGHTMODEL_AMBIENT.xyz * albedo;
				
				fixed3 diffuse = _LightColor0.rgb * albedo * saturate(dot(worldNormal,worldLightDir));

				return fixed4(ambient + diffuse,texColor.a*_AlphaScale);
;			}
			ENDCG
		}

		Pass
		{
		    Tags{"LightMode" = "ForwardBase"}
			Cull Back
			Zwrite Off
			Blend SrcAlpha OneMinusSrcAlpha
                        
			CGPROGRAM
			#pragma vertex vert
			#pragma fragment frag
			#include "LIghting.cginc"

			fixed4 _Color;
			sampler2D _MainTex;
			float4 _MainTex_ST;
			fixed _AlphaScale;

			struct a2v
			{
				float4 vertex : POSITION;
				float3 normal : NORMAL;
				float4 texcoord : TEXCOORD0;
			};

			struct v2f
			{
			        float4 pos : SV_POSITION;
			        float3 worldNormal : TEXCOORD0;
			        float3 worldPos : TEXCOORD1;
			        float2 uv : TEXCOORD2;
			};		

			v2f vert (a2v v)
			{
				v2f o;
				o.pos = UnityObjectToClipPos(v.vertex);
				o.worldNormal = UnityObjectToWorldNormal(v.normal);
				o.worldPos = mul(unity_ObjectToWorld, v.vertex).xyz;
				o.uv = TRANSFORM_TEX(v.texcoord, _MainTex);
				return o;
			}
			
			fixed4 frag (v2f i) : SV_Target
			{
				fixed3 worldNormal = normalize(i.worldNormal);

				fixed3 worldLightDir = normalize(UnityWorldSpaceLightDir(i.worldPos));

				fixed4 texColor = tex2D(_MainTex, i.uv);
				
				fixed3 albedo = texColor.rgb * _Color.rgb;
	
				fixed3 ambient = UNITY_LIGHTMODEL_AMBIENT.xyz * albedo;
				
				fixed3 diffuse = _LightColor0.rgb * albedo * saturate(dot(worldNormal,worldLightDir));

				return fixed4(ambient + diffuse,texColor.a*_AlphaScale);
;			}
			ENDCG
		}
	}

	Fallback"Transparent/Vertexlit"

}

```

![image-20220729104443976](C:\Users\XZY\AppData\Roaming\Typora\typora-user-images\image-20220729104443976.png)

## 3 中级篇

### 3.9 更复杂的光照

#### 3.9.1 Unity的渲染路径

渲染路径：为每一个pass指定他的渲染路径

可以通过摄像机渲染的物体使用前向或者延迟渲染路径‘

指定渲染路径相当于标签告诉unity做出相关的准备

1. 前向渲染路径的原理

   - 逐顶点处理

   - 逐像素处理

   - 球谐函数处理

     ![image-20220729142909848](C:\Users\XZY\AppData\Roaming\Typora\typora-user-images\image-20220729142909848.png)

   前向渲染中，unity使用的判断规则如下：

   场景中最亮的平行光始终使用逐像素处理

   not important 逐个顶点或者SH处理

   important 按照逐像素处理

   根据以上规则得到逐像素光源数量小于quality setting的设置，会有更多逐像素光源渲染

   

2. 延迟渲染：unity中使用unity5.0之前和之后两个版本，之后的版本支持使用standard shader

   缺点：

   - 不支持抗锯齿
   - 不能处理半透明物体
   - 对显卡有一定要求

   第一个pass用来完成G-Buffer：漫反射颜色以及高光反射颜色，平滑度，法线，自发光等

   G缓冲区一般包括如下几个纹理：RT

   - RT0
   - RT1
   - RT2
   - RT3
   - 深度缓冲以及模板缓冲

   第二个pass用来渲染计算



#### 3.9.2 unity的光源类型

四种光源：

平行光

点光源

聚光灯

面光源

point light

spot light

- 位置强度，颜色方向，衰减五个属性



1. 平行光

   照亮范围无限制：作为太阳

   几何属性只有方向，没有具体位置，没有衰减

2. 点光源

   照亮范围有限制，空间是由球体定义

3. 聚光灯

   锥形体


实践：

```
bass pass
//设置渲染路径，forwardbase
#pragma multi_compile_fwdbase
//保证我们在shader中使用光照衰减等光照变量可以正确被赋值

。。。。。。


additional pass
//设置渲染路径，forwardadd
使用Blend开启混合模式，blend one one
同时我们需要使用USING_DIRECTIONAL_LIGHT来判断光源类型，平行光使用_WorldSpaceLightPos0.xyz直接得到光源方向，而点光源和聚光灯需要减去世界空间下顶点的位置
```



unity会选择最亮的平行光在base pass进行处理，其他的平行光交给additional pass处理



#### 3.9.3 unity的光照衰减



#### 3.9.4 unity的阴影

学习如何让一个物体向其他物体投射阴影，以及让物体接收来自其他物体的阴影

阴影如何实现：

光线无法到达的区域

shadow map技术：将摄像机和光源的位置重合，摄像机看不到的位置就是阴影位置

屏幕空间的阴影映射技术

如果需要接收来自其他物体的阴影，需要在shader中对阴影映射纹理进行采样，把采样结果和最后的阴影结果相乘得到最后的阴影效果

如果我们想向别的物体投影阴影，就必须把物体加入到光源的阴影映射纹理计算，让其他的物体在对阴影映射纹理采样时得到相关信息，通过为该物体执行lightmode为shadowcaster的pass来实现的



不透明物体的阴影

通常直接fallback

注意，物体接收阴影时候，相关属性名称需要和内置函数的定义相同，a2v顶点坐标用vertex，位置使用pos，输入结构体为v

我们还可以使用帧调试来查看阴影绘制过程

统一管理光照衰减和阴影

透明度物体的阴影

### 3.10 高级纹理

更为复杂的纹理

#### 3.10.1 立方体纹理

是环境映射的一种实现方法，模拟物体周围环境，使得物体像金属一样反射周围环境

立方体纹理包括六张图像，是三维纹理坐标

优点：方便，效果好

缺点：引入新的光源或者物体，需要重新计算纹理坐标，而且立方体纹理不能反射自身，所以尽量对凸面体使用，对凹面体不用，最经典的例子天空盒子

1. 天空盒子

2. 创建用于环境映射的立方体纹理

   - 方法一：提供一张具有特殊布局的纹理，然后设置texture type
   - 方法二：直接创建cubemap，这里建议使用方法一，因为方法一可以支持边缘修正，光华反射，以及HDR功能
   - 方法三：通过unity脚本自动生成立方体纹理

3. 反射

   

   ```
   Shader "Custom/Chapter10-Reflection"
   {
       Properties{
       _Color ("Color Tint",Color)=(1,1,1,1)
       _ReflectColor("Reflection Color",Color)=(1,1,1,1)
       _ReflectAmount("Reflect Amount",Range(0,1))=1
       _Cubemap("Reflection Cubemap",Cube) = "_Skybox"{}
       }
       SubShader
   	{
   		Tags { "RenderType"="Opaque" }
   		LOD 100
    
   		Pass
   		{
   			Tags{ "LightMode" = "ForwardBase" }
   			CGPROGRAM
   			#pragma vertex vert
   			#pragma fragment frag
   			#pragma multi_compile_fwdbase
   			
   			#include "UnityCG.cginc"
   			#include "Lighting.cginc"
   			#include "AutoLight.cginc"
    
   			struct appdata
   			{
   				float4 vertex : POSITION;				
   				float3 normal : NORMAL;				
   			};
    
   			struct v2f
   			{				
   				float4 pos : SV_POSITION;
   				float3 worldNormal : TEXCOORD0;
   				float3 worldPos : TEXCOORD1;
   				float3 worldViewDir  : TEXCOORD2;	
   				float3 worldRef1 : TEXCOORD3;//反射向量
   				SHADOW_COORDS(4)
   			};
    
   			samplerCUBE _Cubemap;
   			fixed4 _Color;
   			fixed4 _ReflectColor;
   			fixed _ReflectAmount;
   						
   			
   			v2f vert (appdata v)
   			{
   				v2f o;
   				o.pos = UnityObjectToClipPos(v.vertex);
   				o.worldNormal = UnityObjectToWorldNormal(v.normal);
   				o.worldPos = mul(unity_ObjectToWorld, v.vertex).xyz;
   				o.worldViewDir = UnityWorldSpaceViewDir(o.worldPos);
   				//根据view和normal经过reflect函数计算出反射向量
   				o.worldRef1 = reflect(-o.worldViewDir, o.worldNormal);
   				TRANSFER_SHADOW(o)			
   				return o;
   			}
   			
   			fixed4 frag (v2f i) : SV_Target
   			{				
   				fixed3 worldNormal = normalize(i.worldNormal);
   				fixed3 worldLightDir = normalize(UnityWorldSpaceLightDir(i.worldPos));
   				fixed3 ambient = UNITY_LIGHTMODEL_AMBIENT.xyz;
   				//最终反射颜色：texCUBE函数：从_Cubemap立方体纹理通过反射向量进行采样，再与自定义反射颜色相乘
   				fixed3 reflection = texCUBE(_Cubemap, i.worldRef1).rgb * _ReflectColor.rgb;
   				fixed3 diffuse = _LightColor0.rgb * _Color.rgb * saturate(dot(worldNormal, worldLightDir));
   				UNITY_LIGHT_ATTENUATION(atten, i, i.worldPos)
   				//最终输出颜色:环境光 + 漫反射颜色->反射颜色的插值_ReflectAmount 再乘以 (atten) 
   				return fixed4(ambient + lerp(diffuse, reflection, _ReflectAmount) * atten , 1.0);
   			}
   			ENDCG
   		}
   	}
   
   	FallBack "Reflective/VertexLit"//包含阴影处理的一个内置shader
   }
   
   ```

   ![image-20220731164112057](C:\Users\XZY\AppData\Roaming\Typora\typora-user-images\image-20220731164112057.png)

4. 折射

#### 3.10.2 渲染纹理

#### 3.10.3 程序纹理

### 3.11 让画面动起来

#### 3.11.1 内置变量（时间）

#### 3.11.2 纹理动画

#### 3.11.3 顶点动画



[back](./)