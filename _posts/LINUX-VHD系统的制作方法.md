---
title: LINUX-VHD制作「转载」
date: 2017-03-14 11:59:09
category: [BLOG]
tags: [Linux,Core]
---

## LINUX-VHD系统的制作方法

1. 在windows 下，使用 `vbox` 制作 `linux vhd` 文件。

2. 安装一些软件：`kpartx`,`kpartx-boot`,`util-linux`,`dm-setup`

3. 需要修改两个文件：`local`,`mkinitramfs`

   备份之

   ```bash
   sudo cp /usr/share/initramfs-tools/scripts/local ./local.backup
   sudo cp /usr/sbin/mkinitramfs ./mkinitramfs.backup
   ```

4. 修改之

   - local

     `sudo gedit /usr/share/initramfs-tools/scripts/local`

     复制以下代码

     ```bash
     ##############################################################
     #                         TO BOOT FROM VHD                   #
     ##############################################################
     for x in $(cat /proc/cmdline); do
     case $x in
     vloop=*)
             VLOOP="${x#vloop=}"
             ;;
     vlooppart=*)
             VLOOPPART="${x#vlooppart=}"
             ;;
     vloopfstype=*)
             VLOOPFSTYPE="${x#vloopfstype=}"        
             ;;
     vloopcheck=*)
             VLOOPCHECK="${x#vloopcheck=}"        
             ;;
     esac
     done
     if [ "$VLOOP" ]; then
             if [ "$mountroot_status" != 0 ]; then
                     if [ ${FSTYPE} = ntfs ] || [ ${FSTYPE} = vfat ]; then
                             panic "
             Could not mount the partition ${ROOT}.
             This could also happen if the file system is not clean because of an operating
             system crash, an interrupted boot process, an improper shutdown, or unplugging
             of a removable device without first unmounting or ejecting it.  To fix this,
             simply reboot into Windows, let it fully start, log in, run 'chkdsk /r', then
             gracefully shut down and reboot back into Windows. After this you should be
             able to reboot again and resume the installation.
             (filesystem = ${FSTYPE}, error code = $mountroot_status)"
                     fi
             fi

            mkdir -p /host
            mount -o move ${rootmnt} /host
            str=${VLOOP}

            disk_files="/host${str}"                        
                     
            # FIXME This has no error checking
            modprobe loop
            kpartx -av "${disk_files}"
            sleep 3
                     
            # Get the vloop filesystem type if not set
            if [ -z "${VLOOPFSTYPE}" ]; then
                    FSTYPE="unknown"
            else
                    FSTYPE="${VLOOPFSTYPE}"
            fi
            if [ "$FSTYPE" = "unknown" ] && [ -x /sbin/blkid ]; then
                    FSTYPE=$(/sbin/blkid -s TYPE -o value "/dev/mapper/loop0${VLOOPPART}")
                    [ -z "$FSTYPE" ] && FSTYPE="ext4"
            fi
                     
            if [ ${readonly} = y ]; then
                    roflag=-r
            else
                    roflag=-w
            fi
                     
            [ -z "$VLOOPCHECK" ] && VLOOPCHECK="no"
            if [ "$VLOOPCHECK" = "yes" ] ; then
            echo "checking vloop / filesystem, please wait....."
            fsck.${FSTYPE} -a "/dev/mapper/loop0${VLOOPPART}"
            fi
                             
            mount -t ${FSTYPE} "/dev/mapper/loop0${VLOOPPART}" ${rootmnt}        

            if [ -d ${rootmnt}/host ]; then
                     mount -o move /host ${rootmnt}/host
            fi
     fi 
     ##############################################################
     #                     end,      TO BOOT FROM VHD             #
     ##############################################################
     ```

     在它们之后是

     ```bash
     [ "$quiet" != "y" ] && log_begin_msg "Running /scripts/local-bottom"
             run_scripts /scripts/local-bottom
             [ "$quiet" != "y" ] && log_end_msg
     }
     ```

   - 修改mkinitramfs

     `sudo gedit /usr/sbin/mkinitramfs`

     找到`# util-linux`,插入以下几行

     ```bash
     copy_exec /sbin/losetup /sbin
     copy_exec /sbin/kpartx /sbin
     copy_exec /sbin/shutdown /shutdown
     cp -a /sbin/fsck*  ${DESTDIR}/sbin/
     copy_exec /sbin/e2fsck /sbin
     touch ${DESTDIR}/etc/initrd-release
     touch ${DESTDIR}/version
     ```

5. 安装使用 `ntfs-3g`

   - 编辑文件 `sudo gedit /usr/share/initramfs-tools/scripts/local-bottom/ntfs_3g`

   覆盖原始内容

   ```bash
   #!/bin/sh
   ##set -e
   ##case "${1}" in
   ##        prereqs)
   ##                exit 0
   ##                ;;
   ##esac

   if [ "${ROOTFSTYPE}" = ntfs ] || [ "${ROOTFSTYPE}" = ntfs-3g ] || \
      [ "${LOOPFSTYPE}" = ntfs ] || [ "${LOOPFSTYPE}" = ntfs-3g ]
   then
           mkdir -p /run/sendsigs.omit.d
           pidof mount.ntfs >> /run/sendsigs.omit.d/ntfs-3g
           pidof mount.ntfs-3g >> /run/sendsigs.omit.d/ntfs-3g
   fi
   #####################################################################
   ##the following maybe help to resolve the buffer I/O error problem 
   ##when reboot or halt.
   #####################################################################

   if [ -d /run/initramfs -a -f /init ]
   then
           mkdir -p /run/initramfs/dev /run/initramfs/host /run/initramfs/proc /run/initramfs/root /run/initramfs/run /run/initramfs/sys /run/initramfs/tmp
           rm -rf   /lib/modules
           for xxx in /*
             do        
           if [ ${xxx} = "/dev" -o ${xxx} = "/host" -o ${xxx} = "/proc" -o ${xxx} = "/root" -o ${xxx} = "/run" -o ${xxx} = "/sys" -o ${xxx} = "/tmp" ];
           then
                   :
           else
                   cp -a ${xxx} /run/initramfs/  1>/dev/null 2>&1;
           fi
           done
           unset xxx
   fi
   ####################################################################
   exit 0
   ```

6. 引导设置

   - grub4dos —— for BIOS-MBR

     ```bash
     title VBUNTUFIX uuid-auto-probe
     find --set-root --ignore-floppies --ignore-cd /vbuntufix/vbuntufix.vhd
     uuid ()
     kernel /vbuntufix/vmlinuz  root=UUID=%?% vloop=/vbuntufix/vbuntufix.vhd vlooppart=p1 
     initrd /vbuntufix/initrd.img
     ```

   - grub2 —— for BIOS-MBR/UEFI

     ```bash
     menuentry " UBUNTU-1604.vhd " --class  ubuntu {
             insmod gzio
             insmod part_msdos
             insmod part_gpt
             insmod ext2
             insmod ntfs
             insmod probe
             set vhdfile="/ubt/UBUNTU-1604.vhd"
             set root=(hd0,1)
             search --no-floppy -f --set=aabbcc  $vhdfile
             set root=${aabbcc}
             probe -u --set=ddeeff ${aabbcc}
             loopback loop0 $vhdfile
             linux        (loop0,1)/vmlinuz root=/dev/sda5 rw  kloop=$vhdfile  kroot=/dev/mapper/loop0p1
             initrd        (loop0,1)/initrd.img
     ```

     ​
