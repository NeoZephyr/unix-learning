service 脚本
```
/etc/init.d/
```
systemctl 脚本
```
/usr/lib/systemd/system/
```
```sh
# 随开机运行
systemctl enable <service>
```

```sh
# 服务运行级别
ls /lib/systemd/system
```
```sh
# 当前运行级别
systemctl get-default
```
```sh
# 设置运行级别
systemctl set-default multi-user.target
```

```sh
# 查看 selinux
getenforce
```
```sh
# 临时设置 selinux
setenforce 0
```