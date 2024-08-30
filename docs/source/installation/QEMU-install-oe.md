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

#### 编译安装 qemu-system-riscv64-8.2.6

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

TODO



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

镜像源的路径是`/openeuler/openEuler-24.03-LTS/virtual_machine_img/riscv64/`

```bash
wget https://repo.openeuler.org/openEuler-24.03-LTS/virtual_machine_img/riscv64/RISCV_VIRT_CODE.fd
wget https://repo.openeuler.org/openEuler-24.03-LTS/virtual_machine_img/riscv64/RISCV_VIRT_VARS.fd
wget https://repo.openeuler.org/openEuler-24.03-LTS/virtual_machine_img/riscv64/fw_dynamic_oe_2403_penglai.bin
wget https://repo.openeuler.org/openEuler-24.03-LTS/virtual_machine_img/riscv64/openEuler-24.03-LTS-riscv64.qcow2.xz
wget https://repo.openeuler.org/openEuler-24.03-LTS/virtual_machine_img/riscv64/start_vm.sh
wget https://repo.openeuler.org/openEuler-24.03-LTS/virtual_machine_img/riscv64/start_vm_penglai.sh
```

其中 `start_vm_penglai.sh` 是启用蓬莱 TEE 功能的脚本

此时，执行 `xz -d openEuler-24.03-LTS-riscv64.qcow2.xz` 命令对镜像解压，执行 `bash ./start_vm_penglai.sh` 就可以运行虚拟机了

默认的`root`用户密码是`openEuler12#$`

#### 启动 xfce 桌面

使用上面的镜像[无法启动xfce桌面](https://github.com/discodyer/shutsuryoku/issues/1)，请使用[这个镜像](https://mirror.iscas.ac.cn/openeuler-sig-riscv/openEuler-RISC-V/devel/20240829/v0.1/QEMU/)

和前面一样，只需要下载里面的镜像和固件，解压后再运行脚本就能启动xfce桌面环境了

## 3.安装openEuler桌面环境

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
