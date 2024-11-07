.. openEuler-ros-document documentation master file, created by
   sphinx-quickstart on Thu Aug 22 16:15:03 2024.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

openEuler ROS 使用与开发手册
==================================================

openEuler是一款基于Linux内核的开源操作系统，适用于服务器、数据库、大数据、云计算、人工智能等应用场景。同时，openEuler是一个面向全球的操作系统开源社区，通过社区合作，打造创新平台，构建支持多处理器架构、统一和开放的操作系统，推动软硬件应用生态繁荣发展。

ROS（Robot Operating System，机器人操作系统）是一个用于开发机器人软件的开源框架，为机器人开发提供了消息传递、硬件抽象、设备驱动、算法库、可视化开发调试工具以及包管理等关键功能，使开发人员能够快速创建复杂和稳健的机器人应用。

2007年，ROS诞生于Willow Garage公司和斯坦福大学人工智能实验室。2010年第一个ROS正式版发布，随后ROS版本迭代频繁，逐渐成为应用最为广泛的开源机器人操作系统，并发展成了全球最大的机器人开源社区。2020年ROS1发布了最后一个版本Noetic，支持到2025年。ROS2从ROS1的基础上重新设计，在实时性、分布式多机、安全性、跨平台等方面均有较大提升。

openEuler ROS sig成立于2020年6月，主要目标是将ROS1/ROS2移植到openEuler系统并提供后续支持。经过4年的发展，在其他sig组协助以及社区开发者的共同努力下，ROS sig已经完成1000加软件包的移植适配工作，并随openEuler发版定期发布ROS版本，支持一键安装。

本文档是openEuler ROS的使用与开发手册，包含了openEuler ROS的安装、使用、基础开发教程以及如何参与sig组做贡献等内容。

==================================================

.. toctree::
   :maxdepth: 1
   :caption: 安装openEuler ROS
   
   installation/install-guide.md
   installation/QEMU-install-oe.md
   installation/install-ros-humble.md
   installation/Intel-nuc-install-oe.md
   installation/vm-install-oe.md
   installation/lpi4a-rv-oe.md
   installation/Raspberry-pi-oe.md

.. toctree::
   :maxdepth: 1
   :caption: 基础教程
   
   tutorials/guide.md
   tutorials/base_demo_01.md
   tutorials/use-turtlesim.md
   tutorials/Understanding-ROS2-Services.md
   tutorials/Understanding-ROS2-Actions.md
   tutorials/Understanding-ROS2-Topics.md
   tutorials/test_ROS2_beginner2/Using colcon to build packages.md
   tutorials/test_ROS2_beginner2/Creat_package.md
   tutorials/Rviz_Visualization.md

.. toctree::
   :maxdepth: 1
   :caption: 建图与导航
   
   slam-nav/guide.md
   slam-nav/slam_gmapping.md
   slam-nav/stage_ros.md

.. toctree::
   :maxdepth: 1
   :caption: 其他教程
   
   other-tutorials/qt-ros-dev.md
   other-tutorials/webots.md
   other-tutorials/usb-cam.md
   other-tutorials/usb-cam-build.md
   
.. toctree::
   :maxdepth: 1
   :caption: 如何参与社区贡献
   
   how-to-contribute/become_contributor.md
   how-to-contribute/docs.md
   how-to-contribute/ros-porting-tools.md
   how-to-contribute/eulermaker.md

   
.. toctree::
   :maxdepth: 1
   :caption: ROS1相关与历史文档
   
   ros1/guide.md
   ros1/ROS-instro.md
   ros1/Navigation_Move_Base-Read.md
   ros1/Navigation_Rotate_Recovery-Read.md
   ros1/openEuler2009-and-2103-install.md
   ros1/openEuler2103-ROS1-OBS-package.md
   ros1/openEuler-2103-ROS1-video.md
   ros1/openEuler2103-ros_comm-packageing.md
   ros1/openEuler2103-ROS2-OBS.md
   ros1/openEuler2103-ROS2-video.md

