# Linux 常用命令

Linux 命令是开发者的必备技能，本文总结了常用的命令及其用法。

## 文件操作

### 基本命令

```bash
# 列出文件
ls -la              # 详细列表
ls -lh              # 人类可读的格式
ls -lt              # 按修改时间排序

# 改变目录
cd /path/to/dir     # 进入目录
cd ..               # 返回上级目录
cd ~                # 返回家目录
cd -                # 返回上一个目录

# 显示当前目录
pwd

# 创建目录
mkdir dir           # 创建单个目录
mkdir -p dir1/dir2 # 创建多级目录

# 删除文件或目录
rm file             # 删除文件
rm -r dir           # 删除目录
rm -rf dir          # 强制删除目录（谨慎使用）

# 复制文件
cp file1 file2      # 复制文件
cp -r dir1 dir2     # 复制目录

# 移动/重命名
mv oldname newname  # 重命名
mv file /path/to/   # 移动文件
```

### 文件查看

```bash
# 查看文件内容
cat file.txt        # 显示整个文件
more file.txt       # 分页查看
less file.txt       # 可回退的分页查看
head -n 10 file.txt # 查看前10行
tail -n 10 file.txt # 查看后10行
tail -f file.txt    # 实时跟踪文件

# 查看文件类型
file file.txt

# 查看文件大小
du -h file.txt
du -sh dir/         # 目录总大小
```

### 文件搜索

```bash
# 查找文件
find /path -name "filename"          # 按名称
find /path -type f -name "*.txt"     # 查找文本文件
find /path -size +100M               # 查找大于100MB的文件
find /path -mtime -7                 # 7天内修改的文件

# 搜索文件内容
grep "pattern" file.txt
grep -r "pattern" /path              # 递归搜索
grep -i "pattern" file.txt           # 忽略大小写
grep -n "pattern" file.txt           # 显示行号
grep -v "pattern" file.txt           # 反向匹配

# 组合使用
find . -name "*.java" -exec grep "TODO" {} \;
```

## 权限管理

### 权限查看与修改

```bash
# 查看权限
ls -l file.txt

# 修改权限
chmod 755 file.txt      # rwxr-xr-x
chmod +x script.sh      # 添加执行权限
chmod -R 755 dir/       # 递归修改

# 数字表示法
# 4 = 读, 2 = 写, 1 = 执行
# 7 = 4+2+1 = rwx
# 6 = 4+2 = rw-
# 5 = 4+1 = r-x
```

### 所有者修改

```bash
# 修改所有者
chown user file.txt
chown user:group file.txt

# 递归修改
chown -R user:group dir/
```

## 进程管理

### 进程查看

```bash
# 查看进程
ps aux                # 所有进程
ps -ef                # 完整格式
ps aux | grep java     # 过滤进程

# 实时监控
top
htop                  # 更友好的界面

# 查看进程树
pstree
```

### 进程控制

```bash
# 终止进程
kill PID              # 正常终止
kill -9 PID           # 强制终止
killall process_name  # 按名称终止

# 后台运行
nohup command &       # 退出后继续运行
command &             # 后台运行

# 查看后台任务
jobs
bg                    # 后台恢复
fg                    # 前台恢复
```

### 系统服务

```bash
# systemctl 服务管理
systemctl start service
systemctl stop service
systemctl restart service
systemctl status service
systemctl enable service      # 开机启动
systemctl disable service     # 关闭开机启动
```

## 网络操作

### 网络信息

```bash
# 查看网络接口
ifconfig              # 或 ip addr

# 查看端口占用
netstat -tulpn
ss -tulpn
lsof -i:port

# 测试网络连接
ping host
ping -c 4 host        # 发送4个包

# 测试端口连通性
telnet host port
nc -zv host port
```

### 防火墙

```bash
# firewalld
firewall-cmd --list-all
firewall-cmd --add-port=8080/tcp --permanent
firewall-cmd --reload

# iptables
iptables -L           # 列出规则
iptables -A INPUT -p tcp --dport 8080 -j ACCEPT
```

