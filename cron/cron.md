```sh
systemctl status crond.service
```

接收邮件
```sh
# 运行正确不接收邮件
cmd > /dev/null

# 无论是否运行正确都不接收邮件
cmd &> /dev/null
```

一次性计划任务
```sh
at 18:31
```
```sh
atq
```

周期性计划任务
```sh
# 配置
crontab -e

# 查看现有计划任务
crontab -l
```

计划任务执行日志
```sh
tail -f /var/log/cron
```
计划任务保存文件
```sh
ls /var/spool/cron
```