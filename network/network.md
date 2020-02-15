```sh
# 路由表
route -n
```

```sh
ss -tnl

ip addr list

iptables -L -n

# 当前清除
iptables -F
```

ip
ip 掩码
网关
dns

ifconfig
配置 ip，掩码
route
路由
netstat
网络状态

ip addr
ip 地址和掩码
ip link
接口
ip route
路由
ss
网络状态

dns 服务器指定
/etc/resolv.conf
```
nameserver <ip>
```
主机名配置
hostname
/etc/sysconfig/network

/etc/sysconfig/network-scripts/ifcfg-{network card name}

网卡命名修改
编辑 /etc/default/grub 文件，增加 biosdevname=0 net.ifnames=0
更新 grub
```sh
grub2-mkconfig -o /boot/grub2/grub.cfg
reboot
```

内核参数
```sh
# 核心转发
net.ipv4.ip_forward
vm.drop_caches
# 主机名
kernel.hostname
# 忽略所有 ping 操作
net.ipv4.icmp_echo_ignore_all
```
```sh
# 查看
sysctl -a
sysctl <var>

# 修改
sysctl -w <var>=<value>
```

防火墙
规则表 filter nat
规则链 input output forward prerouting postrouting

iptable -t filter 命令 规则链 规则
```sh
# 查看 filter 规则表
iptables -t filter -L
iptables -t filter -nL
iptables -t filter -vnL

# 向后追加规则，允许源 ip。为 10.0.0.1 的主机进入
iptables -t filter -A INPUT -s 10.0.0.1 -j ACCEPT
iptables -t filter -A INPUT -s 10.0.0.1 -j DROP
# 向前插入规则
iptables -t filter -I INPUT -s 10.0.0.1 -j DROP

iptables -t filter -I INPUT -s 10.0.0.0/24 -j DROP
iptables -t filter -A INPUT -i eth0 -s 10.0.0.0/24 -p tcp --dport 80 -j DROP
```

```sh
# 修改默认规则
iptables -P INPUT DROP
iptables -P INPUT ACCEPT
```
```sh
# 清除规则
iptables -t filter -F
```

```sh
# 目的地址为 114.114.114.114 转发到 10.0.0.1
iptables -t nat -A PREROUTING -i eth0 -d 114.114.114.114 -p tcp --dport 80 -j DNAT --to-destination 10.0.0.1
```
```sh
# 源地址为 10.0.0.0/24 转发到 114.114.114.114
iptables -t nat -A POSTROUTING -o eth0 -s 10.0.0.0/24 -p tcp --dport 80 -j SNAT --to-source 114.114.114.114
```

```sh
yum install iptables-services

# 保存 iptables 设置
iptables-save
```

firewalld
```sh
systemctl status firewalld.service
systemctl start firewalld.service

firewall-cmd --state
firewall-cmd --list-all

firewall-cmd --zone=public --list-interfaces
firewall-cmd --zone=public --list-ports
firewall-cmd --zone=public --list-services

firewall-cmd --get-zones
firewall-cmd --get-default-zone
firewall-cmd --get-active-zone
```
```sh
# 添加临时
firewall-cmd --add-service=https
firewall-cmd --add-port=81/tcp

# 添加永久
firewall-cmd --add-port=82/tcp --permanent
firewall-cmd --reload
firewall-cmd --remove-source=10.0.0.1
```

```sh
# 抓取 ip 头部
tcpdump -i any port 23 -s 1500 -w /root/a.dump
```

ssh 服务
```sh
vi /etc/ssh/sshd_config

Port 22
PermitRootLogin yes
AuthorizedKeysFile .ssh/authorized_keys
```
密钥认证
```sh
ssh-keygen -t rsa
ssh-copy-id -i id_rsa_pub root@10.0.0.1
```

ftp 服务
```sh
yum install vsftpd ftp
systemctl start vsftpd.service
systemctl enable vsftpd.service
```
匿名用户
```sh
ftp localhost
ftp
ls
quit
```

samba
```sh
yum install samba

vi /etc/samba/
```
```sh
useradd user1
smbpasswd -a user1

# 查看用户
pdbedit -L

# 删除
smbpasswd -x user1
```
```sh
systemctl start smb.service

mount -t cifs -o username=user1 //server_ip/user1 /mnt
```

nfs
```sh
vi /etc/exports

/data/share 10.0.0.1(ro) 10.0.0.2(rw)
```
```sh
/data/share *(rw,sync,all_squash)
```
```sh
mkdir -p /data/share
```
```sh
systemctl start nfs.service
```
```sh
showmount -e server_ip
```
```sh
mount -t nfs localhost:/data/share /mnt
```

```sh
yum-config-manager --add-repo https://openresty.org/package/centos/openresty.repo
yum install openresty
service openresty start
```
```sh
cd /usr/local/openresty/nginx/conf
```
基于域名的虚拟主机
```sh
server {
  listen 80;
  server_name www.servera.com
  location / {
    root html/servera;
    index index.html index.htm
  }
}
```