## 磁盘管理

### 磁盘信息

```bash
# 查看磁盘使用
df -h                 # 人类可读格式
df -i                 # 查看 inode

# 查看目录大小
du -h --max-depth=1 /path

# 磁盘分区
fdisk -l
lsblk

# 挂载
mount /dev/sdb1 /mnt
umount /mnt
```

## 系统监控

### 系统资源

```bash
# CPU 信息
cat /proc/cpuinfo
lscpu

# 内存信息
free -h

# 系统信息
uname -a
hostnamectl

# 运行时间
uptime
```

### 日志查看

```bash
# 系统日志
journalctl -u service    # 特定服务
journalctl -f            # 实时跟踪
journalctl --since "1 hour ago"

# 应用日志
tail -f /var/log/app.log
```

## 压缩与解压

```bash
# tar
tar -czf archive.tar.gz dir/     # 压缩
tar -xzf archive.tar.gz          # 解压
tar -xzf archive.tar.gz -C /path # 解压到指定目录

# zip
zip -r archive.zip dir/
unzip archive.zip

# gzip
gzip file.txt                   # 压缩（删除原文件）
gunzip file.txt.gz              # 解压
```

## 用户管理

```bash
# 用户操作
useradd username                # 添加用户
userdel username                # 删除用户
usermod -aG group username      # 添加到组

# 密码管理
passwd username                 # 修改密码

# 查看用户
whoami                         # 当前用户
id username                    # 用户信息
```

## 软件包管理

### apt (Debian/Ubuntu)

```bash
apt update                     # 更新软件包列表
apt upgrade                    # 升级软件包
apt install package            # 安装软件包
apt remove package             # 删除软件包
apt search keyword             # 搜索软件包
apt show package               # 显示软件包信息
```

### yum (CentOS/RHEL)

```bash
yum update                     # 更新
yum install package            # 安装
yum remove package             # 删除
yum search keyword             # 搜索
```

## 实用技巧

### 命令历史

```bash
history                       # 查看历史
!n                           # 执行第n条命令
!!                           # 执行上一条命令
!keyword                      # 执行包含keyword的最近命令
Ctrl + r                      # 搜索历史命令
```

### 管道和重定向

```bash
command > file                # 输出重定向
command >> file               # 追加重定向
command 2> error.log          # 错误输出重定向
command | grep pattern        # 管道过滤
command1 | command2 | command3 # 多个管道
```

### 别名

```bash
# 创建别名
alias ll='ls -la'
alias grep='grep --color=auto'

# 永久别名（添加到 ~/.bashrc）
echo "alias ll='ls -la'" >> ~/.bashrc
source ~/.bashrc
```

## Shell 脚本基础

```bash
#!/bin/bash

# 变量
name="pdkst"
echo "Hello, $name"

# 条件判断
if [ $name == "pdkst" ]; then
    echo "Yes"
else
    echo "No"
fi

# 循环
for i in {1..10}; do
    echo $i
done

while [ $i -le 10 ]; do
    echo $i
    i=$((i+1))
done

# 函数
say_hello() {
    echo "Hello, $1"
}

say_hello "World"
```

## 快捷键

| 快捷键 | 说明 |
|--------|------|
| `Ctrl + C` | 终止当前命令 |
| `Ctrl + Z` | 暂停当前命令 |
| `Ctrl + D` | 退出当前 shell |
| `Ctrl + L` | 清屏 |
| `Ctrl + A` | 光标移到行首 |
| `Ctrl + E` | 光标移到行尾 |
| `Ctrl + U` | 删除到行首 |
| `Ctrl + K` | 删除到行尾 |
| `Tab` | 自动补全 |
| `↑ / ↓` | 历史命令 |

## 参考资料

- [Linux 命令手册](https://linux.die.net/)
- [GNU Coreutils](https://www.gnu.org/software/coreutils/)

---

*最后更新时间: {docsify-updated}*