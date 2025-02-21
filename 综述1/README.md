# 关于3D物体检测和6D姿态估计的综述
## 1.两者任务的本质区别
>**3D物体检测**：主要目的是 识别并定位物体在三维空间中的位置。具体来说，它不仅要检测物体的存在，还要估计物体的位置和尺寸  
**输出**：输出一个三维边界框，定义了物体在三维空间中的位置和尺寸。

>**6D姿态估计**:主要目标是 确定物体的姿态，即物体在三维空间中的 旋转（3D旋转）和位移（3D平移）  
**输出**：物体的 旋转矩阵或四元数（表示物体的方向）和 位移向量（表示物体的位置）。

## 2.3D物体检测的困难
本文只提到了3D物体检测的困难，但是6D姿态估计在很大程度上与3D物体检测重合，因此两者面临的困难是共有的。

为了检测3D物体并推测其姿态，我们需要完全理解图像，而不仅仅是知道图像的分类或定位。但是在实现物体识别和定位任务时存在一些重要的障碍，如

（1）.背景和环境的影响：**遮挡**、**混乱的环境**、**光照条件**

（2）.对于精度和准度的要求：我们需要找到一张图片里**正确的部分**（准度）并且**更加精确地识别**（3D/2D）物体（精度）

（3）.处理任何给定的图像可能包含多个物体：这些物体位于图像的不同位置，由于测试窗口的数量庞大，过程**相对昂贵**且会**产生额外的窗口**。此外，如果应用某些稳定的滑动窗口模板，它会创建不满意的区域。

（4）.大小差异和视角：对于一张图片中的物体，是 **“体积小”** 还是 **“距离远”** 的问题

>传统的3D物体检测模型流程：信息区域选择->特征提取->分类。

## 简单介绍大领域
## 1.人工神经网络（ANN）
人工神经网络（ANN）的功能与人类大脑的功能几乎相同，因为知识是通过网络在学习过程中获取的，并通过某些突触权重神经元进行存储。为了实现最终的设计目标并改变网络的突触权重，ANN实现了一种称为学习算法的学习过程。
## 2.深度神经网络（DNN）
通过应用多层网络从原始数据中提取高级特征。例如，在图像处理中，深度学习模型的较低层只能识别边缘，而较高层可以检测某些字母、物体或物体的特征。最终，深度学习可以处理无序/有序、标记或未标记的数据，并构建模式以进行更准确的预测 
## 3.卷积神经网络及其变体
卷积神经网络（CNN）具有深度前馈架构，并且具有显著的泛化能力，可以通过全连接层构建更好的网络。CNN 的两个最显著特点是其**分类组合能力**和从图像中**提取强大特征**的能力，这证明了 CNN 是最强大的目标检测分类器之一。
一下为常见的CNN基础模型：
>ImageNet 、AlexNet 、ConvNet、LeNet、VGGNet、ResNet 、ZFNet 、GoogLeNet 、GPU处理器的大规模分布式集群 、 OverFeat

针对越来越刁钻的任务，传统的CNN被改变以适应不同的任务
### 3-1.基于区域的卷积神经网络（R-CNN）
R-CNN 通过结合选择性搜索（Selective Search, SS）算法和卷积神经网络（CNN）来有效地进行物体检测。解决了**传统方法在物体检测任务中的精度和效率问题**。R-CNN 主要应用于图像中的物体识别任务，其核心思想是**从图像中提取多个候选区域**，然后对每个区域**分别应用 CNN**进行分类。
### 3-2.Fast R-CNN
通过共享卷积核和RoI池化技术，显著加速了R-CNN的物体检测速度
### 3-3.Faster R-CNN
完全舍弃了选择性搜索算法，允许网络**自主学习对于目标区域的搜索**。并通过预测边界框的交并比（IoU，Intersection-over-Union）衡量区域搜索的好坏，得到比Fast R-CNN更快的检测速度
### 3-4.Mask R-CNN
是 Faster R-CNN 的实际扩展，通过在原有的结构中增加一个额外的分支来预测物体掩码（掩码是一个二值图像--即只有 0 和 1 的图像，其中的 "1" 表示图像中的某个区域或物体，而 "0" 表示背景或不感兴趣的区域。），**这是一个像素级别的操作。**
### 3-5.YOLO
大学生水毕设神器，它的理念就是**只需要一次前向传播**，与在这之前的所有实现都不同。yolo经过多次迭代，其功能日益强大。yolo最大的特点就是**检测速度超级超级快**。使用它，你只要download然后load_model就可以了，超级方便，超级能水毕设
### 3-6.单次多框检测器 (SSD)
一种用于多个区域检测的单次检测器。它在图像上应用额外的小型卷积滤波器，速度更快，且比YOLO更加准确。
### 3-7.Mesh R-CNN
Facebook 提出了一个新颖的 RCNN 方法，称为 Mesh R-CNN，该方法能够将 2D 物体转换为 3D 形状和网格。Facebook 强调了其最新的进展，能够识别复杂的问题。该研究应用了深度学习技术来理解复杂物体的 3D 形状，并提出了新的架构，如边界框、3D体素模式、点云和消息传递，用于预测和定位。是**行业奠基之作**