dns
```sh
yum instal bind bind-utils
```
```sh
vi /etc/named.conf
```
```sh
named-checkconf
```


```sh
ifconfig
ip addr ls

# 修改 ip 地址
ifconfig eth0 10.0.0.1 netmask 255.255.255.0
ip addr add 10.0.0.1/24 dev eth0

ifup eth0
ip link set dev eth0 up

ifdown eth0
ip link set dev eth0 down
```

```sh
route -n
route add default gw <gw ip>
route add -host <ip> gw <gw ip>
route add -net <网段> netmask <netmask> gw <gw ip>

route add -net 10.0.0.0 netmask 255.255.255.0 gw 192.168.0.1
```
```sh
# 删除默认网关
route del default gw <gw ip>
```

```sh
ping www.baidu.com

# 目标主机超时，最多等待 1s
traceroute -w 1 www.baidu.com
mtr

# 域名解析
nslookup www.baidu.com

# 检查端口
telnet www.baidu.com 80

# 抓取所有网卡，80 端口
tcpdump -i any -n port 80
tcpdump -i any -n host 10.10.0.1 and port 80
tcpdump -i any -n host 10.10.0.1 and port 80 -w /tmp/dump.out
```
```sh
# -n 显示 ip 地址
# -t tcp
# -p 显示进程
# -l listen 状态
# -m 内存用量
netstat -ntpl

# 具体接口数据
netstat -i eth0
```
```sh
ss -ntpl
```

network VS NetworkManager
```sh
service network status
service network restart

systemctl list-unit-files NetworkManager.service

chkconfig --list network
chkconfig --level 2345 network off

systemctl disable NetworkManager
```

ip/netmask/gw/dns 配置
```sh
vi /etc/sysconfig/network-scripts/ifcfg-eth0
```
```sh
# 动态 ip
BOOTPROTO="dhcp"
NAME="eth0"
DEVICE="eth0"
# 开机启动网卡
ONBOOT="yes"
```
```sh
# 静态 ip
BOOTPROTO=none
IPADDR=10.10.10.1
NETMASK=255.255.255.0
GATEWAY=10.211.55.1
DNS1=114.114.114.114
```

network vs NetworkManager
```sh
chkconfig --list
```
```sh
service network restart
systemctl restart NetworkManager.service
```

路由配置
```sh
vi /etc/sysconfig/network-scripts/route-eth0
```
```
10.0.0.0/24 via 192.168.10.1
```

```sh
# 主机名
hostname
hostnamectl set-hostname hadoop01

vi /etc/hosts
vi /etc/sysconfig/network
```

```sh
dig -t A www.baidu.com

dig -x ip
```


DNS
```sh
yum info bind
```
```sh
# 测试 A 记录
dig -t A www.baidu.com

# 跟踪解析过程
dig +trace -t A www.baidu.com
```
```sh
# 反向解析
dig -x 119.75.222.17
```
```sh
host -t A www.baidu.com
host -t NS baidu.com
```
```sh
nslookup

# 指定域名服务器 ip
server <ip>

# 查询 A 记录
set q=A
www.baidu.com
```

nat
1. (cip, vip) 客户端发送到代理服务器，将目标地址和目标端口改为挑选出的后端服务器的地址和端口
2. (cip, rip) 代理服务器发送到后端服务器
3. (rip, cip) 后端服务器发送到代理服务器
4. (vip, cip) 代理服务器发送到客户端

rip 与 dip 必须在同一个 ip 网络，且使用私网地址，后端服务器的网关指向 dip
请求与响应都经过代理服务器转发，容易成为系统瓶颈

dr
1. (cip, vip)
2. 代理服务器为请求重新封装一个 mac 首部进行转发，源 mac 是 dip 所在的接口的 mac，目标 mac 是某挑选出的后端服务器所在接口的 mac 地址
2. (vip, cip)

确保前端路由器将目标 ip 为 vip 的请求报文发到代理服务器，这样需要后端服务器使用 arptables 或者修改内核参数限制 arp
后端服务器的网关不指向 dip，确保响应报文不经过代理服务器，后端服务器与代理服务器在同一个物理网络

代理服务器网卡两个 ip 地址，为 dip 与 vip
后端服务器网卡两个 ip 地址，为 rip 与 vip

tun
1. (cip, vip)
2. 代理服务器在原报文的基础上封装新的 ip 首部，(dip, rip)
3. (vip, cip)

dip, vip, rip 都应该是公网地址

fulltun
1. (cip, vip)
2. 代理服务器同时修改报文中的原 ip 与目标 ip，(dip, rip)
3. (rip, dip)
4. (vip, cip)

vip 是公网地址，rip 与 dip 是私网地址，且通常不在同一个网段

区别
proxy_pass http://192.168.1.2:80/ 访问的 uri 替换成后端路径
proxy_pass http://192.168.1.2:80 访问的 uri 直接补在后面


dhcp
