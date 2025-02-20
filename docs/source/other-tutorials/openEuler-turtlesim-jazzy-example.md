# 在 openEuler 上移植并测试 ROS Jazzy：以“小乌龟 (turtlesim)” 为例

## 概述

伴随 ROS（Robot Operating System）在开源机器人领域的持续发展，越来越多的操作系统开始支持最新的 ROS 2 版本。此番在 **openEuler** 上成功运行 **ROS 2 Jazzy** 的“小乌龟（turtlesim）”示例，则标志着企业级操作系统与机器人开源生态之间的进一步融合。本文将介绍项目背景、意义与具体的移植方法，并简要解析 **ROS 2 Jazzy Jalisco** 这一长期支持（LTS）版本的核心价值及技术影响，帮助读者在 openEuler 环境中快速体验“小乌龟”。

---

## 背景与动机

**ROS** 是当前业界最广泛使用的开源机器人软件框架，拥有海量功能包和庞大的开发者社区。从学术研究到工业应用，ROS 依托灵活的通信机制和硬件抽象层，为机器人系统提供了高扩展性与活跃的生态支持。随着 **ROS 1**（Melodic、Noetic 等版本）进入维护收尾阶段，**ROS 2** 系列在实时性、跨平台兼容和工业安全性等方面都表现更优，正逐渐成为未来主流。

**ROS 2 Jazzy Jalisco**（以下简称 *Jazzy*）作为 ROS 2 的第十个正式版本，于 2024 年 5 月 23 日正式发布，并被定位为**长期支持（LTS）**版本，维护期直至 2029 年 5 月。这一决定展现了开源机器人基金会（OSRF）对工业应用稳定性的重视，也为学术界和产业界留下了五年的技术演进窗口。  
*Jazzy* 延续了 ROS 2 的分布式架构设计理念，并升级到 **RTI Connext 6.1.1** 以改善大规模节点通信时的延迟抖动。此外，首次引入的 **rmw_zenoh** 中间件预览版为云端—边缘端协同提供了新的广域网通信选择。

目前 openEuler 已支持 **ROS 2 Humble**，但从长期维护与新功能的角度出发，移植 *Jazzy* 能够为 openEuler 社区及机器人开发者带来更多可行性与拓展空间。本次移植项目完成了大量适配与测试工作，并以“小乌龟（turtlesim）”示例进行验证，满足了对多系统支持与企业级操作系统环境的双重需求。

---

## 项目说明

本项目旨在将 **Jazzy** 版本移植至 **openEuler**，在此过程中积累并整合了针对 ROS 包构建的诸多经验与知识。我们计划以系列文章的形式分享这些调研成果与实践细节，帮助更多开发者在不同操作系统环境中高效构建 ROS 软件包。

### 自研工具「ROT」：自动化移植的试验探索

在本次将 ROS 2 Jazzy 移植到 openEuler 的过程中，我们并未完全采用官方的构建方式，而是自行研发了一个名为 **ROT (ROS openEuler Tool)** 的 Python 工具来自动化处理移植流程。**ROT** 可以视作对 **bloom**、**rosdep** 等官方工具链的二次封装，专门针对 openEuler 做了特别的适配与整合，包含多段 Python 脚本，可通过命令行调用。其主要功能点如下：

1. **自动生成 spec 及源码包**  
   - 通过分析目标 ROS 包依赖，自动调用 *bloom*、*rosdep* 等官方工具链，生成符合 openEuler 打包规范的 spec 文件与源码包；  
   - 大幅减少编写、维护 spec 文件的人工成本。

2. **自动上传至 Gitee**  
   - 完成包生成后，ROT 支持将构建成果自动上传到内部或指定的 Gitee 仓库，实现版本管理与协作开发的一键化操作。

3. **构建错误分析**  
   - 当编译或打包过程中遇到缺失依赖、版本冲突等问题时，ROT 会收集日志并进行基础分析，帮助开发者快速定位、修复问题。

...

