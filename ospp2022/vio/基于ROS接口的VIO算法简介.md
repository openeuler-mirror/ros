# 基于ROS接口的VO/VIO算法简介

### （1）SVO1.0算法

SVO（Semi-Direct Monocular Visual Odometry）半直接法视觉里程计，是苏黎世大学机器人感知组的克里斯蒂安.弗斯特于2014年ICRA会议上发表， 并在github上开源：https://github.com/uzh-rpg/rpg_svo。

所谓半直接是指通过对图像中的特征点图像块进行直接匹配来获取相机位姿，而不像直接匹配法那样对整个图像使用直接匹配。 虽然semi-direct方法使用了特征，但它的思路主要还是通过direct method来获取位姿，这和feature-method不一样。同时，semi-direct方法和direct method不同的是它利用特征块的配准来对direct method估计的位姿进行优化。 通俗点说，就是结合了特征点法和直接法的视觉里程计。

算法优点：

- 稀疏直接法，运行速度非常快；
- 轻量化，可运行在嵌入式程序中，适配硬件广泛；

缺点：

- 算法在平视场景容易丢失跟踪，且无法恢复；
- 对光照环境有一定要求，在运动速度过快等条件下极容易丢失；

### （2）SVO2.0算法

2021年7月14日苏黎世大学SVO2.0开源，论文：https://rpg.ifi.uzh.ch/docs/TRO17_Forster-SVO.pdf。

它是机器人和感知小组 (RPG) 开发的最新版本的半直接视觉里程计 (SVO)。如 SVO 论文 (TRO-17) 中所述，SVO 是作为一个快速且多功能的视觉前端而诞生的。从那时起，通过各种研究和工业项目整合了不同的扩展。 SVO Pro 支持不同的相机型号、主动曝光控制、基于滑动窗口的后端以及带闭环的全局束调整。

SVO2.0特点：

- 视觉里程计：最新版本的 SVO，支持单目或立体设置中的透视和鱼眼/折反射相机。它还包括主动曝光控制；
- 视觉惯性里程计：SVO 前端 + 视觉惯性滑动窗口优化后端（由 OKVIS 修改）；
- 视觉惯性 SLAM：SVO 前端 + 视觉惯性滑动窗口优化后端 + 全局捆绑调整地图（使用 iSAM2）。借助 iSAM2，全球地图实时更新，并用于以帧速率进行定位；
- 具有闭环的视觉惯性 SLAM：通过 DBoW2 将闭环集成到全局束调整中。姿势图优化也被包括在内，作为全局捆绑调整的轻量级替代品。

