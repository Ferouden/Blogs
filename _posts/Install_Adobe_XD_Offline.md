---
title: Install Adobe XD offline
date: 2017-02-23
category: [BLOG]
tags: [Windows,Adobe,Adobe XD]
---

## 获取软件包

1. 抓包获取下载地址

   我使用了Fiddler抓包

   ![fillder抓包](/img/adobe_xd_install-1.png)

2. 直接下载

   [下载链接 V: 0.6.16.7](https://mega.nz/#!LwYwmS6R!O-L3ccbQcCbaM8C8uPZiAH32SmKcNRFADJtHvQ6N1fw)

下载获得`UWPAssist.zip`，解压

## 安装

1. 使用Windows自带的UWP安装器直接安装`1\SparklerApp\SparklerApp.appxbundle`

2. 导入证书`1\SparklerApp\SparklerApp_0.6.16.7_x64.cer`，并记住对应版本号`0.6.16.7`

3. 进入`1\SupportAssets`文件夹，以管理员权限运行命令

   ```
   .\CSDKConfigurator.exe 0.6.16.7 Adobe.CC.XD adobexd.client.protocol

   ```

   将其中的`0.6.16.7`换成对应版本号

   查看生成的log文件

   ```text
   [Thu Feb 23 01:03:34 2017] Starting
   [Thu Feb 23 01:03:34 2017] Successful get SELECT * FROM Win32_ComputerSystem.
   [Thu Feb 23 01:03:34 2017] Successful get ComputerSystem row.
   [Thu Feb 23 01:03:34 2017] Successful get UserName field.
   [Thu Feb 23 01:03:34 2017] Current user is DESKTOP-UV9DLE9\jiang.
   [Thu Feb 23 01:03:34 2017] LookupAccountSid successful.
   [Thu Feb 23 01:03:34 2017] ConvertSidToStringSid succeeded returning: S-1-5-21-2342568884-3878327840-2893186036-1001
   [Thu Feb 23 01:03:34 2017] Current user SID is S-1-5-21-2342568884-3878327840-2893186036-1001
   [Thu Feb 23 01:03:34 2017] fullpath is C:\Program Files\WindowsApps\Adobe.CC.XD_0.6.16.7_x64__adky2gkssdxte\CreativeSDKAppServiceClient.exe
   [Thu Feb 23 01:03:35 2017] fullpath is C:\Program Files\WindowsApps\Adobe.CC.XD_0.6.16.7_x64__adky2gkssdxte\LogTransport2.exe
   [Thu Feb 23 01:03:36 2017] fullpath is C:\Program Files\WindowsApps\Adobe.CC.XD_0.6.16.7_x64__adky2gkssdxte\amtlib.dll
   [Thu Feb 23 01:03:37 2017] fullpath is C:\Program Files\WindowsApps\Adobe.CC.XD_0.6.16.7_x64__adky2gkssdxte\LogSession.dll
   [Thu Feb 23 01:03:38 2017] fullpath is C:\Program Files\WindowsApps\Adobe.CC.XD_0.6.16.7_x64__adky2gkssdxte\VulcanMessage5.dll
   [Thu Feb 23 01:03:39 2017] fullpath is C:\Program Files\WindowsApps\Adobe.CC.XD_0.6.16.7_x64__adky2gkssdxte\AdobePIP.dll
   [Thu Feb 23 01:03:40 2017] fullpath is C:\Program Files\WindowsApps\Adobe.CC.XD_0.6.16.7_x64__adky2gkssdxte\updaternotifications.dll
   [Thu Feb 23 01:03:41 2017] fullpath is C:\Program Files\WindowsApps\Adobe.CC.XD_0.6.16.7_x64__adky2gkssdxte\VulcanControl.dll
   [Thu Feb 23 01:03:42 2017] Permission of files changed successfully
   [Thu Feb 23 01:03:42 2017] Executing protocol handler registration with <& 'C:\Program Files\WindowsApps\Adobe.CC.XD_0.6.16.7_x64__adky2gkssdxte\CreativeSDKAppServiceClient.exe' --register adobexd.client.protocol --usersid S-1-5-21-2342568884-3878327840-2893186036-1001>
   [Thu Feb 23 01:03:42 2017] Protocol handler registration is successful
   [Thu Feb 23 01:03:42 2017] Closing
   ```

安装完成

> 说真的。。。Adobe XD 真是UI工作者的神器。。。
