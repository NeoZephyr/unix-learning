```sh
# 查看 cpu 信息，每隔 1s 采集一次，采集 10 次
sar -u 1 10

# 查看内存
sar -r 1 10

# 查看 io
sar -b 1 10

# 查看磁盘
sar -d 1 10

# 查看进程
sar -q 1 10
```
```sh
yum install epel-release
yum install iftop

# 网络查看
iftop -P
```


```sh
# 当前终端进程
ps

ps -ef

# 查看线程
ps -eLf

pstree
```
```sh
# p 以占据 cpu 百分比排序
# m 以占据内存百分比排序
# t 累积占用 cpu 时间排序
top
top -p <pid>
```
R: running
S: Interruptable sleeping
D: uninterruptable sleeping
T: stopped
Z: zombie

+: 前台进程
|: 多线程进程
N: 低优先级进程
<: 高优先级进程
s: session leader

vmstat
```sh
# 1s 显示一次，显示 10 次
vmstat 1 10
```

```sh
# 进程 pid
echo $$
```
```sh
kill -l
```
```sh
# 关掉终端依然可以运行
nohup <cmd> &
```


内存查看
```sh
free
free -h

top
```

磁盘
```sh
df -h
```