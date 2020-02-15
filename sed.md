sed 替换
```sh
sed 's/old/new/' a.txt

# 多个替换指令
sed -e 's/old/new/' -e 's/old/new/' a.txt

# 替换后写入文件
sed -i 's/old/new/' -i 's/old/new/' a.txt

sed -r 's/扩展正则表达式/new/' a.txt
```
```sh
# 分组回调
sed -r 's/(a.*b)/\1:\1/' a.txt
```

全局替换
```sh
sed 's/old/new/g' a.txt
```
```sh
# 对第三次匹配的进行替换
sed 's/old/new/3' a.txt
```

```sh
# 打印模式空间的内容
sed 's/old/new/p' a.txt
```
```sh
# 只输出替换成功的行
sed -n 's/old/new/p' a.txt
```
```sh
# 替换成功的行写入文件
sed -n 's/old/new/w /tmp/a.txt' a.txt
```

```sh
# 只在第一行进行替换
sed '1s/old/new/' a.txt

# 1 - 3 行进行替换
sed '1,3s/old/new/' a.txt

# 1 到最后一行进行替换
sed '1,$s/old/new/' a.txt

# 对以 bin 开头的行进行替换
sed '/^bin/s/old/new/' a.txt

sed '/^bin/1,$s/old/new/' a.txt
```

```sh
sed -f sed_script filename
```

sed 删除
```sh
sed '/ab/d' a.txt
```

sed 插入
```sh
sed '/ab/i insert new line' a.txt
```
```sh
sed '/ab/a append new line' a.txt
```

sed 改写
```sh
sed '/ab/c replace new line' a.txt
```

sed 读取文件到匹配处
```sh
sed '/ab/r afile' a.txt
```

sed 多行处理
N 将下一行加入到模式空间
D 删除模式空间中的第一个字符到第一个换行符
P 打印模式空间中的第一个字符到第一个换行符
```sh
sed 'N;s/hel\nlo/HELLO/' a.txt
sed 'N;s/hel.lo/HELLO/' a.txt
```
```sh
# 123, 456
sed 'N;N;s/\n//;s/hello bash/hello world\n/;P;D'
```


```sh
# 不输出模式空间中的内容至屏幕
sed -n
# 多点编辑
-e
# 文件中，每行一个编辑命令
-f
# 支持扩展正则表达式
-r
# 直接编辑原文件
-i
```
```sh
# 删除第 1-5 行
sed '1,5d' /etc/fstab
# 删除第 3 行
sed '3d' /etc/fstab
# 删除 UUID 开头的行
sed '/^UUID/d' /etc/fstab

# 删除所有奇数行
sed '1~2d' /etc/fstab
# 删除所有偶数行
sed '2~2d' /etc/fstab
```
```sh
# 显示奇数行
sed -n '1~2p' /etc/fstab
```
```sh
# 在第三行前面插入 "hello world"
sed '3i \hello world' /etc/fstab
```
```sh
# 在第三行后面追加两行
sed '3a \hello world\nhello shell' /etc/fstab
```
```sh
sed '/^UUID/a \# comment' /etc/fstab
```
```sh
# 将 UUID 开头的行替换成 "# comment"
sed '/^UUID/c \# comment' /etc/fstab
```
```sh
# 将非 # 开头的行存储到 fstab.out 文件
sed '/^[^#]/w /tmp/fstab.out' /etc/fstab
```
```sh
# 在 /etc/fstab 文件的第三行后面，直接写入 /etc/issue 文件
sed '3r /etc/issue' /etc/fstab
```
```sh
# 匹配到的行打印行号
sed '/^UUID/=' /etc/fstab
```
```sh
# 删除非注释行
sed '/^#/!d' /etc/fstab
```
```sh
sed '' /etc/fstab
```