#速率变换网络中的多参考帧编码 
Dual Frame Motion Compensation With Uneven Quality Assignment
##场景，问题，对象，变量，约束

网络传输过程中带宽速率降低，引起视频质量下降--延迟，帧丢失
* 变化多少，是否已知？此处假设变化已知

**运动补偿，参考帧，帧之间的依赖性！单一依赖，多重依赖，主要依赖于！**

通过多参考帧提高补偿准确性
* 多参考帧：
    * STR, short term reference
    * LTR, long term reference
* 区别对待
    低带宽时周期构建高质量LTR--需要额外bits,带宽，时间
    
实际场景：
* 简单的带宽变化场景
* 不均匀分配时，对问题的解决：
    * 固定带宽分出一部分来传输LTR
    * cognitive radio自动识别未使用部分，短暂借用
    * 直接传输，利用缓存机制减轻时延问题

结论：  
1. 多参考帧遇到rate switching，重新选取选取STR过程一样，但是LTR短时间内质量较高，所以短期内视频质量相比单参考帧上升，长期类似  
2. **不均匀分配的多参考帧**效果好于**常规多参考帧**和**增强质量的单参考帧（plused quality single fram）**  
3. rate switching network中，多参考帧可以平滑场景转换带来的问题 




---
---
---
#264新标准中QP与stepsize关系变化应对及CM,RC,QP鸡蛋问题解决
Rate-distortion analysis for H.264/AVC video coding and its application to rate control

##场景，问题，对象，变量，约束
问题：

1. 264中的quantization scheme使得quantization parameter和true quantization stepsize间保持非线性关系 
2. 鸡蛋问题
	需要已知MV,编码模式来选择量化参数，然而RDO顺序不同（帧，宏块？），并且需要根据量化参数来选择MV,编码模式

RD model，量化参数，量化步长，buffer


##贡献
1. 提出新的RD模型，表示非线性QP和真实步长之间的关系
	基于统计
2. 改进码率控制scheme

	1. frame level one pass, MB level partial two pass但可通过编码参数控制，所以可以实现one pass--完全one pass为一个子集
	2. 融入video buffering，根据H264加入的hypothetical reference decoder（HRD），满足buffer不上溢下溢的条件

	迭代两次，第一次，设定QP，根据MB信息得到最优CM,RC，然后根据CM,RC更新QP，两次的RD cost比较，如果后者高，根据阈值选定部分MB再次更新QP

---
---
---
#SVC中取舍STAR组合问题，rate和perceptual quality模型构建
modeling of rate and perceptual quality of compressed video as functions of frame rate and quantization stepsize and its applications   

##场景，问题，对象，变量，约束
具体编码过程，输出码率bit rate受几个因素影响：

	spatial resolution--frame size
	temporal resolution--frame rate
	amplitude resolution--SNR，由QS,QP决定
	STAR

有不同组合，均要满足BR条件，但是编码结果(感知结果)不一样：
	
	noticeable coding artifacts--SR++,TR++,QS,QP++
	high quality--SR--,TR--, QP--

一般经验：控制变量法

	固定TR,SR
	选QP
	不能满足BS，加入skip

	或：
	联合选取QP和skip，以经验规则或MSE控制

考虑接收端，变化多，根据接收端rate，选择scalable stream中特定的组合--提取特定layer   
如何取舍？--video adaption

##贡献
构建两个模型，rate，perceptual quality，变量均为frame rate，QP   
RD model编程QR model

模型构建思路：**控制变量补偿法**

	rate:FR最高时，基于不同QP的范数关系，乘上实际FR的纠正系数(temporal correction)
	quality 同理，基于解码后帧的PSNR,乘上补偿项

参数：场景依赖
利用源视频3个特征，线性组合得到（3+2）

应用场景：
rate：scalable，nonscalable
quality：scalable

扩展：
	
	加入SR因素
	验证quality在nonscalable下的实用性
	对不稳定序列，使用滑动窗口或scene change detection改进内容变化场景下的取舍


---
---
---
#二次多项式RD model的视频编码
a new rate control scheme using quadratic rate distortion model--CSVT1997
##场景，问题，对象，变量，约束

已知变量R，D，建模，求参，用模型编码

D:使用一帧的平均量化尺度（average quantization scale）

二次模型参数，最小二乘法

##贡献
模型，高斯展开，得到二次

假设：输出码率恒定

bit allocation scheme：

	两个假设： I，P，B量化参数的尺度比例为定值--distortion，相同类型间类似，不同类型间比例固定，未考虑场景变化
	一个等于： 总码率*总图片数=总bits==剩余bits
	三个码率： 依据先前模型，计算I,P,B三种帧的码率，具体求解顺序有区别

##结果指标

	PSNR
	bit rate fluctuation


