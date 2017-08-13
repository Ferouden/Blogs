---
title: Use Turbo.net to Virtualization Microsoft Office 2013
date: 2017-03-20 7:38
update: 2017-03-20 7:40
tag: [Turbo.net,Virtualization App]
category: [Virtualization,Windows]
---

> 因为Office 在win8 win10 上使用了和win7 不同的 Licensing 服务，并且互不兼容，所以我们选择在win7 x86上部署Office 2013

> 理论上该方法适用于Office 2016，请自行尝试

> 环境说明：
>
> - OS: Win7 x86
> - Virtualization Tool: Turbo.net Studio
> - Target App: Office 2013

## Step 1

准备一个干净的windows系统。

## Step 2：安装

1. 安装Spoon Turbo.net Studio
2. 拍摄预快照
3. 保存
4. 开始安装Office 2013
5. 测试其中一个程序
6. 激活
7. 关闭程序
8. 停止 `Office Protection Platform Service`
9. 停止 `Office Source Engine Service`
10. 执行 `Capture and Diff`

## Step 3：编辑配置文件

1. 启用`Always launch child processes as current user`
2. 启用`Spawn COM Servers within the virtual environment`
3. 将虚拟服务`Office Protection Platform`设置为自动启动
4. 打开XAPPL添加内容:
   - 在`Virtualization Settings element`中加入`honorWow6464AccessFlag = "True"`
   - 在`Virtualization Settings element`中加入`nullifyLpcSID = "True"`
   - 作如图修改![img](https://support.turbo.net/customer/portal/attachments/259604)

## Step4：Build

