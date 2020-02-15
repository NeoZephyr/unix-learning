MBR 分为三个部分
1. 446 字节，bootloader 程序，引导启动操作系统的程序
2. 64 字节，分区表，每 16 字节标识一个分区
3. 2 字节，MBR 区域有效性标识

主分区和扩展分区标识：1 - 4
逻辑分区表示：5+

```sh
# 查看磁盘分区情况
fdisk -l
fdisk -l /dev/sda

fdisk /dev/sda
```

元数据 inode
存储文件大小、权限、属主属组、时间戳、数据块指针
inode 大小固定

inode index
block index
super block
block group

/var/log/messages
根 inode -> 根磁盘块 -> 磁盘块 -> 根目录下所有文件及其 inode 对应关系

硬链接文件：指向同一个 inode 的多个文件路径
符号链接文件：数据块指针的空间中存储的是真实文件的访问路径
设备文件：数据块指针的空间中存储的是设备号

```sh
# 给文件增加 inode
# 不能跨越文件系统
ln a.txt b.txt

ln -s a.txt b.txt
```

```sh
getfacl a.txt
setfacl -m u:pain:rw a.txt
setfacl -m g:pain:rw a.txt

# 收回权限
setfacl -s u:pain:rw a.txt
```

```sh
# 查看磁盘
fdisk -l

fdisk /dev/sdc
m
# 新建分区
n
# 创建主分区
p
# 分区号
1
# 分区起始
2048
# 分区结束
+50G
# 打印分区
p
# 删除分区
d
# 保存退出
w

# 使用 ext4 文件系统进行格式化
mkfs.ext4 /dev/sdc1 

# 挂载
mount /dev/sdc1 /mnt/sdc1

# 查看
mount
```
```sh
# 持久化分析挂载信息
vi /etc/fstab

/dev/sdc1 /mnt/sdc1 ext4 defaults 0 0
```

通过磁盘扩充交换分区
```sh
# 交换分区格式化
mkswap /dev/sdc2
swapon /dev/sdc2

# 关闭 swap
swapoff /dev/sdc2
```
通过文件扩充交换分区
```sh
dd if=/dev/zero bs=4M count=1024 of=/swapfile
chmod 600 /swapfile
mkswap /swapfile
swapon /swapfile
```
```sh
# 持久化 swap 信息
vi /etc/fstab

/dev/sdc2 swap swap defaults 0 0
```

逻辑卷
```sh
# 创建 3 个物理卷
pvcreate /dev/sd[b,c,d]

# 查看
pvs

# 将物理卷 /dev/sdb /dev/sdc 加入卷组
vgcreate pain /dev/sdb /dev/sdc

# 查看卷组
vgs

# 创建 100M 的逻辑卷
lvcreate -L 100M -n lv1 pain

# 查看逻辑卷
lvs

mkfs.xfs /dev/pain/lv1
mount  /dev/pain/lv1 /mnt/pain/lv1
```
```sh
# 扩展逻辑卷
vgextend centos /dev/sdd
lvextend -L +50G /dev/centos/root
xfs_growfs /dev/centos/root
```

```sh
lsb_release
uname -r
```

```sh
mount /dev/sda3 /mnt

# 只读挂载
mount -r /dev/sda3 /mnt

# 查看挂在目录被哪个进程使用
lsof /mnt

# 终止正在访问挂载点的进程
fuser -km /mnt

# 挂在光盘
mount -r /dev/cdrom /media

umount /mnt
```

```sh
# 只显示本地文件系统
df -l
df -h

# inode 数量
df -i
```
```sh
du /etc

# 查看目录下文件大小之和
du -s /etc
du -sh /etc
```

RAID 0 数据在从内存缓冲区写入磁盘时，根据磁盘数量将数据分成 N 份，这些数据同时并发写入 N 块磁盘，使得数据整体写入速度是一块磁盘的 N 倍；读取的时候也一样。RAID 0 不做数据备份

RAID 1 数据在写入磁盘时，将一份数据同时写入两块磁盘

RAID 10 将所有磁盘 N 平均分成两份，数据同时在两份磁盘写入，相当于 RAID 1；每一份磁盘平分成两份，也就是 N/2 块磁盘，利用 RAID 0 技术并发读写，这样既提高可靠性又改善性能

RAID 3 数据写入磁盘的时候，将数据分成 N-1 份，并发写入 N-1 块磁盘，并在第 N 块磁盘记录校验数据，这样任何一块磁盘损坏（包括校验数据磁盘），都可以利用其他 N-1 块磁盘的数据修复

RAID 5 和 RAID 3 很相似，但是校验数据不是写入第 N 块磁盘，而是螺旋式地写入所有磁盘中。。这样校验数据的修改也被平均到所有磁盘上，避免 RAID 3 频繁写坏一块磁盘的情况。

RAID 6 和 RAID 5 类似，但是数据只写入 N-2 块磁盘，并螺旋式地在两块磁盘中写入校验信息

```sh
# 复制 1k
dd if=src of=dest bs=1024 count=1024

# 40m 的空洞
dd if=/dev/zero of=dest bs=4M count=10 seek=10
```

```sh
# 生成光盘镜像
dd if=/dev/sr0 of=/tmp/test.iso
# 挂载光盘
mount /dev/sr0 /mnt
```