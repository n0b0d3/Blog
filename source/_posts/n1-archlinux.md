---
title: 斐讯 N1 安装 ArchLinux
date: 2020-01-11 01:15:12
tags: Linux
---

#### 分区和格式化以及挂载

```
fdisk /dev/sda
mkfs.vfat /dev/sda1 # dosfstools
mkfs.ext4 /dev/sda2
mount /dev/sda2 /mnt
mkdir -p /mnt/boot
mount /dev/sda1 /mnt/boot
```

#### 开始安装

```
pacstrap /mnt base
genfstab -U /mnt >> /mnt/etc/fstab
echo '104.27.184.124 archlinux.jerryxiao.cc' >> /etc/hosts
for item in $(curl -sL https://archlinux.jerryxiao.cc/any | grep keyring | grep -v sig | cut -d  \" -f2 |xargs); do curl -OL https://archlinux.jerryxiao.cc/any/$item ; done

pacman -U jerryxiao-keyring-*.pkg.tar.xz

echo '[jerryxiao]
Server = https://archlinux.jerryxiao.cc/$arch' >> /etc/pacman.conf

pacman -Sy linux-phicomm-n1 linux-phicomm-n1-headers
```

#### 设置 Boot

```
echo '/dev/mmcblk1 0x27400000 0x10000' > /etc/fw_env.config
```

#### 设置 uBoot 脚本

aml_autoscript.cmd 和 aml_autoscript.zip

```
cd /boot && curl -OL https://raw.githubusercontent.com/cattyhouse/N1-install/master/aml_autoscript.zip

cat <<'EOF' > /boot/aml_autoscript.cmd
defenv
setenv bootcmd 'run start_autoscript; run storeboot;'
setenv start_autoscript 'if mmcinfo; then run start_mmc_autoscript; fi; if usb start; then run start_usb_autoscript; fi; run start_emmc_autoscript'
setenv start_emmc_autoscript 'if fatload mmc 1 1020000 emmc_autoscript; then autoscr 1020000; fi;'
setenv start_mmc_autoscript 'if fatload mmc 0 1020000 s905_autoscript; then autoscr 1020000; fi;'
setenv start_usb_autoscript 'for usbdev in 0 1 2 3; do if fatload usb ${usbdev} 1020000 s905_autoscript; then autoscr 1020000; fi; done'
setenv upgrade_step 2
saveenv
sleep 1
reboot
EOF
```

s905_autoscript.cmd

```
cat <<'EOF' > /boot/s905_autoscript.cmd
if fatload mmc 0 0x11000000 boot_android; then if test ${ab} = 0; then setenv ab 1; saveenv; exit; else setenv ab 0; saveenv; fi; fi;
if fatload usb 0 0x11000000 boot_android; then if test ${ab} = 0; then setenv ab 1; saveenv; exit; else setenv ab 0; saveenv; fi; fi;
setenv env_addr 0x10400000
setenv kernel_addr 0x11000000
setenv initrd_addr 0x13000000
setenv boot_start booti ${kernel_addr} ${initrd_addr} ${dtb_mem_addr}
setenv addmac 'if printenv mac; then setenv bootargs ${bootargs} mac=${mac}; elif printenv eth_mac; then setenv bootargs ${bootargs} mac=${eth_mac}; fi'
setenv try_boot_start 'if fatload ${devtype} ${devnum} ${kernel_addr} zImage; then if fatload ${devtype} ${devnum} ${initrd_addr} uInitrd; then fatload ${devtype} ${devnum} ${env_addr} uEnv.ini && env import -t ${env_addr} ${filesize} && run addmac; fatload ${devtype} ${devnum} ${dtb_mem_addr} ${dtb_name} && run boot_start; fi; fi;'
setenv devtype mmc
setenv devnum 0
run try_boot_start
setenv devtype usb
for devnum in 0 1 2 3 ; do run try_boot_start ; done
EOF
```

emmc_autoscript.cmd

```
cat <<'EOF' > /boot/emmc_autoscript.cmd
setenv env_addr 0x10400000
setenv kernel_addr 0x11000000
setenv initrd_addr 0x13000000
setenv dtb_mem_addr 0x1000000
setenv boot_start booti ${kernel_addr} ${initrd_addr} ${dtb_mem_addr}
setenv addmac 'if printenv mac; then setenv bootargs ${bootargs} mac=${mac}; elif printenv eth_mac; then setenv bootargs ${bootargs} mac=${eth_mac}; fi'
if fatload mmc 1 ${kernel_addr} zImage; then if fatload mmc 1 ${initrd_addr} uInitrd; then if fatload mmc 1 ${env_addr} uEnv.ini; then env import -t ${env_addr} ${filesize}; run addmac; fi; if fatload mmc 1 ${dtb_mem_addr} ${dtb_name}; then run boot_start;fi;fi;fi;
EOF
```

生成二进制文件

```
cd /boot
mkimage -C none -A arm -T script -d aml_autoscript.cmd aml_autoscript
mkimage -C none -A arm -T script -d s905_autoscript.cmd s905_autoscript
mkimage -C none -A arm -T script -d emmc_autoscript.cmd emmc_autoscript
```

同步 aml_autoscript 的内容

```
fw_setenv bootcmd 'run start_autoscript; run storeboot;'
fw_setenv start_autoscript 'if mmcinfo; then run start_mmc_autoscript; fi; if usb start; then run start_usb_autoscript; fi; run start_emmc_autoscript'
fw_setenv start_emmc_autoscript 'if fatload mmc 1 1020000 emmc_autoscript; then autoscr 1020000; fi;'
fw_setenv start_mmc_autoscript 'if fatload mmc 0 1020000 s905_autoscript; then autoscr 1020000; fi;'
fw_setenv start_usb_autoscript 'for usbdev in 0 1 2 3; do if fatload usb ${usbdev} 1020000 s905_autoscript; then autoscr 1020000; fi; done'
```

#### 系统优化

安装 haveged

```
pacman -S haveged openssh
systemctl enable haveged
systemctl enable sshd
```

优化日本存储

```
cat <<'EOF' >> /etc/systemd/journald.conf
# 日志写入内存, 减少磁盘读写
Storage=volatile
# 日志最多占用100M空间
SystemMaxUse=100M
RuntimeMaxUse=100M
EOF
```

