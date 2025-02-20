# 在 openEuler 上移植并测试 ROS Jazzy：以“小乌龟 (turtlesim)” 为例

## 概述

伴随 ROS（Robot Operating System）在开源机器人领域的持续发展，愈来愈多的操作系统开始尝试支持最新的 ROS 2 版本。在 **openEuler** 上成功运行 **ROS 2 Jazzy** “小乌龟（turtlesim）”示例，则标志着企业级操作系统与开源机器人生态进一步融合。本文介绍了项目的背景、意义以及具体的移植与使用方法，并对 **ROS 2 Jazzy Jalisco** 这一长期支持（LTS）版本的核心价值和技术影响进行了简要解析，帮助读者快速上手在 openEuler 环境中体验“小乌龟”。

---

## 背景与动机

**ROS** 是业界应用最广泛的开源机器人软件框架，囊括了海量的功能包与庞大的社区力量。从学术研究到工业应用，ROS 不仅提供了灵活的通信机制和硬件抽象层，也因社区驱动而拥有旺盛的生态活力。随着 **ROS 1**（Melodic、Noetic 等版本）逐步进入维护收尾阶段，**ROS 2** 系列正成为未来主流，尤其在实时性、跨平台兼容和工业安全性等方面都有大幅提升。

**ROS 2 Jazzy Jalisco**（以下简称 *Jazzy*）作为 ROS 2 的第十个正式发行版，于 2024 年 5 月 23 日正式发布，被定位为**长期支持（LTS）** 版本，官方维护期将持续至 2029 年 5 月。这体现了开源机器人基金会（OSRF）对工业应用稳定性的重视，为学术界和产业界留出了五年的技术演进与升级窗口。  
**Jazzy** 继承了 ROS 2 的分布式架构理念，并升级至 **RTI Connext 6.1.1** 版本以改善大规模节点通信的延迟抖动问题。同时，预览支持 **rmw_zenoh** 中间件，为云端—边缘端协同提供新的广域网通信选择。  
目前，openEuler 上的最新 ROS 支持版本为 **Humble**。从长期维护角度和新功能引入的角度来看，移植 **Jazzy** 对 openEuler 社区和机器人开发者而言都有重要价值。本次我们在移植过程中完成了大量适配与测试，并最终以“小乌龟（turtlesim）”示例进行验证，满足了社区用户对多系统支持与企业级环境的需求。

---

## 项目说明

本项目旨在将 **Jazzy** 版本移植至 **openEuler**，过程中我们积累并整合了许多针对 ROS 包构建的经验与知识。接下来将以一系列文章陆续分享调研成果与实践过程，希望能够帮助更多开发者在其他操作系统环境中高效构建 ROS 软件包。

---

## 启用软件源

1. **创建 .repo 文件**  
   首先在 `/etc/yum.repos.d` 目录下创建一个新的 `.repo` 文件，用于添加软件源。示例命令如下：
   ```bash
   cd /etc/yum.repos.d
   sudo vi openEuler-jazzy.repo
   ```
2. **编辑 repo 文件**  
   在 `openEuler-jazzy.repo` 中填写以下内容（可根据实际情况修改仓库地址）：
   ```ini
   [openEuler-Jazzy]
   name=openEuler-Jazzy
   baseurl=https://eulermaker.compass-ci.openeuler.openatom.cn/api/ems1/repositories/jazzy_ament_package/openEuler%3A24.03-LTS/x86_64/
   enabled=1
   gpgcheck=0
   ```
3. **刷新并更新系统仓库**  
   由于新增了 repo 文件，需要清理并生成缓存：
   ```bash
   sudo dnf clean all
   sudo dnf makecache
   sudo dnf update --nobest
   ```

---

## 安装 ROS Jazzy

在成功添加仓库后，即可使用 `dnf` 命令安装 ROS Jazzy。  

```bash
sudo dnf install "ros-jazzy*" --skip-broken -y
```

由于仓库中仍存在部分冲突包，执行上述命令时可能会出现大量 `conflict` 报错，这属于 **预期行为**。`dnf` 会根据指令自动安装可行的包，无法安装的包将被跳过；对于仅想快速测试“小乌龟”示例的用户，完全可以忽略此报错。

---

## 运行“小乌龟”示例

1. **激活 ROS 环境**  
   在安装完 ROS 后，需要激活环境脚本：
   ```bash
   source /opt/ros/jazzy/setup.zsh
   ```
   > 若使用 `bash`，则将 `setup.zsh` 替换为 `setup.bash`。

2. **启动 turtlesim 节点**  
   打开一个终端窗口，执行：
   ```bash
   ros2 run turtlesim turtlesim_node
   ```

3. **开启键盘控制**  
   在另一个终端窗口中，执行：
   ```bash
   ros2 run turtlesim turtle_teleop_key
   ```
   随后在该终端中使用方向键或 `WASD` 控制屏幕上的小乌龟移动。如果界面正常弹出并可以移动，说明示例运行成功！

   **示例界面**：  
   ![turtlesim demo](./image/openEuler-turtlesim-example/turtlesim.png)

---

## 其他说明

1. **dnf 更新时出现 tbb 冲突**  
   在使用 `dnf update` 时，如果出现针对 **tbb** 的冲突提示，是因为 openEuler 中自带的 tbb 包与 ROS Jazzy 所需版本并不完全一致。此时可以放心忽略提示，安装流程仍可继续进行。

2. **下载包时出现大量 conflict**  
   在安装 ROS Jazzy 相关包的过程中，可能出现多条 conflict 提示。当前仓库重点满足跑通“小乌龟”示例的需求，其余部分还在逐步完善中。只要关键组件能正确安装，“小乌龟”运行不受影响。

---

## 其他相关文档

我们计划后续陆续发布更多与 **ROS Jazzy** 打包相关的文档，包括但不限于：

- **ROS 官方工具链调研**  
- **在 eulermaker 上打包的完整流程**  
- **衍生工具原理及说明**  
- ……

这些文档将详细介绍 ROS 官方打包流程，解析本次“小乌龟”环境的构建原理，并分享在实践中积累的经验与坑点。敬请关注后续更新！