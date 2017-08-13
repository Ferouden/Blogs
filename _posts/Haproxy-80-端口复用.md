---
title: Haproxy 80 端口复用
date: 2017-5-10
update: 2017-5-10
tag: [haproxy,server]
category: [Linux]
---

## http/https ssh On Port 80

> 使用Haproxy，监听80/443端口，实现使用80/443端口访问ssh 和 http/https 服务

首先下载Haproxy源码：

`http://www.haproxy.org/download/1.7/src/haproxy-1.7.5.tar.gz`

查看内核版本：`uname -r`

进入目录编译安装：

```code
cd haproxy-1.7.5
make TARGET=linux2628 PREFIX=/usr/local/haproxy
make install PREFIX=/usr/local/haproxy
```

其中，`PREFIX`为安装位置，`TARGET`需要与`linux`版本一致

配置Haproxy

```conf
global
	maxconn 5120
	chroot /usr/local/haproxy
	daemon
	quiet
	nbproc 2
	pidfile /usr/local/haproxy/haproxypid
	
defaults
	timeout connect 5s
	timeout client 50s
	timeout server 20s
listen http
	bind :80
	timeout client 1h
	tcp-request inspect-delay 2s
	acl is_http req_proto_http
	tcp-request content accept if is_http
	server server-http :8080
	use_backend ssh if !is_http
backend ssh
	mode tcp
	timeout server 1h
	server server-ssh :22
```

把内容保存为`/usr/local/haproxy/etc/haproxy.conf`，执行命令：

`/usr/local/haproxy/sbin/haproxy -f/usr/local/haproxy/etc/haproxy.conf`