
### 1.在树莓派上安装openeuler:

跟随这罗老师的tutorial配置好ros安装所需要的一些环境,

参考链接:https://gitee.com/yunxiangluo/ros_openeuler?_from=gitee_search

这篇博客目的是在openeuler源码编译ros-comm,然后在将rpm包进行打包,并且验证打包可执行程序

因此着重记录一下跟罗老师tutorial当中需要注意或者说不同的地方

### 2.下载ros-common源码

在你的workspace当中执行:

```
rosinstall_generator ros_comm --rosdistro melodic --deps --tar > melodic-ros_comm.rosinstall
```

这条指令的目的是将ros-comm当中所有的52个pkg的url链接写入到melodic-ros_comm.rosinstall文件当中,里面会包含各个pkg的版本号,后续自己打包的时候,也是需要版本号的

```
wstool init -j8 src melodic-ros_comm.rosinstall
```

这条指令的目的是,根据melodic-ros_common.rosinstall文件当中的下载链接,将ros-comm当中的源码下载到src目录当中,

安装依赖库:

```
pip install empy
```

然后通过

```
./src/catkin/bin/catkin_make
```

进行catkin_make,编译工作区间,用来验证工作区间,来编译各个pkg

在执行catkin_make的时候,可能会出现python-empty这个库,可以通过pip install em来安装

### 3.将catkin源码打包rpm

构建源码打包的工程

