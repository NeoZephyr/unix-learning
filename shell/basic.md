```sh
localectl set-locale LANG=zh_CN.utf8
```

```sh
whatis command
```

```sh
# 禁止覆盖输出重定向到已存在的文件
set -C

# 关闭上述特性
set +C
```
```sh
# 错误输出重定向
cat word.txt 2> word.out

# 合并正常与错误输出
cat word.txt &>> word.out
cat word.txt >> word.out 2>&1
```
```sh
cat > test.out << EOF
Hello World
Hello cat
EOF
```

```sh
cat word.out | tee word.txt
```

```sh
# 登录式切换，重新读取用户配置文件
su - pain

# 非登录式切换
su pain
```

本地变量：作用域为当前 shell 进程
环境变量：作用域为当前 shell 进程及其子进程

```sh
# 查看变量
set

# 查看环境变量
export

# 撤销变量
unset name

# 设置为只读变量
readonly name
```
```sh
# 环境变量赋值
export name=value
```

```sh
# -t 指定分隔符
# -k 指定排序字段
# -n 基于数值大小而非字符大小排序
# -r 逆序
# -u 去除重复
sort -t: -k3 -n /etc/passwd
```
```sh
# -c 显示每行重复次数
# -u 仅显示未曾重复过的行
# -d 仅显示重复过的行
uniq
```

```sh
# 算数运算
let sum=$num1+$num2
$[$num1+$num2]
```
```sh
# 数值
test 2 -eq 3
test 2 -lt 3

# 字符串
[[ "a" > "b" ]]
[[ "a" == "b" ]]
[[ "a" != "b" ]]

# 字符串是否为空
-z ""

# 字符串是否不空
-n ""


-a file
-e file

# 块设备文件
-b file

# 字符设备文件
-c file

# 目录文件
-d file

# 普通文件
-f file

# 符号链接文件
-h file

# 管道命名文件
-p file

# 套接字文件
-S file
```
```sh
# 是否存在，且对当前用户可读
-r file

# 是否存在，且对当前用户可写
-w file

# 是否存在，且对当前用户可执行
-x file

# 是否存在，且有 suid 权限
-u file

# 是否存在，且有 sgid 权限
[ -g file ]

# 是否存在，且有 sticky 权限
[ -k file ]
```
```sh
# 文件是否有内容
[ -s file ]

# 文件上一次读操作后是否修改过
-N file

# 当前用户是否为文件属主
-O file

# 当前用户是否为文件属组
-G file
```
```sh
# 是否指向同一个文件系统的相同 inode 的硬链接
file1 -ef file2

# file1 比 file2 新
file1 -nt file2

# file1 比 file2 旧
file1 -ot file2
```

```sh
find ./ -name "pass"
find /etc -iname "pass*"

find /etc -type d
```

文件具有 SUID 权限，则该文件运行为进程时，进程属主为文件的属主
```sh
chmod u+s /opt/cat
```
suid
执行命令时获取文件属主权限
sgid
用于目录，在该目录下创建新的文件和目录，权限自动更改为该目录的属组
sbit
用于目录，在该目录下创建新的文件和目录，仅 root 和自己可以删除

shell 脚本参数
```sh
echo $[$1+$2]
```
```sh
echo $1
shift
echo $1
```
```sh
# 运行文件名称
echo $0

# 参数个数
echo $#
```

```sh
if [ $1 -gt $2 ]; then
  echo "max: $1"
else
  echo "max: $2"
fi
```

重定向
```sh
# 输入重定向
wc -l < /etc/passwd
read a < a.txt
```
```sh
# 错误重定向
<cmd> 2> error.txt
# 全部重定向
<cmd> &> error.txt
```
```sh
cat > /etc/a.conf << EOF
echo "hello"
EOF
```


```sh
# 执行结果赋值
res=`ls /home`
res=$(ls /home)
```
```sh
a=10

# 导出，子 shell 可以获取父 shell 变量
export a

# 删除变量
unset a
```

