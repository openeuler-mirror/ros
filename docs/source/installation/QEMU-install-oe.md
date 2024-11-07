# QEMU安装openEuler24.03操作系统

## 1.安装QEMU

### 1.1Windows安装QEMU虚拟机

参考链接: [Windows11 上使用 QEMU 创建 aarch64（ARM64）虚拟机](https://blog.csdn.net/baidu_25117757/article/details/128302530)

#### 安装QEMU

[https://qemu.weilnetz.de/w64/](https://qemu.weilnetz.de/w64/)

#### 下载QEMU UEFI固件

[https://releases.linaro.org/components/kernel/uefi-linaro/latest/release/qemu64/QEMU_EFI.fd](https://releases.linaro.org/components/kernel/uefi-linaro/latest/release/qemu64/QEMU_EFI.fd)

#### 下载镜像文件

[https://www.openeuler.org/zh/download/](https://www.openeuler.org/zh/download/)

#### 安装tap-windows

[https://build.openvpn.net/downloads/releases/tap-windows-9.24.7-I601-Win10.exe](https://build.openvpn.net/downloads/releases/tap-windows-9.24.7-I601-Win10.exe)

### 1.2Ubuntu安装QEMU虚拟机

测试环境:
 - Ubuntu 22.04.4 LTS

#### 编译安装 qemu-system-riscv64

有两种方式安装qemu-system-riscv64，一种是包管理器安装，另一种是源码编译安装。注意运行24.03的openEuler for RISC-V要求qemu版本大于8.1，如果包管理器中的qemu版本太低需要使用源码编译安装。


##### 包管理器直接安装


1. x86架构
```bash
sudo apt install qemu-system-x86_64
```

2. arm架构
```bash
sudo apt install qemu-system-arm
```

3. RISC-V架构
```bash
sudo apt install qemu-system-riscv64
```

##### 源码编译安装

参考文章：

[https://docs.openeuler.org/en/docs/24.03_LTS/docs/Installation/RISC-V-QEMU.html](https://docs.openeuler.org/en/docs/24.03_LTS/docs/Installation/RISC-V-QEMU.html
)

[https://www.openeuler.org/en/blog/phoebe/2023-09-26-Run-openEuler-RISC-V-On-Qemu.html](https://www.openeuler.org/en/blog/phoebe/2023-09-26-Run-openEuler-RISC-V-On-Qemu.html)

由于发行版软件仓库里的qemu版本比较老(6.1)，所以需要自行编译安装新版本的qemu，运行24.03的openEuler for RISC-V要求qemu版本大于8.1，我们就安装 8.x 最新的 8.2.6 版。

首先安装需要的软件包

```bash
sudo apt install build-essential git libglib2.0-dev libfdt-dev libpixman-1-dev zlib1g-dev ninja-build libslirp-dev ninja-build wget tar xz-utils
```

确保你的`/etc/apt/sources.list`里开启了deb-src源，然后用下面的命令自动安装依赖

```bash
sudo apt-get build-dep qemu
```

在 `/usr/local` 下创建目标文件夹

```bash
sudo mkdir -p /usr/local/bin/qemu-riscv64
```

下载源码压缩包

```bash
wget https://download.qemu.org/qemu-8.2.6.tar.xz
```

下载完成后，解压并进入源码文件夹

```bash
tar xvJf qemu-8.2.6.tar.xz && cd qemu-8.2.6
```

配置编译选项

```bash
sudo ./configure --enable-slirp --target-list=riscv64-softmmu,riscv64-linux-user --prefix=/usr/local/bin/qemu-riscv64
```

`riscv64-softmmu`表示系统模式，`riscv64-linux-user`表示用户模式，为了测试方便，建议两个都编译，避免重复编译。

开始编译，(-j开启多线程编译)

```bash
sudo make -j$(nproc) && sudo make install
```

然后编辑`.bashrc`，在最后添加环境变量，然后source刷新一下用户配置
```bash
echo "export PATH=/usr/local/bin/qemu-riscv64/bin:$PATH" >> ~/.bashrc
source ~/.bashrc
```

现在可以使用 `qemu-system-riscv64 --version`检查是否能正常显示版本号，若显示版本信息，则表示QEMU安装成功，并且正常运行。

## 2.QEMU创建openEuler24.03虚拟机

### 2.1安装openEuler24.03操作系统（X86架构）

#### 设置img
```
qemu-img create -f qcow2 openEuler-24.03-LTS-SP3-x86.img 50G
```

#### 安装虚拟机

其中有部分需要修改
根据安装路径，**修改UEFI固件地址，修改网卡名，修改img路径，修改iso路径**

```
qemu-system-aarch64 -m 30000 -cpu cortex-a72 -smp 8,cores=2,threads=2,sockets=2 -M virt -bios D:\Programs\Qemu\QEMU_EFI.fd -net nic -net tap,ifname=tap1212 -device nec-usb-xhci -device usb-kbd -device usb-mouse -device VGA -drive if=none,file=E:\VirtuakMachine\mirror\openEuler-24.03-LTS-x86_64-dvd.iso,id=cdrom,media=cdrom -device virtio-scsi-device -device scsi-cd,drive=cdrom -drive if=none,file=E:\VirtuakMachine\openEuler-24.03-LTS-SP3-x86.img,id=hd0 -device virtio-blk-device,drive=hd0
```

-m 4000 表示分配给虚拟机的内存最大4000MB，可以直接使用 -m 4G
-cpu cortex-a72 指定CPU类型，还可以选择cortex-a53、cortex-a57等
-smp 4,cores=4,threads=1,sockets=1 指定虚拟机最大使用的CPU核心数等
-M virt 指定虚拟机类型为virt，具体支持的类型可以使用 qemu-system-aarch64 -M help 查看
-bios F:\QEMU\QEMU_EFI.fd 指定UEFI固件文件
-net tap,ifname=tap1212 启用网络功能（ifname=tap1212中的tap1212请修改为前面步骤中自己修改后的网卡名称）
-device nec-usb-xhci -device usb-kbd -device usb-mouse 启用USB鼠标等设备
-device VGA 启用VGA视图，对于图形化的Linux这条很重要！
-drive if=none,file=F:\QEMU\openEuler-20.03-LTS-SP3-aarch64-dvd.iso,id=cdrom,media=cdrom 指定光驱使用镜像文件
-device virtio-scsi-device -device scsi-cd,drive=cdrom 指定光驱硬件类型
-drive if=none,file=F:\QEMU\openEuler-20.03-LTS-SP3-aarch64.img 指定硬盘镜像文件

#### 启动虚拟机
```
qemu-system-aarch64 -m 16000 -cpu cortex-a72 -smp 8,cores=2,threads=2,sockets=2 -M virt -bios D:\Programs\Qemu\QEMU_EFI.fd -net nic -net tap,ifname=tap1212 -device nec-usb-hci -device usb-kbd -device usb-mouse -device VGA -drive if=none,file=D:\VirtuakMachine\qemu\openEuler-24.03-LTS-SP3-aarch64.img,id=hd0 -device virtio-blk-device,drive=hd0
```
#### 虚拟机配置

安装dde桌面
```
yum install dde
```

设置以图形化界面启动
```
systemctl set-default graphical.target
```

重启生效
```
reboot
```




### 2.2安装openEuler24.03操作系统（arm架构）

以win11下的QEMU为例

#### 设置img

```
qemu-img create -f qcow2 openEuler-24.03-LTS-SP3-aarch64.img 50G
```

#### 安装虚拟机

```
qemu-system-aarch64 -m 16000 -cpu cortex-a72 -smp 8,cores=2,threads=2,sockets=2 -M virt -bios D:\Programs\Qemu\QEMU_EFI.fd -net nic -net tap,ifname=tap1212 -device nec-usb-xhci -device usb-kbd -device usb-mouse -device VGA -drive if=none,file=D:\VirtuakMachine\openEuler-24.03-LTS-aarch64-dvd.iso,id=cdrom,media=cdrom -device virtio-scsi-device -device scsi-cd,drive=cdrom -drive if=none,file=D:\VirtuakMachine\qemu\openEuler-24.03-LTS-SP3-aarch64.img,id=hd0 -device virtio-blk-device,drive=hd0
```

其中有部分需要修改
根据安装路径，**修改UEFI固件地址，修改网卡名，修改img路径，修改iso路径**

>qemu-system-aarch64 -m 16000 -cpu cortex-a72 -smp 8,cores=2,threads=2,sockets=2 -M virt -bios 
>**D:\Programs\Qemu\QEMU_EFI.fd** -net nic -net tap,**ifname=tap1212** -device nec-usb-xhci -device usb-kbd 
>-device usb-mouse -device VGA -drive if=none,**file=D:\VirtuakMachine\openEuler-24.03-LTS-aarch64-dvd.iso**,
>id=cdrom,media=cdrom -device virtio-scsi-device -device scsi-cd,drive=cdrom -drive if=none,
>**file=D:\VirtuakMachine\qemu\openEuler-24.03-LTS-SP3-aarch64.img**,id=hd0 -device virtio-blk-device,drive=hd0

#### 启动虚拟机

修改成和上面一致的路径，注意两段指令并不相同，想启动时使用安装命令会导致重新安装

```
qemu-system-aarch64 -m 16000 -cpu cortex-a72 -smp 8,cores=2,threads=2,sockets=2 -M virt -bios D:\Programs\Qemu\QEMU_EFI.fd -net nic -net tap,ifname=tap1212 -device nec-usb-xhci -device usb-kbd -device usb-mouse -device VGA -drive if=none,file=D:\VirtuakMachine\qemu\openEuler-24.03-LTS-SP3-aarch64.img,id=hd0 -device virtio-blk-device,drive=hd0
```

>qemu-system-aarch64 -m 16000 -cpu cortex-a72 -smp 8,cores=2,threads=2,sockets=2 -M virt -bios 
>**D:\Programs\Qemu\QEMU_EFI.fd** -net nic -net tap,**ifname=tap1212** -device nec-usb-xhci -device usb-kbd 
>-device usb-mouse -device VGA -drive if=none,**file=D:\VirtuakMachine\qemu\openEuler-24.03-LTS-SP3-aarch64.img**,
>id=hd0 -device virtio-blk-device,drive=hd0

### 2.3 安装openEuler24.03操作系统（risc-v架构）

测试环境:
 - Ubuntu 22.04.4 LTS

#### 下载镜像文件和启动脚本

首先创建一个工作目录

```bash
mkdir oerv2403 && cd oerv2403
```

为了后续的调试和可视化呈现，我们需要安装带有桌面的镜像，镜像源的路径是 `/openeuler-sig-riscv/openEuler-RISC-V/devel/20240829/v0.1/QEMU/`

```bash
wget https://mirror.iscas.ac.cn/openeuler-sig-riscv/openEuler-RISC-V/devel/20240829/v0.1/QEMU/start_vm_xfce.sh
wget https://mirror.iscas.ac.cn/openeuler-sig-riscv/openEuler-RISC-V/devel/20240829/v0.1/QEMU/openEuler-24.03-V1-xfce-qemu-devel.qcow2.zst
wget https://mirror.iscas.ac.cn/openeuler-sig-riscv/openEuler-RISC-V/devel/20240829/v0.1/QEMU/fw_payload_oe_uboot_2304.bin
```

下载完成后，执行 `unzstd openEuler-24.03-V1-xfce-qemu-devel.qcow2.zst` 命令对镜像解压，执行 `bash ./start_vm_xfce.sh` 就可以运行虚拟机了

默认的`root`/`openeuler` 用户密码是`openEuler12#$`

## 3.安装openEuler桌面环境(RISC-V架构的openEuler已经安装了桌面环境，可跳过)

openEuler默认不带桌面，为了方便ROS开发测试，可选择安装UKUI等桌面：[官方教程](https://docs.openeuler.org/zh/docs/24.03_LTS/docs/desktop/%E5%AE%89%E8%A3%85UKUI.html#)

### 3.1安装DDE桌面

进入openEuler系统后，输入以下命令安装dde桌面环境：

```
yum install dde
```

设置以图形化界面启动

```
systemctl set-default graphical.target
```

重启生效

```
reboot
```

### 3.2安装UKUI桌面

安装UKUI

```
sudo dnf -y install ukui
```

设置图形启动

```
sudo systemctl set-default graphical.target
```

重启电脑后进入桌面系统

## 其他系统设置

TODO