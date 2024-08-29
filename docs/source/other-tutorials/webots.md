# openEuler上安装测试Webots仿真软件

## Webots 简介
[官网](https://cyberbotics.com/)  [仓库](https://github.com/cyberbotics/webots) [文档](https://cyberbotics.com/doc/guide/menu) 

Webots 是一款免费，开源的 3D 机器人模拟软件，可以模拟各种机器人，包括两轮机器人、工业手臂、腿式机器人、模块化机器人、汽车、无人机、自主水下航行器、履带式机器人、航空航天器等。  
Webots 核心基于现代 GUI 框架 Qt、物理引擎 (ODE fork) 和 OpenGL 3.3 渲染引擎 (wren) 的组合。它可以在 Windows、Linux 和 macOS 上运行。

## 构建
Webots 目前仅提供 x86_64 架构支持  
### 下载源码
```bash
git clone --recurse-submodules -j8 https://github.com/cyberbotics/webots.git
cd ./webots
```
### 依赖安装
```bash
dnf install openeuler-lsb cmake swig mesa-libGL glib2-devel freeimage-devel freetype-devel libxml2-devel boost-devel libssh-devel libzip readline-devel pbzip2 wget zip unzip glm-devel stb_image-devel stb_image_write-devel
```
### 构建
这一步会根据当前环境下载对应的依赖，需要良好的网络环境
```bash
make -j8
```
构建完成后直接运行项目目录下生成的 `webots` 脚本即可，需要在桌面环境下运行。

## 测试运行
### 设置环境变量
```
export WEBOTS_HOME={webots_dir}
```

### 运行 Demo 测试
初次运行命令时，如果没有设置 Webots 目录对应的环境变量，会提示是否自动安装 Webots。选择是则会自动下载最新稳定版本并设置环境变量，从源码安装的是 nightly 版本，二者版本不同为正常现象。

[webots_ros2 官方文档](https://github.com/cyberbotics/webots_ros2/wiki/Examples) 提供了许多 demo 可用于测试，此处仅提供一个最简单的示例。

```bash
ros2 launch webots_ros2_universal_robot multirobot_launch.py
```

## 参考资料
- [Webots Linux 构建](https://github.com/cyberbotics/webots/wiki/Linux-installation/)
- [webots_ros2 官方文档](https://github.com/cyberbotics/webots_ros2/wiki/Examples)