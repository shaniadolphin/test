# RK3328 LINUX开发者指南
****
|Author|shaniadolphin|
|---|---|
|E-mail|349948204@qq.com|
****
## **目录**
* [编译固件](#编译LINUX固件)
    * 安装环境
    * 创建目录
    * 下载SDK
    * 编译UBOOT
    * 编译kernel
* [制作根文件系统](#制作根文件系统)
    * 安装依赖
    * 下载ubuntu core
    * 创建根文件映像
    * 复制qemu
    * 复制DNS
    * 增加源
    * chroot
* [打包固件](#打包固件)
    * 检查映像
    * 打包固件
* [烧录固件](#烧录固件)

### **编译LINUX固件**
#### **1. 在Ubuntu 16.04 amd64系统中安装以下包**
```Bash
sudo apt-get install bc bison build-essential curl \
     device-tree-compiler dosfstools flex gcc-aarch64-linux-gnu \
     gcc-arm-linux-gnueabihf gdisk git gnupg gperf libc6-dev \
     libncurses5-dev libpython-dev libssl-dev libssl1.0.0 \
     lzop mtools parted repo swig tar zip
```
#### **2. 创建工作目录**
```Bash
mkdir ~/proj/roc-rk3328-cc
cd ~/proj/roc-rk3328-cc
```
#### **3. 下载 Linux SDK**
```Bash
# U-Boot
git clone -b roc-rk3328-cc https://github.com/FireflyTeam/u-boot
# Kernel
git clone -b roc-rk3328-cc https://github.com/FireflyTeam/kernel --depth=1
# Build
git clone -b debian https://github.com/FireflyTeam/build
# Rkbin
git clone -b master https://github.com/FireflyTeam/rkbin
cd ~/proj/roc-rk3328-cc
```
#### **4. 编译UBOOT**
通过运行**build**目录下的**mk-uboot.sh**脚本，设定选项为**`roc-rk3328-cc`**
```Bash
./build/mk-uboot.sh roc-rk3328-cc
```
编译完后输出:
> out/u-boot/
>> idbloader.img
>> rk3328_loader_ddr786_v1.06.243.bin
>> trust.img
>> uboot.img

各个镜像文件的说明如下：

* idbloader.img: DDR 初始化与 miniloader 结合的文件。
* rk3328_loader_ddr786_v1.06.243.bin: DDR 初始化文件。
* trust.img: ARM trusted 固件。
* uboot.img: U-Boot映像文件。

也可以通过以下文件配置**UBOOT**:

* configs/roc-rk3328-cc_defconfig

#### **5. 编译编译kernel**
kernel中需要配置，并定义设备树，会涉及到以下文件:

* arch/arm64/configs/fireflyrk3328_linux_defconfig: 默认内核配置。
* arch/arm64/boot/dts/rockchip/rk3328-roc-cc.dts: 开发板设备树描述。
* arch/arm64/boot/dts/rockchip/rk3328.dtsi: CPU 设备树描述。

通过以下命令，完成内核配置，并更新默认配置：
```Bash
# 这非常重要!
export ARCH=arm64
cd kernel
# 首先使用默认配置
make fireflyrk3328_linux_defconfig
# 自定义你的 kernel 配置
make menuconfig
# 保存为默认配置
make savedefconfig
cp defconfig arch/arm64/configs/fireflyrk3328_linux_defconfig
```
需要注意，在**make menuconfig**时应进行如下配置：
```Bash
Device Drivers > Network device support > Wireless LAN > Rockchip Wireless LAN support 
> Rockchip Wireless LAN support 下的除“*”之外的"M"选项都去掉
Device Drivers > Network device support > Wireless LAN > Realtek rtlwifi family of devices 
> Realtek rtlwifi family of devices 下的选项都去掉
```
配置好后即可编译整个**kernel**(脚本文件内部设置了使用**-j4** 来编译):
```Bash
./build/mk-kernel.sh roc-rk3328-cc
```
编译完后输出:
>out/
>> boot.img
>> kernel
>> Image
>> rk3328-roc-cc.dtb

* boot.img: 包含 Image and rk3328-roc-cc.dtb 的映像文件, 为 fat32 文件系统格式。
* Image: 内核映像。
* rk3328-roc-cc.dtb: 设备树。

### **制作根文件系统**
#### **1. 安装依赖包**
```Bash
sudo apt-get install qemu qemu-user-static binfmt-support debootstrap
```
#### **2. 下载ubuntu core**
```Bash
wget -c http://cdimage.ubuntu.com/ubuntu-base/releases/16.04.1/release/ubuntu-base-16.04.1-base-arm64.tar.gz
```
#### **3. 创建根文件映像**
创建一个大小为**20G**的根文件系统映像文件，将**ubuntu core** 解压到该映像中，如果不需要安装过多的软件，可以先设置成**2G**，后期再调整。
```Bash
cd /mnt/h/proj
dd if=/dev/zero of=rootfs.img bs=1M count=0 seek=20000
sudo mkfs.ext4 -F -L ROOTFS rootfs.img
sudo mkdir /mnt/temp
sudo mount rootfs.img /mnt/temp 
sudo tar -xzvf ubuntu-base-16.04.1-base-arm64.tar.gz -C mnt/
```
#### **4. 复制qemu**
将**qemu-aarch64-static** 放到挂载的**rootfs** 的**/usr/bin** 中，能在**x86_64** 主机系统下**chroot** 到该**arm64** 文件系统中运行
```Bash
sudo cp -a /usr/bin/qemu-aarch64-static mnt/usr/bin/
```
#### **5. 复制主机DNS**
可以直接拷贝本机DNS设置：
```Bash
sudo cp -b /etc/resolv.conf mnt/etc/resolv.conf
```
#### **5. 增加有效的更新源**
用**vim** 编辑器打开**sources.list**
```Bash
vim mnt/etc/apt/sources.list
```
将以下内容复制到**sources.list** 中：
```Bash
# See http://help.ubuntu.com/community/UpgradeNotes for how to upgrade to
# newer versions of the distribution.

deb http://ports.ubuntu.com/ubuntu-ports/ xenial main  restricted
deb-src http://ports.ubuntu.com/ubuntu-ports/ xenial main restricted

## Major bug fix updates produced after the final release of the
## distribution.
deb http://ports.ubuntu.com/ubuntu-ports/ xenial-updates main restricted
deb-src http://ports.ubuntu.com/ubuntu-ports/ xenial-updates main restricted

## Uncomment the following two lines to add software from the 'universe'
## repository.
## N.B. software from this repository is ENTIRELY UNSUPPORTED by the Ubuntu
## team. Also, please note that software in universe WILL NOT receive any
## review or updates from the Ubuntu security team.
deb http://ports.ubuntu.com/ubuntu-ports/ xenial universe
deb-src http://ports.ubuntu.com/ubuntu-ports/ xenial universe
deb http://ports.ubuntu.com/ubuntu-ports/ xenial-updates universe
deb-src http://ports.ubuntu.com/ubuntu-ports/ xenial-updates universe

## N.B. software from this repository may not have been tested as
## extensively as that contained in the main release, although it includes
## newer versions of some applications which may provide useful features.
## Also, please note that software in backports WILL NOT receive any review
## or updates from the Ubuntu security team.
deb http://ports.ubuntu.com/ubuntu-ports/ xenial-backports main restricted
deb-src http://ports.ubuntu.com/ubuntu-ports/ xenial-backports main restricted

deb http://ports.ubuntu.com/ubuntu-ports/ xenial-security main restricted
deb-src http://ports.ubuntu.com/ubuntu-ports/ xenial-security main restricted
#deb http://ports.ubuntu.com/ubuntu-ports/ xenial-security universe
#deb-src http://ports.ubuntu.com/ubuntu-ports/ xenial-security universe
#deb http://ports.ubuntu.com/ubuntu-ports/ xenial-security multiverse
#deb-src http://ports.ubuntu.com/ubuntu-ports/ xenial-security multiverse

deb http://ports.ubuntu.com/ubuntu-ports/ xenial-proposed main restricted
deb-src http://ports.ubuntu.com/ubuntu-ports/ xenial-proposed main restricted
```
#### **7. chroot 到新的文件系统中**
**chroot**命令用来在指定的根目录下运行指令，在使用**chroot** 之后，系统的目录结构将以指定的位置作为**/** 位置。
```Bash
sudo chroot mnt/
```
在**rootfs** 的**root** 用户下设置：
```Bash
# 这里可以修改设置
USER=dolphin
HOST=dolphin
# 创建用户
useradd -G sudo -m -s /bin/bash $USER
passwd $USER
# 输入密码

# 设置主机名和以太网
# echo $HOST /etc/hostname
# echo "127.0.0.1 $HOST" >> /etc/hosts
# echo "127.0.0.1 localhost.localdomain localhost" > /etc/hosts
# echo "a.b.c.d  proxy">>  /etc/hosts
mkdir /etc/network
mkdir /etc/network/interfaces.d
echo "auto eth0" > /etc/network/interfaces.d/eth0
echo "iface eth0 inet dhcp" >> /etc/network/interfaces.d/eth0
# echo "nameserver 127.0.1.1" > /etc/resolv.conf
# echo "nameserver 8.8.8.8 " >> /etc/resolv.conf

# 使能串口
# ROC-RK3328-CC的UART默认使用1 500 000波特率和TTL电平。
ln -s /lib/systemd/system/serial-getty\@.service /etc/systemd/system/getty.target.wants/serial-getty@ttyS0.service

# 安装包
apt-get update
apt-get install lightdm vim git
apt-get install ifupdown net-tools network-manager
```
退出，并卸载文件系统
```Bash
exit
sudo sync
# <enter user password>
sudo umount rootfs/
```
如果想要在创建的根文件系统中安装软件，也可以继续**chroot** 到该文件系统中，安装一些常用的软件，比如**pip**， **numpy** 等，包括编译安装**opencv**等，避免复杂的交叉编译环境设置。
### **打包固件**
#### **1. 准备并检查映像**
把**Linux** 根文件系统映像文件 **rootfs** 放在 **out/** 下，这时**out** 目录应包含以下文件:
```Bash
$ tree out
```
> out
>> boot.img
>> kernel
>>> Image
>>> rk3328-roc-cc.dtb

>> rootfs.img
>> u-boot
>> idbloader.img
>> rk3328_loader_ddr786_v1.06.243.bin
>> trust.img
>> uboot.img
#### **2. 打包固件**
```Bash
./build/mk-image.sh -c rk3328 -t system -r out/rootfs.img
```
该脚本将根据《存储映射》所描述的布局，将分区映像文件写到指定位置，并最终打包成**out/system.img**。 
### **烧录固件**
插入**SD** 卡，如果**SD** 被自动挂载，则先将其卸载。
安装**pv**：
```Bash
sudo apt-get install pv
```
通过检查内核的日志查找**SD**卡的设备文件：
```Bash
dmesg | tail
```
如果设备文件为**/dev/sdb** ，使用**dd** 命令进行烧录：
```Bash
pv -tpreb /path/to/your/raw/firmware | sudo dd of=/dev/sdb conv=notrunc
```
如果需要将分区镜像写入到**SD** 卡，可以运行以下命令：
```Bash
sudo dd if=./out/u-boot/idbloader.img of=/dev/sdb seek=64 conv=sync,fsync 
sudo dd if=./out/u-boot/uboot.img of=/dev/sdb seek=16384 conv=sync,fsync 
sudo dd if=./out/u-boot/trust.img of=/dev/sdb seek=24576 conv=sync,fsync 
sudo dd if=./out/boot.img of=/dev/sdb seek=32768 conv=sync,fsync 
sudo dd if=./out/rootfs.img of=/dev/sdb seek=262144 conv=sync,fsync
```
也可以运行下面指令，将生成的统一固件**system.img** 写入到**SD** 卡中：
```Bash
build/flash_tool.sh -c rk3328 -d /dev/sdb -p system -i out/system.img
```
****

### **参考文档**

|#|链接地址|文档名称|
|---|---|---|
|1|`dev.t-firefly.com/forum.php?mod=viewthread&tid=52810&highlight=3288`|[构建ROC-RK3328-CC 固件](http://dev.t-firefly.com/forum.php?mod=viewthread&tid=52810&highlight=3288 "社区文档")|
|2|`wiki.t-firefly.com/zh_CN/ROC-RK3328-CC/linux_compile_firmware.html`|[ROC-RK3328-CC 开发者指南](http://wiki.t-firefly.com/zh_CN/ROC-RK3328-CC/linux_compile_firmware.html "官方文档")|
|3|`wiki.t-firefly.com/zh_CN/ROC-RK3328-CC/flash_sd.html`|[ROC-RK3328-CC烧写SD  卡](http://wiki.t-firefly.com/zh_CN/ROC-RK3328-CC/flash_sd.html "官方文档")|
|4|`opensource.rock-chips.com/wiki_Distribution`|[制作根文件系统](http://opensource.rock-chips.com/wiki_Distribution "官方文档")|
****
