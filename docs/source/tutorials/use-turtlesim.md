# 测试小乌龟

## 开始前的准备

 - [安装 ROS 2 Humble](../installation/install-ros-humble.md)
 - 安装 colcon
 - [创建 colcon 工作区](./creating-a-colcon-workspace.md)

## TurtleSim 相关概念

### TurtleSim 坐标系

在 `turtlesim` 中，坐标系定义如下：

- **世界坐标系**：`turtlesim` 的世界坐标系是一个二维平面，原点位于左下角 `(0, 0)`，坐标范围从 `(0, 0)` 到 `(11.08, 11.08)`

 - **X 轴**：沿着水平向右增加
 - **Y 轴**：沿着垂直向上增加

每只乌龟的姿态包括位置 `(x, y)` 和朝向 `theta`，`theta` 是一个表示乌龟相对于 X 轴的角度（以弧度为单位）

### 小乌龟的控制方式

在 `turtlesim` 中，控制乌龟的主要方式包括：

- **速度控制**：通过发布 `geometry_msgs::msg::Twist` 消息到 `/turtle1/cmd_vel` 话题，控制乌龟的线速度和角速度

 - `linear.x` 控制乌龟的前进或后退速度
 - `angular.z` 控制乌龟的旋转速度

- **服务调用**：ROS 2 提供服务调用来执行特定操作，如生成新乌龟、传送乌龟到指定位置等

 - `/spawn` 服务：生成新的乌龟，并设置其初始位置和朝向
 - `/teleport_absolute` 服务：将乌龟传送到指定的绝对位置
 - `/teleport_relative` 服务：相对于乌龟的当前位置进行传送

### 获取小乌龟的位置信息

虽然 `turtlesim` 不提供传统的里程计（odom）话题，但可以通过订阅 `/turtle1/pose` 话题获取乌龟的当前位置和朝向信息，达到类似里程计的效果。`/turtle1/pose` 发布的是 `turtlesim::msg::Pose` 消息，包含以下字段：

 - `x` 和 `y`：乌龟的当前位置
 - `theta`：乌龟的朝向角度
 - `linear_velocity` 和 `angular_velocity`：乌龟的线速度和角速度

## 编写ROS节点程序

我们创建的包目录`test01`下，有几个文件，我们首先修改`package.xml`，这个文件主要是用来描述我们的包需要的依赖项

### 修改 `package.xml`

确保`package.xml`里面有下面这几项，这是我们的构建依赖

```xml
  <depend>rclcpp</depend>
  <depend>geometry_msgs</depend>
  <depend>turtlesim</depend>
```

### 修改 `CMakeLists.txt`

然后修改`CMakeLists.txt`，我们要给`test01`包里的每一个程序都添加构建项目

首先我们给第一个C++节点`rectangular_motion.cpp`添加下面的描述

```cmake
# find dependencies
find_package(ament_cmake REQUIRED)
find_package(rclcpp REQUIRED)
find_package(geometry_msgs REQUIRED)
find_package(turtlesim REQUIRED)
find_package(nav_msgs REQUIRED)

# Add executable
add_executable(rectangular_motion src/rectangular_motion.cpp)
ament_target_dependencies(rectangular_motion rclcpp std_msgs geometry_msgs nav_msgs turtlesim)

# Install the executable
install(TARGETS
  rectangular_motion
  DESTINATION lib/${PROJECT_NAME})
```

这样就可以正常构建我们的C++程序了

如果要添加python脚本，需要在`CMakeLists.txt`中添加下面的几项

```cmake
# find dependencies
find_package(rclpy REQUIRED)

install(
  PROGRAMS
  scripts/turtle_spawner.py
  DESTINATION lib/${PROJECT_NAME}
)
```

这样colcon也能正确配置我们的python脚本

### 构建节点程序

回到工作区目录`~/ros2_ws`下运行

```bash
cd ~/ros2_ws
colcon build
```

编译完成后可以使用`ros2 run test01 <program-name>`调用我们写的节点

如果提示找不到程序，确保当前终端执行过`source ~/ros2_ws/install/setup.bash`设置环境

## 参考链接

 - [文章中的代码地址](https://github.com/discodyer/shutsuryoku/tree/test01/test01)
 - [Developing a ROS 2 package](https://docs.ros.org/en/humble/How-To-Guides/Developing-a-ROS-2-Package.html)