## 4.一些子任务和解决它们的方法
### 4-1.特征提取、分割与匹配
#### 一.SIFT（尺度不变特征变换）
是一种用于检测和描述图像**局部特征**的计算机视觉算法。通过将图像转换为大量的局部特征向量，SIFT 每次处理一张图像时可生成约 1000 个 SIFT 关键点，并在 1k 毫秒内完成分类。即使图像中存在遮挡，SIFT 依然能够提供高精度的识别结果。
#### 二.DaSNetV2
该方法通过采用 PWP 3D (每像素计数) 方法并利用 NVIDIA CUDA 框架加速推理
#### 三.DORN
一个多尺度网络框架，采用间距递增离散化 (SID) 策略重建深度和深度网络，从而减少了现有特征图的复杂性。

### 4-2.形状变化
#### 一. 3DVP (3D Voxel Pattern)
该方法使用 ACF (Aggregate Channel Features) 检测器来提取每个对象的基本特征
#### 二. C3DPO (Canonical 3D Pose) 网络
该网络可以从单色 RGB 图像中部分重建 3D 对象，从而实现视角变换和对象变形,即便没有训练图像和网格数据
#### 三.Deep MANTA 的框架
Deep MANTA 的核心思想是利用深度卷积神经网络（CNN）来学习和提取目标的多视角几何特征，从而同时完成多个任务。通过端到端的学习框架
#### 四.CenterNet 框架
该框架比传统的边界框检测器和姿态估计方法更简单、更快且更准确。

### 4-3.3D 边界框顶点的 3D 投影
3D 边界框顶点的 3D 投影是指将 3D 边界框的顶点投影到 2D 平面（通常是相机的图像平面）上，以便在 2D 空间中可视化或处理它们的过程。此过程对于对象检测、跟踪和增强现实等应用程序至关重要
#### 一.3DOP (3D Object Proposal)
3DOP 的主要贡献是：
- 利用**深度信息**提升候选区域的精确度。
- 为物体检测提供更可靠的 3D proposals，从而提高后续分类和姿态估计的性能。
- 克服传统 2D region proposal 方法在 3D 场景中的局限性。

3DOP 生成多组 3D 候选框，能够识别 3D 空间中几乎所有对象。该方法引入了对象尺寸、地面平面、深度、多样的空间点密度、框内点密度、可见性及与地面的距离等特征。

#### 二.Mono3D 
Mono3D的主要贡献：
- 不依赖深度图或LiDAR数据，成本更低。
- 通过语义分割增强候选框生成的准确性，提升检测效果。
- 效率较高，虽然不如点云方法精确，但推理速度快

#### 三.DSS（Deep Sliding Shapes）
是一种 3D 汇聚公式方法，它以 RGB-D 图像中的 3D 体积视图为输入，并输出 3D 对象的边界框。
- 该方法首次提出了 3D 区域建议网络（RPN）
- 以及第一个联合对象识别网络（ORN）

#### 四. BB8框架
该框架将 3D 边界框的 8 个角点应用于多输入图像，经过训练，可以预测这些角点的 2D 投影，并通过 PNP 算法从这种 2D-3D 对应关系中计算出 3D 姿态。

