---
title: Gentoo 的安装
date: 2018-06-02 19:58:44
tags: 
- Linux
- Gentoo
categories:
- Liunx运维
- Gentoo
---



<font  size=4 face="黑体">更新日志</font> 

> 2018-06-02: 创建

## 准备工作环境

### Linux系统
> 可以使用自己原有的Linux工作环境
> 推荐使用Gentoo官方[Livecd](https://www.gentoo.org/downloads/)
  使用方法如下列图示：
![](http://p7b7this6.bkt.clouddn.com/18-6-2/82978522.jpg)
![](http://p7b7this6.bkt.clouddn.com/18-6-2/518881.jpg)
![](http://p7b7this6.bkt.clouddn.com/18-6-2/96020345.jpg)

### 开通网络服务
> 虚拟机网络配置较为简单，LiveCD启动后即有网络，详细参考[官方文档](https://wiki.gentoo.org/wiki/Handbook:AMD64/Installation/Networking/zh-cn)
![](http://p7b7this6.bkt.clouddn.com/18-6-2/83910657.jpg)
[](https://wiki.gentoo.org/wiki/Handbook:AMD64/Installation/Networking/zh-cn)

### sshd服务
> 启动sshd服务`/etc/init.d/sshd start`
> 配置sshd服务允许密码登陆`vi /etc/ssh/sshd_config`，修改以下内容：
![](http://p7b7this6.bkt.clouddn.com/18-6-2/73642801.jpg)

## 对安装Gentoo的磁盘进行处理

### 默认的分区方案
![](http://p7b7this6.bkt.clouddn.com/18-6-2/41447592.jpg)

### 创建分区表
> 以`fdisk`创建Linux分区表，最终结果如下图示
![](http://p7b7this6.bkt.clouddn.com/18-6-2/40478433.jpg)

### 创建文件系统
```bash
mkfs.ext2 -T small /dev/sda2

mkfs.ext4 -T small /dev/sda5
mkfs.ext4 -T small /dev/sda6
```

### 激活swap分区
```bash
mkswap  /dev/sda3
swapon  /dev/sda5
```

### 挂载分区
![](http://p7b7this6.bkt.clouddn.com/18-6-2/97976949.jpg)
![](http://p7b7this6.bkt.clouddn.com/18-6-2/23441804.jpg)






## 安装Gentoo安装文件（stage3）

### 下载stage3
> 官方下载[stage3](https://www.gentoo.org/downloads/)
> 将其上传至LiveCD中的`/mnt/gentoo`

### 校验其安全性（可省略），解压stage3
> 参考官方[文档](https://wiki.gentoo.org/wiki/Handbook:AMD64/Installation/Stage/zh-cn)
```bash
cd /mnt/gentoo

xz -d stage3-*
tar xvf stage3*

# tar xvjpf stage3-*.tar.bz2 --xattrs --numeric-owner
```

### 编译配置选项
> 为了保留设置，Portage读入/etc/portage/make.conf文件 ，一个用于Portage的配置文件。`vi /mnt/gentoo/etc/portage/make.conf`
![](http://p7b7this6.bkt.clouddn.com/18-6-12/29231760.jpg)

## 安装Gentoo基本系统
### 配置Chroot
```bash
# 选择一个Gentoo镜像站点，推荐163
mirrorselect -i -o >> /mnt/gentoo/etc/portage/make.conf
# 构建ebuild配置文件
mkdir /mnt/gentoo/etc/portage/repos.conf
cp  /mnt/gentoo/usr/share/portage/config/repos.conf \
    /mnt/gentoo/etc/portage/repos.conf/gentoo.conf

# 复制DNS信息（保证chroot后网络畅通）
cp -L /etc/resolv.conf /mnt/gentoo/etc/

# 挂载必要文件系统（ /proc  /sys  /dev ）
# --make-rslave 操作是稍后安装systemd支持时所需要的。
mount -t proc /proc /mnt/gentoo/proc
mount --rbind /sys /mnt/gentoo/sys
mount --make-rslave /mnt/gentoo/sys
mount --rbind /dev /mnt/gentoo/dev
mount --make-rslave /mnt/gentoo/dev


# Chroot（进入新环境，所有操作均将立即生效）
chroot /mnt/gentoo /bin/bash    
source /etc/profile             # 将配置变量重新载入内存
export PS1="(chroot) $PS1"      # 更改主提示符


# 挂载 /boot 分区
mkdir /boot
mount /dev/sda2 /boot
```
### 配置Poetage
```bash
# 从网站安装ebuild 数据库快照
emerge-webrsync

# 更新Portage ebuild 数据库
emerge --sync

# 选择正确的配置文件
eselect profile list
eselect profile set NUMBER

# 更新@world集合
emerge --ask --update --deep --newuse @world
```
### 配置时区和地区
```bash
# 配置时区
# 查找可用的时区
ls /usr/share/zoneinfo
# 设置时区为上海
echo "Asia/Shanghai" > /etc/timezone
#更新/etc/localtime
emerge --config sys-libs/timezone-data

# 配置地区
# 启用US和CN地区及附加字符格式
echo 'en_US ISO-8859-1
en_US.UTF-8 UTF-8
zh_CN GBK 
zh_CN.UTF-8 UTF-8
' >> /etc/locale.gen
#
locale-gen
# 设定正确的地区
eselect locale list
eselect locale set NUMBER

# 重新加载环境
env-update && source /etc/profile && export PS1="(chroot) $PS1"
```


## 配置Linux Kernel
### 安装源码
```bash
emerge --ask sys-kernel/gentoo-sources

ls -l /usr/src/linux
```
### 手动配置内核
> 详见[Kernel/Gentoo 内核配置指南](https://wiki.gentoo.org/wiki/Kernel/Gentoo_Kernel_Configuration_Guide/zh-cn)
```bash
cd /usr/src/linux
# 配置Kernel
make menuconfig

# 编译Kernel
make && make modules_install
# 安装Kernel
make install
```

## 配置新系统
### 硬盘分区开机自动挂载
![](http://p7b7this6.bkt.clouddn.com/18-6-2/34674673.jpg)
### 配置root密码
`passwd root`

## 安装系统工具
### VIM
`emerge --ask vim`
### 无线网络驱动
`emerge sys-kernel/linux-firmware`
### 安装wpa_supplicant
`emerge wpa_supplicant`
### 安装DHCP客户端
`emerge dhcpcd`
### 安装PPPoE客户端
`emerge --ask net-dialup/ppp`
### 日志工具
```bash
emerge syslog-ng
rc-update add syslog-ng default
```
### Cron守护进程
```bash
emerge --ask sys-process/cronie
rc-update add cronie default
crontab /etc/crontab
```






## 配置BootLoader
```bash
echo 'GRUB_PLATFORMS="efi-64"' >> /etc/portage/make.conf
emerge --ask sys-boot/grub:2

grub-install --target=x86_64-efi --efi-directory=/boot

grub-mkconfig -o /boot/grub/grub.cfg

reboot
```





