# VMWare安装openEuler24.03操作系统

## 1.安装VMWare虚拟机

[官网](https://www.vmware.com/)

1.从页面中点击＂Products＂，
![img_1.png](image/vm-install-oe/1.png)

2.下滑页面找到并点击DOWNLOAD TRIAL
![img_2.png](image/vm-install-oe/2.png)

3.下滑页面找到Windows版并点击 DOWNLOAD NOW 即可自动跳转下载
![img_3.png](image/vm-install-oe/3.png)

4.之后根据提示正常安装，在桌面上找到安装完成的VMware并双击打开，出现弹窗要求重启电脑，点击是
![img_4.png](image/vm-install-oe/4.png)

## 2.安装openEuler24.03系统

首先是下载[openEuler系统镜像](https://www.openeuler.org/zh/download/?version=openEuler%2024.03%20LTS)

选择"创建新的虚拟机",进入向导页面，选择"自定义"
![img_10.png](image/vm-install-oe/10.png)

进入选择兼容性页面，确定好后直接"下一步"
![img_11.png](image/vm-install-oe/11.png)

点击"稍后安装操作系统"
![img_12.png](image/vm-install-oe/12.png)

因为VMware没有openEuler系统选项，所以我们选择"Linux"->"其他Linux 6.x内核64位"
![img_13.png](image/vm-install-oe/13.png)

虚拟机命名，选择好安装位置，点击"下一步"
![img_14.png](image/vm-install-oe/14.png)

后面处理器和内存根据手中电脑配置而定，官方给的硬件要求是2个CPU，内存不低于4GB，硬盘不低于32GB
![img_15.png](image/vm-install-oe/15.png)
![img_16.png](image/vm-install-oe/16.png)

网络，IO和磁盘类型
![img_17.png](image/vm-install-oe/17.png)
![img_18.png](image/vm-install-oe/18.png)
![img_19.png](image/vm-install-oe/19.png)
![img_20.png](image/vm-install-oe/20.png)
![img_21.png](image/vm-install-oe/21.png)
![img_22.png](image/vm-install-oe/22.png)

最后在自定义硬件中找到新CD/DVD，使用ISO映像文件，把镜像文件导入即可
![img_23.png](image/vm-install-oe/23.png)


之后我们打开虚拟机,开启后会让我们选择语言，选择中文
![img_5.png](image/vm-install-oe/5.png)

这个时候会进入安装信息摘要
![img_6.png](image/vm-install-oe/6.png)

选择安装目的地,进去默认即可
![img_7.png](image/vm-install-oe/7.png)

选择软件选择处,点击最小安装即可
![img_8.png](image/vm-install-oe/8.png)

在用户设置处,进入Root账户,设置密码,之后重装系统
![img_9.png](image/vm-install-oe/9.png)

安装好之后,我们还需要解决VMWare复制粘贴问题

```
yum install open-vm-tools-desktop -y
reboot
```

## 3.安装桌面环境

桌面环境这里推荐UKUI,安装简便,与其他UI界面相比，UKUI更加注重易用性和敏捷度，各元件相依性小，可以不依赖其他套件而独自运行

1.更新源

```
sudo dnf update
```

2.安装UKUI

```
sudo dnf install ukui
```

3.在确认正常安装后，如果希望以图形界面的方式启动，请在命令行运行以下代码，并重启

```
systemctl set-default graphical.target
reboot
```



