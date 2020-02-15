linux 启动过程
1. bios 引导，选择引导介质。选择第一个有引导程序的设备作为引导设备
2. mbr，硬盘主引导记录
```sh
dd if=/dev/sda of=mbr bs=446 count=1
hexdump -C mbr
```

3. grub 选择内核，引导内核启动
4. kernel
5. systemd 系统初始化
6. shell

```sh
# 查看运行级别
who -r
```

```sh
uname -r
yum install kernel-3.10.0
yum update
```
```sh
/boot/grub2/grub.cfg
```

grub 配置文件
```
/etc/default/grub
/etc/grub.d/
/boot/grub2/grub.cfg
grub2-mkconfig -o /boot/grub2/grub.cfg
```
```sh
# 当前引导的内核
grub2-editenv list
```
```sh
# 查找所有内核版本
grep ^menu /boot/grub2/grub.cfg
```
```sh
# 引导第一个内核
grub2-set-default 0
```
```sh
reboot
```