![img](../pics/ros-install-5.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

soueces里面存放的是tar.gz结尾的压缩包,例如catkin-0.7.26.tar.gz

将文件夹进行压缩的命令:

```
tar zcvf catkin-0.7.26.tar.gz catkin-0.7.26/
```

将文件夹进行解压的命令

```
tar zxvf catkin-0.7.26.tar.gz catkin-0.7.26/
```

然后后面就是在SPEC文件夹下面创建catkin.sepc文件

```
Name:		catkin
Version:	0.7.26
Release:	1
Summary:	This is ROS melodic Catkin Package
License:	GPL
URL:		https://github.com/ros-gbp/catkin-release/archive/release/melodic/catkin
Source0:	https://github.com/ros-gbp/catkin-release/archive/release/melodic/catkin-0.7.26.tar.gz
BuildRequires:	gcc-c++
BuildRequires:	cmake
#BuildRequires:	python-empy
#BuildRequires:	libpng-devel
#BuildRequires:	libjpeg-turbo-devel
#BuildRequires:	gcc-gfortran
#BuildRequires:	openblas-devel
#BuildRequires:	sqlite-devel
#BuildRequires:	fftw-devel
#BuildRequires:	boost-devel
#BuildRequires:	python3-devel
#BuildRequires:	boost-python3-devel

%description
This is ROS melodic Catkin Package include catkin_make, catkin_init_workspace, and so on.

%prep
%setup -q

#%build
#cat > hello-world2.sh <<EOF
#!/usr/bin/bash
#echo Hello world
#EOF


%install
mkdir -p %{buildroot}/opt/ros/melodic/bin/
install -m 777 bin/catkin_make   %{buildroot}/opt/ros/melodic/bin/catkin_make
install -m 777 bin/catkin_find   %{buildroot}/opt/ros/melodic/bin/catkin_find
install -m 777 bin/catkin_make_isolated   %{buildroot}/opt/ros/melodic/bin/catkin_make_isolated
install -m 777 bin/catkin_init_workspace   %{buildroot}/opt/ros/melodic/bin/catkin_init_workspace
install -m 777 bin/catkin_topological_order   %{buildroot}/opt/ros/melodic/bin/catkin_topological_order
install -m 777 bin/catkin_test_results   %{buildroot}/opt/ros/melodic/bin/catkin_test_results
echo %{buildroot}

%files
#%defattr(-,root,root)
/opt/ros/melodic/bin/*

%changelog
* Thu May 28 2020 openEuler Buildteam <buildteam@openeuler.org> - 19.4-1
- Package init
```

打包成rpm 执行:

```
rpmbuild -ba catkin.spec
```

后续就要通过实际打包过程当中会出现那种报错,进行针对性的分析.

改变文件的所有权

```
chown -R davidhan:davidhan genmsg-0.5.16.tar.gz 
```

安装编译好的rpm包进行验证

```
rpm -ivh xxx.rpm
```

注意点: 在安装一个rpm包之前,一定要确定这个rpm包之前没有安装过,如果安装过的话,需要用卸载rpm包的命令,先把这个包卸载掉,然后再重新安装.

```
rpm -e xxx
```

总结一些心得:针对每个包首先你需要知道这个包当中有那些文件需要放到/opt/ros的目录下面,如何知道呢?就需要看懂cmakelist.txt到底写的是那些东西,可以参考自己的ros系统,看那些文件安装到/opt/ros下面,然后模仿,需要注意那些.in,后缀名的文件,因为这些文件还没有完全变成.py或者.sh文件,需要进行系统的一部分环境变量去转换,在打包的时候会报错.

－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－

2021年7月21日更新

本次更新主要是为了适配noetic版本的spec文件，模板如下：

```
Name:		ros_comm
Version:	1.15.11
Release:	2
Summary:	This is ROS noetic ros_comm Package
License:	Public Domain and Apache-2.0 and BSD and MIT and BSL-1.0 and LGPL-2.1-only
URL:		https://github.com/ros-gbp/ros_comm-release/archive/release/noetic/ros_comm
Source0:	https://github.com/ros-gbp/ros_comm-release/archive/release/noetic/ros_comm/1.15.11-1.tar.gz
BuildRequires:	gcc-c++
BuildRequires:	cmake
BuildRequires:	lz4-devel
BuildRequires:	bzip2-devel
BuildRequires:	python3-devel
BuildRequires:	python3-setuptools
BuildRequires:	openssl-devel
BuildRequires:	boost-devel
BuildRequires:	python3-gpgme
BuildRequires:	gpgme-devel
BuildRequires:	cpp-gpgme
BuildRequires:	python3-crypto

%description
This is ROS noetic ros_comm Package.

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

cd PyYAML-5.3.1/
python3 setup.py install --user
cd ..

cd distro-1.5.0/
python3 setup.py install --user
cd ..

cd rospkg-1.2.8/
python3 setup.py install --user
cd ..


cd console_bridge/
mkdir build/
cd build/
cmake ..
make -j8
make install
cd ..
cd ..

cd poco/
mkdir cmake-build/
mkdir cmake-release/
cd cmake-build/
cmake ..
make -j8
make install
cd ..
cd ..


cd tinyxml2/
mkdir build
cd build
cmake ..
make -j8
make install
cd ..
cd ..


cd ..


./src/catkin/bin/catkin_make_isolated --install 

####
# 对install_isoloate内部的变量名称进行替换
#
####
SRC_PATH=$PWD/install_isolated
DST_PATH=/opt/ros/noetic
sed -i "s:${SRC_PATH}:${DST_PATH}:g"  `grep -rIln "${SRC_PATH}" install_isolated/*`

####
# 添加.catkin和.rosinstall文件
#
####
mkdir -p %{buildroot}/opt/ros/noetic/
mkdir -p %{buildroot}/usr/local/
cp -r install_isolated/* %{buildroot}/opt/ros/noetic/
cp  install_isolated/.rosinstall %{buildroot}/opt/ros/noetic/
cp  install_isolated/.catkin %{buildroot}/opt/ros/noetic/
cp -r 3rdparty/install/* %{buildroot}/usr/local/
%files
%defattr(-,root,root)
/opt/ros/noetic/*
/opt/ros/noetic/.rosinstall
/opt/ros/noetic/.catkin
/usr/local/*
%changelog
* Thu 12-15-2020 openEuler Buildteam <hanhaomin008@126.com>
- Package init
```