环境变量
```sh
env
```

```sh
# pid
echo $$

# pname
echo $0
```
```sh
echo $1
echo $2
echo ${10}

# 如果位置 2 没有赋值，就取 _
echo ${2-_}
```

```sh
# login shell
su - <user>

# no login shell
su <user>
```

```sh
ipts=(10.0.0.1 10.0.1.2)
echo ${ipts[@]}
echo ${#ipts[@]}
echo ${ipts[0]}
```

转义
```sh
echo "\$a"
```
```sh
# 不解析变量
echo '$a'
# 解析变量
echo "$a"
```

```sh
num=`expr 4 + 5`
```
```sh
(( a=4+5 ))
```
```sh
[[ 5 > 4 ]]
[ 5 -gt 4 ]
echo {0..9}
```
```sh
(( 5 > 4 && 6 < 5 ))
```


```sh
# 读取键盘输入
read name age

echo -n "enter name: "; read name
echo -t 60 -p "enter name: "; read name
```


```sh
declare -i sum=0
for i in {1..100}; do
  sum=$[$sum+i]
done
echo $sum
```
```sh
for file in /var/log/*; do
  echo $file
done
```

```sh
declare -i sum=0
declare -i i=1

while [ $i -le 100]; do
  sum=$[$sum+$i]
  let i++
done

echo $sum
```

test 条件判断
```sh
[ -e /tmp/a.sh ]
[ 5 -gt 4 ]
[ "abc" > "bcd" ]
```
```sh
if [ $uid = 0 ]; then
  echo "user root"
else
  echo "other user"
fi
```
```sh
if [ $uid = 0 ]; then
  echo "user root"
  echo $uid
else
  echo "other user"
fi
```
```sh
if [ $uid = 0 ]; then
  echo "user root"
  if [ -x /tmp/a.sh ]; then
    /tmp/a.sh
  fi
elif [ $uid = 1 ]; then
  echo "user user1"
else
  echo "other user"
fi
```
```sh
case "$1" in
  "start"|"START")
    echo $0 start...
  ;;
  "stop")
    echo $0 stop...
  ;;
  *)
    echo Usage: $0 {start|stop}
  ;;
esac
```
```sh
for i in {1..9}
do
  echo $i
done
```
```sh
for filename in `ls *.mp3`
do
  mv $filename $(basename $filename .mp3).mp4
done
```
```sh
for filename in /etc/profile.d/*.sh
do
  if [ -x $filename ]; then
    . $filename
  fi
done
```
```sh
for i in {1..9}
do
  if [ $i -eq 5 ]; then
    continue
  fi
  echo $i
done
```

```sh
while [ $a -lt 10 ]
do
  echo $a
  ((a++))
done
```
```sh
while :
do
  echo "loop"
done
```

声明索引数组
```sh
declare -a arr
```
声明关联数组
```sh
declare -A arr
```
```sh
arr[0] = jack
arr[1] = page
echo ${arr[0]}
```
```sh
arr={"jack" "page"}
```
```sh
declare -A map
map[us]="usa"
map[cn]="china"
```

数组长度
```sh
echo ${#map[*]}
```
数组所有元素
```sh
echo ${map[*]}
```

处理参数
```sh
for pos in $*
do
  if [ "$pos" = "help" ]; then
    echo $pos $pos
  fi
done
```
```sh
while [ $# -ge 1 ]
do
  if [ "$1" = "help" ]; then
    echo $1 $1
  fi
  shift
done
```

函数
```sh
function test() {
  cd $1
  ls
}
test /tmp
```
```sh
checkpid() {
  local i
  for i in $*;
  do
    [ -d "/proc/$i" ] && return 0
  done

  return 1
}
```
```sh
source func.sh
checkpid 1
```

捕捉信号
```sh
trap "echo sig 15" 15
trap "echo sig 2" 2
echo $$
```