#### 五.Mono3OD
它将单个 RGB 图像独特地转换为减少对象检测的形式，同时增加 3D 边界框的置信分数。
####  六.FQNet
可以预测 3D 提议与对象之间的 3D IoU（交并比）。Zhang 等人由此模型提出了一种 3D 对象检测框架，基于单张图像的**端到端**网络，通过确定对象类别、生成 2D 区域提议、2D 位置、位置、尺寸以及 3D 定位来完成检测。
#### 七.Mono-Fenet
这是一种用于 3D 对象检测的强大特征增强方法，其中包括 ROI 均值池化层、PointFE 网络以及通过 3D-NMS 和独特的 RGB 图像进行特征增强网络。该方法通过对 2D 边界框应用一些约束来计算对象的方向和体积，从而输出对象的完整 3D 姿态和尺寸。
#### 八.SS3D 
SS3D 是一种单阶段单目 3D 对象检测器，通过一个代表返回 3D 表征，并用于 3D 边界框的几何形状检测

### 4-4.基于关键点估计的目标检测

#### 一.CornerNet
是一种基于关键点推断的目标检测方法。该方法创新性地将**目标检测任务**转换为**关键点检测任务**，通过检测目标的边界框（Bounding Box）的两个角点（左上角和右下角）来完成目标检测。是key_point模式的奠基之作品。
#### 二.ExtremeNet
它通过检测目标边界框的五个极限点（Extreme Points：顶部、底部、左侧、右侧，以及中心点）来完成目标的检测和定位。该方法进一步拓展了基于关键点推断的目标检测思路，并显著提高了复杂场景中目标检测的性能。
#### 三.CenterNet
它仅选择对象的中心点作为检测关键点。拥有极快的推理速度。


## 5.6D姿态估计的相关
推测物体的六自由度（6D）姿态是一个重要问题，它需要检测物体在相机中心坐标系中的三维方向和三维位置。
三个关键因素：
- 绕 X 轴 左右旋转（滚转，Roll），
- 绕 Y 轴 上下旋转（俯仰，Pitch），
- 绕 Z 轴 前后旋转（偏航，Yaw）。
### 5-0.Perspective-n-Point（PNP）/ Deep-6DPose
PNP是计算机视觉和图形学中最重要的技术之一，基本目标是从给定的 2D 图像坐标和已知的 3D 空间中的物体特征点，计算相机的外部参数（即旋转矩阵和位移向量），进而获得物体的 6D 位姿（即 3D 位置 + 3D 方向），是6D姿态估计的**基石**。
Zhang 等人受到 BB8方法的启发，把PNP算法应用到深度学习，提出了 Deep-6DPose这一历史性的模型。其主要由两个组件构成：RPN 和包含Lie代数的Mask R-CNN。前者用于负责从图像中生成候选区域（Region Proposals），即可能包含物体的图像区域；后者在物体检测和分割的基础上，通过结合 Lie 代数，进一步计算并优化物体的 6D 位姿估计。可以精确地估计物体在 3D 空间中的旋转和位移。
>这是一种端到端的深度学习解决方案，不仅方便快捷，而且封装性好。它的地位之于6D姿态估计就如同《attention is all you need》之于深度学习的地位。

### 5-1.6D-VNet 
该方法的基本技术是利用 RPN（Region Proposal Network）和 2D 目标检测网络（Mask R-CNN）的输出来控制目标物体的 6D 位姿。这些网络通过设计损失函数模型，学习物体的旋转和平移。
### 5-2.基于 RANSAC 的单目RGB模板匹配
在较大且连续的 6D 位姿空间内优化性能。其技术可行性通过一种新的复合稠密 3D 物体坐标形式（包括物体类别标签）得以实现。
### 5-3.SSD-6D
这是一种基于 CNN 的方法，用于从 RGB 图像中检测 3D 物体并准确预测其 6D 位姿。这是一种独特的检测器方法，专门针对合成模型信息进行相关训练，适用于**小型物体**以及具有**多种概念**和实际优势的物体集合。
### 5-4. 6D-RNet
该模型通过单目 RGB 图像预测 6D 物体位姿，采用了带有 ROI 提案的CNN,能够预测 3D 物体的中间轮廓、3D 方向和 3D 平移向量。此外，针对 6D 类别级别的位姿估计，还开发了两种基于边界框（BB）的替代方法，这些方法无需使用 PNP），直接输出 6D 位姿，并结合了 ResNet、RPN和 FCN（全卷积网络）。
### -------------------------------------------------------
(以下为点云估计模型)
### 5-5.MCTS-6D
该方法通过交互式物体候选位姿的笛卡尔积来识别最佳视角，并进行高效搜索，同时为每个物体创建候选位姿集群。首次采用**蒙特卡洛树**搜索技术，在微调过程中进行权衡，并探索新的实例。
### 5-6.POSNN
>**这是一个通用结构**：不仅仅局限于特定物体类型或特定场景，可以适用于多种不同的环境，具有**高可扩展性**和灵活性。**非常适合模型拼接！！！！！！**

