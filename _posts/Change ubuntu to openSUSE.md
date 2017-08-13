---
title: Change system of Windows subsystem
date: 2017-03-13 11:57:24
category: [BLOG]
tags: [Winodws,Linux,Subsystem]
---


# Change "Bash on Ubuntu on Windows" to "Bash on SUSE on Windows"

### First step

of couse you need to enable WSL. About how? Please read [this](https://msdn.microsoft.com/en-us/commandline/wsl/install_guide)

### Second ------ That's the main steps

- Run command given below, to download and open a docker user space of *openSUSE Leap 42.2*

  ```shell
  wget -O openSUSE-42.2.tar.xz https://github.com/openSUSE/docker-containers-build/blob/openSUSE-42.2/docker/openSUSE-42.2.tar.xz?raw=true
  ```

- Extra the *openSUSE* 

  ```shell
  sudo mkdir rootfs
  sudo tar -C rootfs -Jxf openSUSE-42.2.tar.xz
  exit
  ```

- Backup your *"Bash on Ubuntu on Windows"*

  ```shell
  cd %localappdata%\lxss\
  rename rootfs rootfs.ubuntu
  ```

- copy your new os ------ *"openSUSE"* to rootfs

  ```shell
  move .\home\<linux_user>\rootfs .\
  ```

- renew your root user

  ```shell
  lxrun /setdefaultuser root
  ```

### Third Step

Now is time to change it's appearance.

- Change it's logo.

  ```shell
  cd %localappdata%\lxss\
  rename bash.ico Ubuntu.ico
  rename Saki-NuoveXT-Apps-suse.ico bash.ico
  ```

- Change it's title.

  ```shell
  %AppData%\Microsoft\Windows\Start Menu\Programs
  ```

  ​
