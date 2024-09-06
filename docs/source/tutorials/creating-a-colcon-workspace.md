# 创建 colcon 工作区

## 开始前的准备

 - [安装 ROS 2 Humble](../installation/install-ros-humble.md)
 - 安装 colcon

在 `openEuler 24.03` 上安装 `colcon` 可以使用 `pip3`

```bash
pip3 install -U pytest colcon-common-extensions
```

## 创建工作区

```bash
mkdir -p ~/ros2_ws/src/example
cd ~/ros2_ws/src/example
# 创建一个包，名称为test01，采用MIT协议开源
ros2 pkg create test01 --license MIT
```

然后会在 `src/example` 下创建一个名为 `test01` 的包

可以把 `example` 目录创建为git仓库，管理你的项目

首先，[在 GitHub 上创建一个存储库](https://docs.github.com/zh/repositories/creating-and-managing-repositories/creating-a-new-repository)

```bash
# cd ~/ros2_ws/src/example
echo "# test01" >> README.md
git init
# 创建并切换到一个名为test01的分支
git checkout -q -b test01
git add .
git commit -m "first commit"
# 修改为你的仓库地址
git remote add origin https://github.com/your_account/your_repo.git
git push --set-upstream origin test01
```

上面的命令会初始化Git仓库，创建一个`test01`分支并推送到远程仓库

可以先用`colcon build`初始化一下工作区

```bash
cd ~/ros2_ws
colcon build
```

然后会在顶层目录创建三个文件夹

```text
.
├── build
├── install
├── log
└── src
```

此时，执行 `source ~/ros2_ws/install/setup.bash` 设置环境，或者添加到`.bashrc`中

```bash
echo "source ~/ros2_ws/install/setup.bash" >> ~/.bashrc
```

## 相关链接

[Using colcon to build packages](https://docs.ros.org/en/humble/Tutorials/Beginner-Client-Libraries/Colcon-Tutorial.html)