POSNN可以用于计算物体的 3D 平移并预测其与相机的距离。此外，该方法通过减少 ShapeMatch-Loss 损失函数，使 POSNN 能够处理对称物体，并使用 VGG16 主干网络来提取特征。
### 5-7.PointPoseNet
是一个用于 6DoF 物体的分类器，提出了使用深度学习对点云中的刚性物体进行推断的思想。该方法在点云系统中进行点对点的对应分配，并通过联合分类和分割进行处理
### 5-8. PVNet
像素级投票网络，用于解决处于严重遮挡下的6D物体姿态识别。通过建立像素级别的投票网络，和基于不确定点的PNP算法（底层使用协方差矩阵完成），极大提高被遮挡物体的识别精度。
### 5-9.ConvPoseCNN 
同时采用PoseCNN和PVNet。它能够避免对单个物体进行裁剪，提供了像素级物体位姿的平移预测和方向模块，并且已经被完整的 CNN 预测网络所替代。同时，它移除了 ROI 池化的方向层，用PVNet代替，通过否定基于像素的向量，并将其用于关键点位置。
### -------------------------------------------------------
（以下为从 RGB-D 图像进行 6D 位姿估计）
### 5-10.场景坐标回归森林
采用RNSAC位姿优化算法，通过 RNSAC 算法评估 SCoRe 森林，从而进行精确的位姿估计。由于 RGB-D 图像中的深度通道有助于提取场景中刚性物体实例的完整 6D 位姿（包括 3D 旋转和 3D 平移），因此该方法的核心目标是通过密集的 3D 物体坐标标签和密集类别对来表示中间结果。
### 5-11.采用Vitruvian Manifold的方法
不直接从 RGB 图像预测 6D 位姿，而是根据图像中物体的坐标进行预测。图像中的每个像素都指向一个标准位置下的物体坐标，称为 VM（Vitruvian Manifold）。在此方法中使用了流行的随机森林算法，并结合几何有效性进行投票。
### 5-12.DenseFusion
结合了 RGB-D 图像中的颜色和深度数据，并将重复的细化方法融入神经网络架构中。
### 5-13.应用 CNN 处理深度图像
>论文名称《DPOD: Dense Pose Object Detector for 6D Object Pose Estimation in RGB-D Images》

通过深度图像处理和几何特征的密集融合来估计 6D 位姿，并使用 PNP 和 RANSAC 算法来计算输入图像和可用 3D 模型之间的密集多类 2D/3D 对应关系映射。
### --------------------------------------------------------
（以下为实例级别的物体姿态检测）
### 5-14.使用局部描述符和基于图像的模型优化
>论文名称为《6D Object Pose Estimation for Grasping in Cluttered Scenes》

