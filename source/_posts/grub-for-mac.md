---
title: 用 uuid 引导 macOS
date: 2017-08-14 09:41:55
tags: 系统
---

之前在 grub.cfg 写入引导 macOS 的配置文件为：

```buildoutcfg
menuentry 'Mac OS X (on /dev/sda3)' --class osx --class darwin --class os  $menuentry_id_option 'osporber-xnu-64-6834a4ed4dccef17' {
	insmod part_msdos
	insmod hfsplus
	set root='hd1,gpt3'
	chainloader /System/Library/CoreServices/boot.efi
}
```
设置 root 路径为 /dev/sda3 设备，由于电脑偶尔会插入优盘，导致设备的盘符发生变化 hd1 变成 hd0，macOS 无法正确引导。

想到 grub 可以通过设备的 uuid 进行引导，于是我在 Arch 查看 /dev/sda3 的 uuid。

```sh
ls -l /dev/disk/by-uuid

lrwxrwxrwx 1 root root 10 Aug 14 21:13 1c0e6a70-8a37-4f84-a21d-b36b14581251 -> ../../sda4
lrwxrwxrwx 1 root root 10 Aug 14 21:13 67E3-17ED -> ../../sda1
lrwxrwxrwx 1 root root 10 Aug 14 21:13 e58d4ce3-3eb7-35e1-a131-0eef1078a25f -> ../../sda3
lrwxrwxrwx 1 root root 10 Aug 14 21:13 f91a9568-c6d2-465b-bc29-0788c52210e2 -> ../../sda5
```
于是将 grub.cfg 改为：
```buildoutcfg
menuentry 'Mac OS X (on /dev/sda3)' --class osx --class darwin --class os  $menuentry_id_option 'osporber-xnu-64-6834a4ed4dccef17' {
	insmod part_msdos
	insmod hfsplus
	set root='hd1,gpt3'
	search --no-floppy --fs-uuid --set=root e58d4ce3-3eb7-35e1-a131-0eef1078a25f
	chainloader /System/Library/CoreServices/boot.efi
}
```
再次引导的时候还是会出现 unknown device，最后发现在 Arch 下直接查找的 uuid 并不对，所以 grub 找不到设备，macOS 无法启动。

正确的 uuid 应该使用 grub-prob 进行计算，生成方式为：
```buildoutcfg
sudo grub-prob -t fs_uuid -D /dev/sda3
```

再次写入 grub.cfg 引导就正常了，开机时插拔优盘进行测试也是正常的。

