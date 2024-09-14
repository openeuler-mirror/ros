# openEuler上搭建QT+ROS开发环境

## 一、QT，QT Creator，ROS plugin，下载安装

QT Creator是基于 QT环境的 IDE开发工具，QT Creator可以兼任不同版本的 QT环境，

但是有最低QT版本限制，OpenEuler自带QT5。我下载的是清华源 QT Creator 14 linux版 ，[https://mirrors.tuna.tsinghua.edu.cn/qt/official_releases/qtcreator/14.0/14.0.0/](https://)

![1724837555306](image/qt-ros-dev/1724837555306.png)

下载后双击运行QT Creator 安装程序，点击 直接运行

![1724836017939](image/qt-ros-dev/1724836017939.png)

![1724837744811](image/qt-ros-dev/1724837744811.png)

显示QT Creator安装配置选项，需要先在QT官网上注册账号，请自行注册

![1724837911255](image/qt-ros-dev/1724837911255.png)

选择安装目录，组件建议全部点上，

![1724837992887](image/qt-ros-dev/1724837992887.png)![1724838089226](image/qt-ros-dev/1724838089226.png)![1724838112243](image/qt-ros-dev/1724838112243.png)

安装完成后，进入QT Creator 程序目录，

![1724838291402](image/qt-ros-dev/1724838291402.png)

在github上下载对应的 ROS plugin，我使用的是Creator14，

故选择 ROS Project Manager-14.1-Linux-x86_64-14.0版本，（注意：别下成源码Source code了）

[https://github.com/ros-industrial/ros_qtc_plugin/releases](https://)
![1724833583029](image/qt-ros-dev/1724833583029.png)

![1724838459894](image/qt-ros-dev/1724838459894.png)

启动程序 qtcreator 在安装目录的bin文件夹下，如图，双击qtcreator，点在终端运行

![1724839393056](image/qt-ros-dev/1724839393056.png)

安装ROS插件，点击 帮助->关于插件，选择ROS插件压缩包，一直点下一步，完成

![1724838963947](image/qt-ros-dev/1724838963947.png)

![1724838880170](image/qt-ros-dev/1724838880170.png)

![1724838864313](image/qt-ros-dev/1724838864313.png)

![1724858125589](image/qt-ros-dev/1724858125589.png)

![1724858180654](image/qt-ros-dev/1724858180654.png)

点击导航栏标签  帮助-> 关于插件，显示如下 ROSProjectManager 安装完成，如下

![1724836388729](image/qt-ros-dev/1724836388729.png)

## 二、环境配置

下载qt开发环境，一般无需手动安装，dnf install  qt5-devel

使用dnf list installed | grep "qt" 查看环境如下,仅供参考，有的包没有并不影响运行：

![1724858811579](image/qt-ros-dev/1724858811579.png)

![1724858830521](image/qt-ros-dev/1724858830521.png)

点击创建项目，选择ROS工作空间，填写项目名称，项目路径

![1724859876187](image/qt-ros-dev/1724859876187.png)

这里注意 build system选择 colcon ， 因为 使用的是ROS2框架

![1724860027812](image/qt-ros-dev/1724860027812.png)![1724861268443](image/qt-ros-dev/1724861268443.png)

![1724860082931](image/qt-ros-dev/1724860082931.png)

项目创建完成后，在工作空间创建src文件夹，执行colcon build命令，完成后如下图：

（右键点击，项目栏，点击 在此打开终端，加载出控制台）

![1724860337639](image/qt-ros-dev/1724860337639.png)

提示找不到QT 环境，这里要手动设置 qmake 路径下，请进行如下配置：

![1724860693534](image/qt-ros-dev/1724860693534.png)

点击 最左侧菜单->项目，点击 管理构建套件，配置QT编译环境如下：

![1724859812804](image/qt-ros-dev/1724859812804.png)

点击 QT版本->添加，将需要的QT编译器 添加进去，/usr/lib64/qt5/bin/qmake

![1724860993311](image/qt-ros-dev/1724860993311.png)

![1724859832643](image/qt-ros-dev/1724859832643.png)

编译 环境变量，需要手动配置，需要如下几个变量 使用比较麻烦，建议 使用控制台进行编译

![1724861419425](image/qt-ros-dev/1724861419425.png)

添加 运行配置，可点击左下角运行按钮 快捷执行命令

![1724991284201](image/qt-ros-dev/1724991284201.png)

点击运行

![1724991373077](image/qt-ros-dev/1724991373077.png)


## 三、添加节点，运行测试

使用ROS插件，添加节点，可以快捷创建 初始模板，右键src目录，点击 添加新文件

![1724861744771](image/qt-ros-dev/1724861744771.png)

选择package，填写 包名，必须 手动点击预览 手动选择 一次路径 ，才能点击一下步

![1724861868430](image/qt-ros-dev/1724861868430.png)

![1724862168892](image/qt-ros-dev/1724862168892.png)

填入参数，可参考如下

![1724991115969](image/qt-ros-dev/1724991115969.png)

完成后，目录结构如下，（如果无法创建出package ，可能是项目创建问题，请参考其他博客）

![1724862530776](image/qt-ros-dev/1724862530776.png)

下图是点击创建 工业机器人支持包，创建出的工程

![1724862472630](image/qt-ros-dev/1724862472630.png)

在工作区路径下（就是install存在的路径下），执行colcon build命令，会编译所有包，

![1724862950356](image/qt-ros-dev/1724862950356.png)

编译成功后，source执行install目录底下的setup.bash文件，将编译后的程序，加到ros2环境里

ros2 run 包名 双击tab  ， 可以显示 当前包下 可执行的节点有哪些

![1724863869287](image/qt-ros-dev/1724863869287.png)

ros2 launch 包名 双击tab   ，  可以显示 当前包下 可行的脚本有哪些

![1724863988409](image/qt-ros-dev/1724863988409.png)

ros2 run 包名 程序名  ， 执行程序

ros2 launch 包名 脚本名， 执行脚本

![1724864155718](image/qt-ros-dev/1724864155718.png)

![1724865605240](image/qt-ros-dev/1724865605240.png)
