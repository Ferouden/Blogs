---
title: CentOS7 升级内核并启用`TCP-BBR`功能
date: 2017-04-03
update: 2017-04-03
category: [Linux]
tags: [Server,Net Speedup]
---


## CentOS7 升级内核并启用`TCP-BBR`功能

### 确认当前内核并升级

```bash
uname -r
rpm --import https://www.elrepo.org/RPM-GPG-KEY-elrepo.org
rpm -Uvh http://www.elrepo.org/elrepo-release-7.0-2.el7.elrepo.noarch.rpm
yum --enablerepo=elrepo-kernel install kernel-ml -y
```

### 查看内核列表，更改启动项

```bash
egrep ^menuentry /etc/grub2.cfg | cut -f 2 -d \'
```

你会看见类似返回

```bash
CentOS Linux (4.9.0-1.el7.elrepo.x86_64) 7 (Core)
CentOS Linux (3.10.0-327.13.1.el7.x86_64) 7 (Core)
CentOS Linux (3.10.0-327.10.1.el7.x86_64) 7 (Core)
CentOS Linux (3.10.0-229.20.1.el7.x86_64) 7 (Core)
CentOS Linux (0-rescue-fd8cf26e06e411e4a9d004010897bd01) 7 (Core)
```

设置处于编号 0 的`CentOS Linux (4.9.0-1.el7.elrepo.x86_64) 7 (Core)`默认运行

```bash
grub2-set-default 0
```

重启系统，并 `uname -r` 检查

### 写入参数到`sysctl.conf`

```bash
echo "net.core.default_qdisc=fq" >> /etc/sysctl.conf
echo "net.ipv4.tcp_congestion_control=bbr" >> /etc/sysctl.conf
sysctl -p
```

检查是否生效

`sysctl net.ipv4.tcp_available_congestion_control`

正常应返回

```bash
net.ipv4.tcp_available_congestion_control = bbr cubic reno
```

同时，执行`lsmod | grep bbr`，应返回

`tcp_bbr          ***** **`
