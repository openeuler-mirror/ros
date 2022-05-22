**前言**：做这件事的初衷是为了有一天能够在openeuler上通过yum install ros-xxx来安装ros，想想都觉得很炫酷。

 **step0: 环境准备**

**系统环境** OpenEuler21.03 

**ROS2的版本** ros2-foxy

目前是2021年12月1日，ros2已经到了galactic，关于galactic可以参考

[ROS 2 Documentation — ROS 2 Documentation: Galactic documentation](https://docs.ros.org/en/galactic/)

本章用于打包的是ros2的foxy版本，在ubuntu20.04上已经是比较成熟的版本，相关的文档参考：

[ROS 2 Documentation — ROS 2 Documentation: Foxy documentation](https://docs.ros.org/en/foxy/)

由于ros2目前还没有相对比较完善的wiki, 因此在这边进行一定的总结



**step1: 如何编译ros2的工程**

1.在ros1当中我们使用的catkin进行编译，在ros2当中我们使用colcon进行编译

关于colcon参考的

[Bootstrap from source — colcon documentation](https://colcon.readthedocs.io/en/released/developer/bootstrap.html)

```
$ mkdir colcon-from-source && cd colcon-from-source
$ curl --output colcon.repos https://raw.githubusercontent.com/colcon/colcon.readthedocs.org/main/colcon.repos
$ mkdir src
$ vcs import src < colcon.repos  # 这行要等很久
```

由于vcs感觉需要等很久很久，因此部分的包我已经上传到了gitee上面，大家可以去我的主页上查找

[davidhan008/colcon-pkg-config](https://gitee.com/davidhan008/colcon-pkg-config)

然后使用进行安装

```
./src/colcon-core/bin/colcon build --paths src/*
```

跟catkin一样我们需要source环境变量

```
souce install/local_setup.sh
```

就可以使用colcon来编译整个项目工程

```
colcon build 
```

相比于catkin，使用colcon的工程目录为

```
├── build
│   ├── ament_clang_format
│   ├── ament_clang_tidy
│   ├── ament_cmake
├── install
│   ├── bin
│   ├── COLCON_IGNORE
│   ├── lib
│   ├── local_setup.bash
│   ├── local_setup.ps1
│   ├── local_setup.sh
│   ├── _local_setup_util_ps1.py
│   ├── _local_setup_util_sh.py
│   ├── local_setup.zsh
│   ├── setup.bash
│   ├── setup.ps1
│   ├── setup.sh
│   ├── setup.zsh
│   ├── share
│   └── src
├── log
│   ├── build_2021-11-30_13-28-44
│   ├── build_2021-11-30_13-28-53
│   ├── COLCON_IGNORE
│   ├── latest -> latest_build
│   └── latest_build -> build_2021-11-30_13-28-53
└── src
    ├── ament
```

**step2: 如何编译ros2的工程源码在哪里？**

可以从官网下载，下载ros2.repos，里面有我们需要打包的ros2-foxy所需要的

https://github.com/ros2/ros2/releases

举个例子:



```
repositories:
  ament/ament_cmake:
    type: git
    url: https://github.com/ament/ament_cmake.git
    version: 0.9.9
  ament/ament_index:
    type: git
    url: https://github.com/ament/ament_index.git
    version: 1.1.0
  ament/ament_lint:
    type: git
    url: https://github.com/ament/ament_lint.git
    version: 0.9.6
  ament/ament_package:
    type: git
    url: https://github.com/ament/ament_package.git
    version: 0.9.5
  ament/google_benchmark_vendor:
```

我们可以克隆下来所需要编译的包，然后git checkout 到指定的项目分支上，以为ament_cmake为例子，

```
git clone  https://github.com/ament/ament_cmake.git
cd ament_cmake 
git checkout 0.9.9
```

**step3: 打包一个ament练练手**

**3.1 确定目录**

目录结构：其中3rdparty存放pip安装的第三方库，build_tools存放colcon编译工具，workspace/src 存放ros2的源码 workspace/install是我们在obs上编译生成的库和可执行文件



```
├── 3rdparty
│   ├── argcomplete-1.11.1
│   ├── attrs-21.2.0
│   ├── catkin_pkg-0.4.22
│   ├── coverage-5.4
│   ├── distlib-0.3.3
│   ├── docutils-0.16
│   ├── empy-3.3.4
│   ├── importlib_metadata-3.8.0
│   ├── importlib_metadata-4.8.2
│   ├── iniconfig-1.1.1
│   ├── more-itertools-5.0.0
│   ├── notify2-0.3.1
│   ├── packaging-21.3
│   ├── pluggy-1.0.0
│   ├── py-1.11.0
│   ├── pyparsing-2.4.7
│   ├── pyparsing-3.0.6
│   ├── pytest-6.2.5
│   ├── pytest-cov-3.0.0
│   ├── pytest-repeat-0.9.1
│   ├── pytest-rerunfailures-10.2
│   ├── pytest-runner-5.3.1
│   ├── python-dateutil-2.8.1
│   ├── PyYAML-6.0
│   ├── setuptools-59.3.0
│   ├── setuptools_scm-4.1.2
│   ├── six-1.15.0
│   ├── toml-0.10.2
│   ├── typing_extensions-3.7.4
│   ├── typing_extensions-4.0.0
│   ├── wheel-0.33.0
│   ├── zipp-1.0.0
│   ├── zipp-3.5.1
│   └── zipp-3.6.0
├── build_tools
│   └── colcon
└── workspace
    └── src
```



依赖库去哪里下载呢？

在这里[sip · PyPI](https://pypi.org/project/sip/)

切记要注意库的版本，有的版本比较新的库已经不支持python3 setup.py --user的安装方式了

```
Name:		ament
Version:	0.9.9
Release:	1
Summary:	This is ROS foxy ament Package
License:	Public Domain and Apache-2.0 and BSD and MIT and BSL-1.0
URL:		https://github.com/ros-gbp/ament-release/archive/release/foxy/ament
Source0:	https://github.com/ros-gbp/ament-release/archive/release/foxy/ament/0.9.9-1.tar.gz
BuildRequires:	gcc-c++
BuildRequires:	cmake
BuildRequires:	python3-devel
BuildRequires:	python3-setuptools

%description
This is ROS foxy ament Package.

%prep
%setup

%install
cd 3rdparty/ 

cd empy-3.3.4/
python3 setup.py install --user
cd ..

cd six-1.15.0/
python3 setup.py install --user
cd ..

cd setuptools_scm-4.1.2/
python3 setup.py install --user
cd ..

cd python-dateutil-2.8.1/
python3 setup.py install --user
cd ..

cd pyparsing-2.4.7/
python3 setup.py install --user
cd ..

cd docutils-0.16/
python3 setup.py install --user
cd ..

cd catkin_pkg-0.4.22/
python3 setup.py install --user
cd ..

#ros2

cd distlib-0.3.3/
python3 setup.py install --user
cd ..

cd attrs-21.2.0
python3 setup.py install --user
cd ..

cd more-itertools-5.0.0
python3 setup.py install --user
cd ..

cd zipp-1.0.0
python3 setup.py install --user
cd ..

cd wheel-0.33.0
python3 setup.py install --user
cd ..

cd toml-0.10.2
python3 setup.py install --user
cd ..

cd importlib_metadata-3.8.0
python3 setup.py install --user
cd ..

cd py-1.11.0
python3 setup.py install --user
cd ..

cd packaging-21.3
python3 setup.py install --user
cd ..

cd iniconfig-1.1.1
python3 setup.py install --user
cd ..

cd pluggy-1.0.0
python3 setup.py install --user
cd ..

cd typing_extensions-3.7.4
python3 setup.py install --user
cd ..

cd pyparsing-3.0.6
python3 setup.py install --user
cd ..

cd pytest-6.2.5
python3 setup.py install --user
cd ..

cd coverage-5.4
python3 setup.py install --user
cd ..

cd pytest-cov-3.0.0
python3 setup.py install --user
cd ..

cd pytest-repeat-0.9.1
python3 setup.py install --user
cd ..

cd pytest-rerunfailures-10.2
python3 setup.py install --user
cd ..

cd pytest-runner-5.3.1
python3 setup.py install --user
cd ..

cd PyYAML-6.0
python3 setup.py install --user
cd ..

cd setuptools-59.3.0
python3 setup.py install --user
cd ..

cd argcomplete-1.11.1
python3 setup.py install --user
cd ..

cd notify2-0.3.1
python3 setup.py install --user
cd ..

cd ..

cd build_tools
export BUILD_WORSPCE=$PWD
./colcon/colcon-core/bin/colcon build --paths colcon/* --merge-install
source install/local_setup.sh
cd ..

# for workspace
cd workspace
colcon build --merge-install

####
# 对install内部的变量名称进行替换
#
####
SRC_PATH=$PWD/install
DST_PATH=/opt/ros/foxy
sed -i "s:${SRC_PATH}:${DST_PATH}:g"  `grep -rIln "${SRC_PATH}" install/*`

SRC_PATH=$BUILD_WORSPCE/install
DST_PATH=/opt/ros/foxy
sed -i "s:${SRC_PATH}:${DST_PATH}:g"  `grep -rIln "${SRC_PATH}" install/*`
####
# install
#
####
mkdir -p %{buildroot}/opt/ros/foxy/
cp -r install/* %{buildroot}/opt/ros/foxy/

%files
%defattr(-,root,root)
/opt/ros/foxy/*

%changelog
* Thu 11-30-2021 openEuler Buildteam <hanhaomin008@126.com>
- Package init
```

colcon build --merge-install 可以达到 catkin_make_isolated --install 一样的效果

 **3.3 去obs上编译**

相关去osb编译的博客参考

[openeuler 下载已经打包好的rpm包,并验证功能_DavidHan008-CSDN博客](https://blog.csdn.net/David_Han008/article/details/107591536)

obs的官网

[Welcome - Open Build Service](https://117.78.1.88/)



**最后：**

相关的ament示例的源码文件和spec文件已经上传到gitee，欢迎大家clone

[davidhan008/ament_obs_test](https://gitee.com/davidhan008/ament_obs_test)



**参考博客**

 [ubuntu16.04 ros2 Crystal Colcon 源码安装教程_一个摩羯座的程序猿 的 知识笔记-CSDN博客](https://blog.csdn.net/shanpenghui/article/details/89210049)

[在Ubuntu 20.04中安装ROS2最新版本Foxy Fitzroy_大橙员-CSDN博客_ros2版本](https://blog.csdn.net/feimeng116/article/details/106602562)

