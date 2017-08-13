---
title: Archlinux Install Log
date: 2017-04-09
category: [BLOG]
tags: [Linux,Archlinux,Install]
---

> 说明和介绍请前往[Archlinux Wiki](https://wiki.archlinux.org/index.php/Arch_Linux)
>
> 相关文档请查看[Archlinux Wiki](https://wiki.archlinux.org/)

## 硬件信息

- CPU
  - Intel i7-4710HQ
- GPU
  - NVIDIA GTX 970M
  - INTEL
- HardDisk
  - 500G HDD
  - 128G SSD
  - 256G SSD
- Other
  - Network
    - Intel wireless
    - Realtek
  - Audio
    - Realtek

## 预操作

### 下载ISO镜像

从[Archlinux](https://www.archlinux.org/download/)下载最新的镜像

### 写入U盘，制作启动盘 (或可刻录光驱)

- Windows

  使用UltraISO，RAW方式写入硬盘映像

- Linux

  使用dd 命令写入U盘设备

### 启动进bios，修改启动设备

## 安装预处理

### 连接到网络

1. DHCP

   使用`dhcpcd`命令自动启用设备并获取IP等信息

2. 无DHCP

   使用ipconfig设置IP

   这时网卡可能并没有启动，使用`ifconfig -a`查看网卡，例如我的网卡名为`enp5s0f2`，使用命令`ifconfig enp5s0f2 up` 启用网卡

   使用命令`ifconfig <Interface> <IP>`设置静态IP，例如`ifconfig enp5s0f2 192.168.1.100`

   使用命令`route add default gw <IP>`设置默认网关，例如`route add default gw 192.168.1.1`

### 设置软件源

`vim /etc/pacman.d/mirrorlist`选取适合的源

在国内建议使用

```bash
Server = http://mirrors.ustc.edu.cn/archlinux/$repo/os/$arch
Server = http://mirrors.tuna.tsinghua.edu.cn/archlinux/$repo/os/$arch
Server = http://mirrors.zju.edu.cn/archlinux/$repo/os/$arch
```

### 准备分区

使用命令`fdisk -l`查看当前连接的磁盘设备，确定目标硬盘，例如`/dev/sdb`

使用命令`cfdisk <device>` 进行分区，因为偏图形化所以比较推荐

> 建议使用UEFI启动方式

我分区为

```bash
/dev/sdb2	500M	ESP
/dev/sdb3	15G	Linux File System
/dev/sdb4	4G	swap
```

使用命令`mkfs.fat /dev/sdb2`将分区格式化为`Fat32`，`mkfs.ext4 /dev/sdb3`将分区格式化为Ext4

### 挂载分区

```bash
mount /dev/sdb3 /mnt
mkdir /mnt/boot
mount /dev/sdb4 /mnt/boot
```

## 安装系统

### 安装基本系统

如果需要编译程序等，请同时安装`base-devel`

```bash
pacstrap /mnt base
```

### 配置系统

#### Fstab

生成`fstab`文件

```bash
genfstab -U /mnt >> /mnt/etc/fstab
```

**正如WIKI建议的** 使用命令`cat /mnt/etc/fstab` 检查一下文件是否正确

例如

```bash
# 
# /etc/fstab: static file system information
#
# <file system>	<dir>	<type>	<options>	<dump>	<pass>
# /dev/sdb3
UUID=a9a331fa-cd96-4e13-84f2-efc22ce1b231	/         	ext4      	rw,relatime,data=ordered	0 1

# /dev/sdb2
UUID=A914-3F87      	/boot     	vfat      	rw,relatime,fmask=0022,dmask=0022,codepage=437,iocharset=iso8859-1,shortname=mixed,errors=remount-ro	0 2

# /dev/sdb4
UUID=8fb128dc-385d-4b74-87a0-ca3dfa60a2b0	none      	swap      	defaults  	0 0
# /usr squashfs
```

#### Chroot

**Change root** 到新系统

```bash
arch-chroot /mnt /bin/bash
```

#### 时区

```bash
ln -s /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
hwclock --systohc --utc
```

#### 本地化

`# vim /etc/locale.gen`

```bash
en_US.UTF-8 UTF-8
zh_CN.UTF-8 UTF-8
zh_TW.UTF-8 UTF-8
```

执行`# locale-gen`生成locale信息

`# echo LANG=en_US.UTF-8 > /etc/locale.conf`

#### 主机名

`# echo yourhostname > /etc/hostname`

同时添加hosts `# vim /etc/hosts`

```bash
127.0.0.1	localhost.localdomain	localhost
::1		localhost.localdomain	localhost
127.0.1.1	myhostname.localdomain	myhostname
```

#### 网络配置

安装 `networkmanager`,`iw`,`wpa_supplicant`,`dialog`，以自动配置网络和WIFI

```bash
pacman -S networkmanager iw wpa_supplicant dialog
systemctl enable networkmanager
```

#### 添加用户并修改Root密码

使用命令`passwd` 修改root密码

使用命令`useradd -m -g users -G wheel -s /bin/zsh username`添加名为username的用户

**注意** 一般的桌面程序使用UID在1000以上的用户，即非内置用户

使用命令`passwd username`修改username密码

在sudoers文件加入该用户使可以使用root权限 `vim /etc/sudoers`

```bash
username ALL=(ALL) ALL
```

#### 安装引导程序

建议安装**grub**，使用Intel CPU的设备安装`intel-ucode`，如果是多系统，需要安装`os-prober`

```bash
pacman -S grub efibootmgr intel-ucode os-prober
grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=grub --boot-directory=/boot
grub-mkconfig -o /boot/grub/grub.cfg
```

现在系统已经可以使用，执行`exit`,`reboot`退出`chroot`并重启系统，同时取下安装U盘(或光盘)

## 其他

### Nvidia Optimus 技术处理

该技术适用于双显卡(Intel + Nvidia)的笔记本设备

```bash
#添加multilib
vim /etc/pacman.conf
#去掉[multilib]的‘#’注释符
#安装驱动和管理程序
pacman -S bumblebee nvidia xf86-video-intel bbswitch mesa lib32-nvidia-utils lib32-mesa-libgl lib32-mesa
#添加用户到bumblebee组
gpasswd -a username bumblebee
gpasswd -a root bumblebee
#启动bumblebee服务
systemctl enable bumblebee
```

#### 配置bbswitch

在`/etc/module-load.d`下新建文件`bbswitch.conf`内容:

```bash
bbswitch
```

在`/etc/modprobe.d`下新建`blacklist.conf`,`bbswitch.conf`

blacklist.conf内容：

```bash
blacklist nouveau
```

bbswitch.conf内容：

```bash
options bbswitch load_state=0
```

在`/usr/lib/systemd/system-shutdown`下新建`nvidia_card_enable.sh`，内容：

```bash
#!/bin/bash  
	case "$1" in
		reboot)
			echo "Enabling NVIDIA GPU"
			echo ON > /proc/acpi/bbswitch  
     ;;  
  	*)
esac
```
