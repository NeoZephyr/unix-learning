awk
```sh
awk '{print $1, $2, $3}' filename

# -F 选项改变字段分隔符
awk -F ',' '{print $1, $2, $3}' filename

# 正则表达式作为分隔符
awk -F "'" '/^menu/{print x++,$2}' filename
```


```sh
# FS 输入字段分隔符
awk 'BEGIN{FS=":"}{print $1,$2}' a.txt

# OFS 输出字段分隔符
awk 'BEGIN{FS=":";OFS="-"}{print $1,$2}' a.txt

# RS 输入分隔符，以 : 分隔作为一行
# ORS 输出分隔符
awk 'BEGIN{RS=":"}{print $1,$2}' a.txt

# NR 表示行号
awk '{print NR,$0}' a.txt b.txt

# FNR 表示行号
awk '{print FNR,$0}' a.txt b.txt
```

```sh
awk '{if($2>=80) {print $1; print $2}}' kpi.txt
```
```sh
awk '{sum=0; for(c=2;c<=NF;c++) sum+=$c; print sum/(NF-1)}' kpi.txt
```

awk 数组
```sh
awk '{sum=0; for(c=2;c<=NF;c++) sum+=$c; average[$1]=sum/(NF-1)}END{for(user in average) print user,average[user]}' kpi.txt
```

命令行数组
ARGC: 命令行参数个数
ARGV: 命令行参数
```sh
awk -f kpi.awk kpi.txt
```
```
BEGIN {
  for(x=0;x<ARGC;x++) {
    print ARGV[X]
  }
  print ARGC
}
```
```sh
awk -f arg.awk hello world
```

```sh
# NF: 每一行字段数
# NR: 文件行号
# FILENAME: 文件名
{
sum=0
for(c=0;c<=NF;c++)
  sum+=$c
average[$1]=sum/(NF-1)

if(average[$1]>=80)
  rank="S"
else if(average[$1]>=60)
  rank="A"
else
  rank="B"
print $1,average[$1],rank
}
END {
for(user in average)
  sum_all+=average[user]
avg_all=sum_all/NR
for(user in average)
  if(average[user] > avg_all)
    above++
  else
    below++
print "above", above
print "below", below
}
```

awk 函数
```sh
awk 'BEGIN{pi=3.14;print int(pi)}'
awk 'BEGIN{srand(); print rand()}'
```

awk 自定义函数
```sh
awk 'function a(){return 0} BEGIN{print a()}'
```