> **试验性质声明**  
> 目前，ROT 仍处于早期探索阶段，仅在内部使用，尚未公开发布。由于部分包的依赖关系尚未完善，在执行 `dnf install` 时依旧可能出现大量 `conflict` 提示。对于“小乌龟（turtlesim）”示例，ROT 已能完成编译与运行，但对更复杂的 ROS 包尚需进一步迭代与测试。  
> 我们计划在后续对 ROT 进行更全面的打包测试与错误处理优化，使其在更多 ROS 场景下为 openEuler 提供自动化支持。现阶段主要是一个技术预告，若社区对此感兴趣，欢迎持续关注后续进展。

尽管目前 ROT 还存在包冲突、依赖不完备等问题，我们相信在今后的不断完善与实践分享中，ROT 能为 ROS 与 openEuler 的深度融合提供一条更自动化、更便捷的路径，也期待更多开发者加入到移植与打包优化的工作中来。

---

## 启用软件源

1. **创建 .repo 文件**  
   在 `/etc/yum.repos.d` 目录下创建一个新的 `.repo` 文件，用于添加软件源。示例命令：
   ```bash
   cd /etc/yum.repos.d
   sudo vi openEuler-jazzy.repo
   ```
2. **编辑 repo 文件**  
   在 `openEuler-jazzy.repo` 中添加如下内容：
   ```ini
   [openEuler-Jazzy]
   name=openEuler-Jazzy
   baseurl=https://eulermaker.compass-ci.openeuler.openatom.cn/api/ems1/repositories/jazzy_ament_package/openEuler%3A24.03-LTS/x86_64/
   enabled=1
   gpgcheck=0
   ```
3. **刷新并更新系统仓库**  
   由于新增了 repo 文件，需要刷新并生成缓存：
   ```bash
   sudo dnf clean all
   sudo dnf makecache
   sudo dnf update --nobest
   ```

---

## 安装 ROS Jazzy

在成功添加仓库后，即可通过以下命令安装 ROS Jazzy：
```bash
sudo dnf install "ros-jazzy*" --skip-broken -y
```
由于仓库中尚存在部分冲突包，可能会出现大量 `conflict` 提示，这属于**预期行为**。`dnf` 会自动安装可行的包，并跳过暂无法安装的部分。对于想快速测试“小乌龟”的用户，可忽略该报错信息。

---

## 运行“小乌龟”示例

1. **激活 ROS 环境**  
   在安装完成后，需要激活 ROS 的环境脚本：
   ```bash
   source /opt/ros/jazzy/setup.zsh
   ```
   > 若使用 `bash`，则改为 `setup.bash`。

2. **启动 turtlesim 节点**  
   在第一个终端中执行：
   ```bash
   ros2 run turtlesim turtlesim_node
   ```

3. **开启键盘控制**  
   在另一个终端执行：
   ```bash
   ros2 run turtlesim turtle_teleop_key
   ```
   使用方向键或 `WASD` 控制屏幕上的小乌龟移动。若乌龟窗口能够正常弹出且顺利移动，即表示示例运行成功！

**示例界面**：  
![turtlesim demo](./image/openEuler-turtlesim-example/turtlesim.png)

---

## 其他说明

1. **dnf 更新时出现 tbb 冲突**  
   在执行 `dnf update` 时，若出现与 **tbb** 相关的冲突提示，可安全忽略；这是因为 openEuler 系统自带的 tbb 版本与 ROS Jazzy 需求不完全一致，但并不影响小乌龟示例的正常运行。

2. **安装包出现大量 conflict**  
   仓库目前主要满足“小乌龟”示例的正常执行，其他包仍在持续完善中。如果出现大量 conflict 提示，只要关键依赖能够正确安装，示例即可成功跑通。

---

## 其他相关文档

为深入介绍 **ROS Jazzy** 的打包与移植，我们后续还将陆续发布：

- **ROS 官方工具链调研**  
- **在 eulermaker 上的打包完整流程**  
- **衍生工具原理及说明**  
- ……  

这些文档将进一步解析官方打包流程、阐明小乌龟环境的构建原理，并分享更多实践经验与常见坑点。欢迎持续关注！