---
---
---
#log关系的frame-level RD-model视频编码
a rate-quantization model for mpeg encoders--ICIP1997
##场景，问题，对象，变量，约束

RD建模，求解，log关系

frame-level


##假设

序列中，所有图片量化矩阵固定

##贡献

##指标

	PSNR
	mismatch ratio：输出bit rate和目标bit rate间关系



---
---
---
#基于小波变换，适应JPEG2000的MC编码模型
A model-based motion compensated video coder with JPEG2000 compatibility--ICIP2004
##场景，问题，对象，变量，约束

可伸缩，扩展视频编码

常规视频编码，基于

	运动补偿
	DCT

用小波变换替代

可行性：

	已经应用到静态图片编码，性能优于DCT
	对伸缩性的支持
	效率被证明和DCT视频编码类似--motion compensated lifting approach

##贡献
。。。


---
---
---
#低延时通讯中DCT编码器改进
rate control in dct video coding for low-delay communications--CSVT1999
##场景，问题，对象，变量，约束

低延迟实时通讯中的问题：
bit rate高，延迟，delay，不连续
bit rate低，带宽浪费

当前方法及问题：

初始化后多次优化：
	
	计算复杂 computational complexity
依据公式选择：	
	
	基于对剩余bits，buffer的使用率，块中像素值方差等的建模
	不能严格满足bit约束条件
	常用于高buffer延迟中，在低延迟应用中会导致 过多skip 和 浪费带宽


##贡献
优化方向：

	减少skip的次数，满足bit rate要求，更准确模型
	运动补偿中，建立D(B)模型，当前总大小下的distortion
		量化参数：B，运动补偿块中的像素值方差，部分rate-model的参数

##结论：
减小运动补偿帧的MSE，

	bit rate由高到低，QP的stepsize由小变大



---
---
---
#视觉跟踪数据集的构建
Eye-tracking database for a set of standard video sequences

##场景，问题，对象，变量，约束

定位：

	显著性模型质量评价的工具

对象变量：

视频
	
	内容
	帧率
	总帧数
	YUV
	分辨率

人的视线移动

	多个人
	每个人多次--两次

##实验过程



##结果分析

看两次视频，移动轨迹是否不一样？--一致性，多次重复

	是，通过每帧上，两次视觉停留点的欧氏距离来度量
		单纯解释存在，不建模
	特别显著序列与不显著序列：
		多个对象比单个对象显著
		单个对象中，大对象比小对象显著

		
常见模型准确性：

IKN, IB

假设：
两模型的分数分布有相同的均值


score=像素点显著值*高斯权重(模仿2`视角)，整图求和，所有人求和
像素点显著值，在整个视频序列上做归一化

t检验

	IKN和IB效果不一样？
		是的！
		除了一种情况，其他情况IKN性能优于IB(city)

	不同模型适合特定某次观看？
		否！两个模型对第几次看不敏感

	运用绝对准确判断模型准确性(去量化，二值化)
		是否比saliency均匀分布效果好？(score高)--情况：视频序列
			IKN:两次观看中，一种情况差，其余情况好
			IB:第一次观看一种情况差，第二次观看两种情况类似

			特殊情况：city
				目标：唯一，规模大，颜色文理分布与背景相似，镜头移动过程中相对背景静止


---
#相关系数非0比例的数据引入
optimum bit allocation and accurate rate control for video coding via ρ-domain source modeling--csvt2002

##问题，场景，对象，变量，关系，约束
RD模型估计中，两种方式各有缺点：

	DCT,DWT没有充分利用信息
	模型估计复杂非线性，有针对性和局限性，场景变换时性能下降

**比例的应用**！！！



##贡献

引入变量，量化变换相关系数中非0的比例

构建线性模型

针对视频中的物体编码(h264)或对宏块进行分类


----
# 压缩域，基于HEVC编码后的特征，利用SVM进行显著性检测
learning to detect video saliency with hevc features--TIP-2017

## 贡献：
eye-tracking dataset:https://github.com/remega/video_database
分析，选取特征
SVM分类

---
# 显著性编码
saliency-aware video compression--TIP2014

## 问题场景，对象变量，

* Lossy image and video encoders are known to produce undesirable compression artifacts at low bit rates.[1][2]
	* Blocking artifacts are the most common form of compression artifacts
	* 普通滤波不能处理：仅能处理边界，但此处为structured high-frequency，不在边界上

* ROI编码：
	* 描述：用bitrate区别对待region
	* 依据：HVS特点，只注意到$2^\circ-5^\circ$
	* 问题：非显著性区域bitrate过低产生visible coding artifacts，吸引注意力变为显著性区域（但是不一定会被注意到）
		* 如何识别？[8],[9]
		* 如何解决，减少？


