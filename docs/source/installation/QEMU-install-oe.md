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

TODO



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

TODO



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