该方法结合了著名的RANSAC算法[65]和Mean Shift算法[36]，能够为任何复杂混乱的场景成功推测物体的6自由度（6-DOF）姿态。此外，它可以处理随机复杂的非规则物体，具有强大的抗干扰能力，可处理遮挡问题，并能够控制光照、尺度和旋转变化。
### 5-15.LieNet
这是一种基于模板的独特姿态估计方法，利用Lie代数旋转矩阵来估算物体的旋转矩阵，并通过预测物体与相机中心的距离来估算平移向量。该方法以图像作为输入，输出物体的识别结果，包括6D姿态（位置和方向）、边界框、标签以及分割掩码。
### 5-16.基于点对特征（PPF）
该方法最初由Drost提出，分为全局建模和局部匹配两个层次。主要结构确定了每个场景的旋转点、模型点以及角度。Vidal工作的扩展体现在自由形式物体姿态的概念上，这在支持高度复杂的自动化系统中具有重要意义。
### --------------------------------------------------------
（以下为类级别的物体姿态检测）
### 5-17.基于NOCS的新方法
NOCS是一种类别级的3D坐标表示，它将一个类别的所有可能物体实例（如所有椅子或所有杯子）映射到一个统一的、归一化的3D坐标空间。目的是解决类别内物体实例的多样性问题（如不同尺寸、形状、纹理等）。
这一方法在RGB-D图像中**无需物体的CAD模型**即可推测隐藏物体实例的6D姿态
### --------------------------------------------------------
（以下没有统一的应用限制）
### 5-18.基于强化学习方法
为了求解6D物体姿态假设并确保最高的准确性，Krull首次成功将强化学习应用于姿态代理分类。此方法的每个决策都遵循基于随机策略梯度方法的潜在分布，该方法直接通过感兴趣的预期损失的梯度进行优化。
### 5-19.模板匹配技术（Template-Matching Techniques）
Hinterstoisser等人构建了一个名为LineMod的框架，该框架基于最新的模板匹配方法，能够自动检测和跟踪3D物体。该方法利用深度图和彩色图像，从不同角度生成物体的模板，以捕捉物体的特征和3D形状。3D模型还可以用于物体位置的精确估计。
### 5-20.基于补丁（patch-based）的框架
该框架引入了一种Latent-Class Hough Forests方法，用于3D物体检测，并在强遮挡和杂乱环境中进行姿态估计。
### 5-21.基于CNN/深度学习的一系列方法，这些方法大都是由某一种方法优化而来，或者仅仅提出一种method而没有完整的模型
- Krull 提出了一种6D姿态估计模型，使用卷积神经网络（CNN）进行图像映射，并表明单一物体的训练数据足以使CNN能够泛化到图像中所有不同的物体和背景。
- Rangesh等人 提出了一种适用于地面物体的3D识别框，结合了2D视觉上下文、3D维度和地平面信息。
- Google宣布发布MediaPipe，这是一种3D物体检测管道，能够识别2D图像中的日常物体，并估计其姿态和大小。（使用TensorFlow开发）
- 一项新模型被设计用于从单目RGB图像中预测物体的姿态和大小，该模型采用了一种多任务学习方法，名为MobileNetv2，可以预测物体大小。高斯回归任务则使用了EPnP，计算出最终的3D边界框坐标。
- Tremblay等人提出了首个针对机器人操控的一次性深度神经网络（one-shot deep neural network），该网络仅使用合成数据进行训练，能够实现3D物体的6自由度（6-DoF）姿态估计。该系统名为DOPE（Deep Object Pose Estimation），结合了透视N点（PNP）算法，将3D边界框与2D图像融合。
- Li提出了一个姿态修正算法，该算法通过观察物体从相机中心线的视角，对姿态进行修正。
### 5-22.模板聚类方法 City-LineMod /Cognitive Template-Clustering Line Mode
该方法利用7维（4D几何+3D纹理）认知特征向量，在补丁-线模式（patch-linemode）聚类方法中还原标准的3D空间点。
此外，不同3D空间点之间的距离也会受到4D附加信息（包括特征的方向和宽度）的影响，从而实现更精确的模板聚类。
### 5-23.混合姿态方法 MOPED
Martinez等人提出了一种基于混合GPU/CPU架构的系统，称为MOPED（多物体位置估计与检测，Multiple Object Position Estimation and Detection）。
该系统利用全层次的并行性，是一种用于物体识别和断裂估计的高效且可测量的感知方法。

此外，该系统还基于另一种称为POSESEQ的物体识别算法，表现出显著的扩展性和准确性提升，同时优化了算法的速度。

# 相关数据集
![数据集](https://i-blog.csdnimg.cn/direct/6627d132573443748dbeb82792fa3513.png)

# 相关模型评价指标
![评价指标](https://i-blog.csdnimg.cn/direct/2176828d98fe4239bacf764e4a733026.png)

# 3D 对象检测论文集
![论文集合](https://i-blog.csdnimg.cn/direct/9659576551ea4298850773b988e54e38.png)