![v102_gm.gif](https://github.com/uzh-rpg/rpg_svo_pro_open/raw/master/doc/images/v102_gm.gif)

### （3）ORB_SLAM1-3算法

ORB_SLAM 是西班牙 Zaragoza 大学的 Raúl Mur-Arta 编写的视觉 SLAM 系统。 它是一个完整的 SLAM 系统，包括视觉里程计、跟踪、回环检测，是一种完全基于稀疏特征点的单目 SLAM 系统，同时还有单目、双目、RGBD 相机的接口。其核心是使用 ORB (Orinted FAST and BRIEF) 作为整个视觉 SLAM 中的核心特征。经过数年的发展，ORB_SLAM也已经发展了三代：

开源代码：

- https://github.com/raulmur/ORB_SLAM；
- https://github.com/raulmur/ORB_SLAM2；
- https://github.com/UZ-SLAMLab/ORB_SLAM3；

ORB_SLAM是一种基于特征匹配的单目SLAM系统，该算法带有回环和重定位功能，所以前端的VO和后端的回环检测构成了整体的SLAM系统。ORB_SLAM2则在ORB_SLAM的基础上扩展到了单目、双目、RGB-D相机全部支持，包含地图复用、回环和重定位功能，其中轻量级的定位模式，利用VO跟踪未建图的区域，并与地图点匹配，允许零漂移定位。ORB_SLAM3则将相机阵营扩展到了针孔相机和鱼眼相机，并且融合了惯性导航单元，构建了一个紧耦合的VIO系统，鲁棒性有了巨大的提升。第二个创新点是多地图系统，它依赖于一种新的位置识别方法和改进版本的回环检测。这两点保证算法在弱视觉的场景下也能成功运行。

demo:https://www.youtube.com/watch?v=HyLNq-98LRo&feature=youtu.be

### （4）DSO和LDSO算法

DSO（Direct Sparse Odometry），是慕尼黑工业大学（Technical University of Munich, TUM）计算机视觉实验室的雅各布.恩格尔（Jakob Engel）博士，于2016年发布的一个视觉里程计方法。DSO属于稀疏直接法的视觉里程计。它不是完整的SLAM，因为它不包含回环检测、地图复用的功能。因此，它不可避免地会出现累计误差，尽管很小，但不能消除。DSO目前开源了单目实现。

DSO是少数使用纯直接法（Fully direct）计算视觉里程计的系统之一。相比之下，SVO[2]属于半直接法，仅在前端的Sparse model-based Image Alignment部分使用了直接法，之后的位姿估计、bundle adjustment，则仍旧使用传统的最小化重投影误差的方式。而ORB-SLAM2，则属于纯特征法，计算结果完全依赖特征匹配。从方法上来说，DSO是新颖、独树一帜的。

LDSO则是在DSO基础上加入了闭环检测模块，算法的鲁棒性和精确度得到了进一步的提升。

开源代码：

- https://github.com/JakobEngel/dso；
- https://github.com/tum-vision/LDSO；

### （5）LSD算法

LSD-SLAM是一种新颖、直接的单目SLAM技术:它不使用关键点，而是直接对图像强度进行操作，用于跟踪和映射。相机跟踪使用直接图像对齐，而几何估计形式的半密集深度地图，通过过滤许多像素立体比较获得。然后，我们构建关键帧的Sim(3)姿态图，这允许构建经过比例漂移修正的大规模地图，包括循环闭包。LSD-SLAM可以在CPU上实时运行，甚至可以在现代智能手机上运行。

开源代码：https://github.com/tum-vision/lsd_slam；

LSD-SLAM系统整个过程分为三大模块，第一个模块是Tracking,第二个模块是Depth Map Estimation, 第三个模块是Map Optimization

[![image](https://github.com/DroidAITech/vslam-evaluation/raw/master/LSD/Installation%20documentation/images/LSD.png)](https://github.com/DroidAITech/vslam-evaluation/blob/master/LSD/Installation documentation/images/LSD.png)

1.跟踪部分连续地跟踪摄像机获取的图像，即估计相对于当前的关键帧的刚体变换位姿，其中初始值为前一帧的位姿。
2.深度地图估计部分将跟踪的帧用于精化或者替代当前关键帧。通过对像素小基线立体的比较滤波和空间规则化来精化深度。如果相机移动过快，通过将已存在的邻近的关键帧的点投影到当前帧来初始化一个新的关键帧。
3.地图优化部分将作为当前跟踪参考帧的关键帧整合到全局地图中，该关键帧的深度地图将不再进行精化。此外，使用尺度感知的直接图像配准方法估计相对于临近关键帧的相似变换，实现回路闭合和尺度漂移的检测。

### （6）VINS-Mono算法

VINS-Mono是香港科技大学沈劭劼团队于2018年开源的单目视觉惯导SLAM方案。是视觉与IMU融合中的经典之作，其定位精度可以媲美OKVIS，而且具有比OKVIS更加完善和鲁棒的初始化以及闭环检测过程。

VINS-Mono是基于优化和滑动窗口的VIO，使用IMU预积分构建紧耦合框架。并且具备自动初始化，在线外参标定，重定位，闭环检测，以及全局位姿图优化功能。由数据预处理、初始化、后端非线性优化、回环检测和全局位姿图优化五部分组成。

![VINS-Mono　框架图](https://github.com/DroidAITech/vslam-evaluation/raw/master/VINS/Installation%20documentation/pics/VINS-Mono%E3%80%80%E6%A1%86%E6%9E%B6%E5%9B%BE.png)

VINS-Mono是目前开源SLAM里面效果非常不错的Visual和Inertial融合SLAM算法, 通过Sliding Windows优化进行视觉和IMU的紧耦合实现VIO, 此外还加了DBoW做Loop Closure, 算是一套完整的SLAM系统. 该算法的前端比较简单,就Harris角点加LK光流跟踪, Loop Closure也挺常规, 算法的厉害之处在于后端Visual Inertial融合优化部分, 也就是VIO部分, 这是VINS算法的核心。

但是，从实际运行效果来看：

（1）vins纯旋转会飘，所以初始化必须平移加旋转，并且是快速地移动，当然移动是包括平移和旋转的；

（2）初始化相对来讲有些麻烦，因为每次启动都要进行初始化；

（3）运行过程中，视觉跟踪失败即一小段没有特征点或者移动太快的时候IMU会飘的很离谱；

（4）尺度信息是依赖IMU的预积分值，而不是直接获取的，如此一来，就会产生误差，影响精度和鲁棒性；