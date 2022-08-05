# ROS SIG问题指南



###	（1）ROS包移植步骤

ROS基础软件包以及其上下游的依赖包移植，是open-Euler社区ROS SIG要完成的主线工作。这部分工作的意义就是让ROS及其相关软件在open-Euler操作系统上成功稳定的运行起来，扩展社区的上层应用生态，使得社区的软件和硬件环境可以有更多的扩展性。

- step 1

检查open-Euler源上是否已经存在了相关的软件包，检查ROS-SIG已移植的软件包列表，是否已经存在该包，避免重复工作。

https://gitee.com/openeuler/community/tree/master/sig/sig-ROS

- step 2

接下来就要将ROS包进行本地构建，具体的操作文档可以参考社区贡献者已有教程文档：

[OpenEuler21.03 本地打包ros_comm并验证](https://gitee.com/openeuler/ros/blob/master/document/OpenEuler21.03%20%E6%9C%AC%E5%9C%B0%E6%89%93%E5%8C%85ros_comm%E5%B9%B6%E9%AA%8C%E8%AF%81.md)

[OpenEuler21.03 ROS1 OBS打包指南](https://gitee.com/openeuler/ros/blob/master/document/OpenEuler21.03%20ROS1%20OBS%E6%89%93%E5%8C%85%E6%8C%87%E5%8D%97.md)

[OpenEuler21.03 ROS2 OBS打包指南](https://gitee.com/openeuler/ros/blob/master/document/OpenEuler21.03%20ROS2%20OBS%E6%89%93%E5%8C%85%E6%8C%87%E5%8D%97.md)

需要注意的是可能要对齐依赖库的版本问题，Python版本问题，以及CI门禁的相关问题。有关社区的CI门禁，请参考:

[门禁功能指导手册](https://www.openeuler.org/zh/blog/zhengyaohui/2022-03-21-ci_guild.html)

### （2）问题论坛

如果在开源的工作中遇到了困难，可以前往社区的互动平台，查找是否有相关的解决方案。社区博客：https://www.openeuler.org/zh/interaction/blog-list/

同时，社区也有丰富的文档供你学习，比如open-Euler 21.03的安装：https://docs.openeuler.org/zh/docs/21.03/docs/Quickstart/quick-start.html

友情提示：社区内的文档偶尔也存在部分错误，需要自己亲自尝试并探索解决方案。

