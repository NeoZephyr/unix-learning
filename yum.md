```sh
# 安装
rpm -ivh package

# 安装或升级
rpm -Uvh package

# 卸载
rpm -e

# 查询
rpm -q zsh
# 查询文件由哪个安装包生成
rpm -qf file
# 查看配置文件
rpm -qc zsh
```
```sh
rpm -q yum

# 仓库列表
yum repolist all
# 显示安装包
yum list
yum install package
yum update package
yum remove package
yum info package
yum search package

# 清理本地缓存
yum clean
# 生成缓存
yum makecache

# 查看安装包依赖关系
yum deplist package

yum history
```