---
title: centos安装vsftpd
date: 2017-11-19 16:11:06
tags: centos vsftpd
---
centos 安装 vftpd

## 安装vsftpd
``` bash
# 查看当前系统版本
# cat /etc/redhat-release 
CentOS release 6.5 (Final)

# 查看是否已经安装vsftpd
# rpm -qa | grep vsftpd

# 如果没有，就安装，并设置开机启动
# yum -y install vsftpd
# chkconfig vsftpd on
```

## 基于虚拟用户的配置
所谓虚拟用户就是没有使用真实的帐户，只是通过映射到真实帐户和设置权限的目的。虚拟用户不能登录CentOS系统。
### 修改配置文件
``` bash
# vim /etc/vsftpd/vsftpd.conf

#服务器独立运行
listen=YES
#设定不允许匿名访问
anonymous_enable=NO
#设定本地用户可以访问。注：如使用虚拟宿主用户，在该项目设定为NO的情况下所有虚拟用户将无法访问
local_enable=YES
#使用户不能离开主目录
chroot_list_enable=YES
#设定支持ASCII模式的上传和下载功能
ascii_upload_enable=YES
ascii_download_enable=YES
#PAM认证文件名。PAM将根据/etc/pam.d/vsftpd进行认证
pam_service_name=vsftpd
#设定启用虚拟用户功能
guest_enable=YES
#指定虚拟用户的宿主用户，CentOS中已经有内置的ftp用户了
guest_username=ftp
#设定虚拟用户个人vsftp的CentOS FTP服务文件存放路径。存放虚拟用户个性的CentOS FTP服务文件(配置文件名=虚拟用户名)
user_config_dir=/etc/vsftpd/vuser_conf
#配置vsftpd日志（可选）
xferlog_enable=YES
xferlog_std_format=YES
xferlog_file=/var/log/xferlog
dual_log_enable=YES
vsftpd_log_file=/var/log/vsftpd.log
```

## 用户认证管理

``` bash
#安装Berkeley DB工具，很多人找不到db_load的问题就是没有安装这个包
# yum install db4 db4-utils

#创建用户密码文本，注意奇行是用户名，偶行是密码
# vim /etc/vsftpd/vuser_passwd.txt
test    ##账号
123456  ##密码
注意：不要有空行，一行账号 一行密码；奇数行为帐号，偶数行为密码

#生成虚拟用户认证的db文件
# db_load -T -t hash -f /etc/vsftpd/vuser_passwd.txt /etc/vsftpd/vuser_passwd.db

#编辑认证文件，全部注释掉原来语句，再增加以下两句
# vim /etc/pam.d/vsftpd
auth required pam_userdb.so db=/etc/vsftpd/vuser_passwd
account required pam_userdb.so db=/etc/vsftpd/vuser_passwd

#创建虚拟用户配置文件
# mkdir /etc/vsftpd/vuser_conf/

#文件名等于vuser_passwd.txt里面的账户名，否则下面设置无效
# vim /etc/vsftpd/vuser_conf/test
#虚拟用户根目录,根据实际情况修改
local_root=/home/ftproot/pub
write_enable=YES                ##写权限
anon_umask=022                  ##由于web页面的特殊性，故单独设置上传文件权限为755，此掩码值可根据具体情况更改
anon_world_readable_only=NO
anon_upload_enable=YES          ##上传权限
anon_mkdir_write_enable=YES     ##新建目录权限
anon_other_write_enable=YES     ##删除/重命名权限
```

## 设置FTP根目录权限
``` bash
#最新的vsftpd要求对主目录不能有写的权限所以ftp为755，主目录下面的子目录再设置777权限
# mkdir /home/ftproot
# chmod -R 755 /home/ftproot
# chmod -R 777 /home/ftproot/pub

#建立限制用户访问目录的空文件
# touch /etc/vsftpd/chroot_list

#如果启用vsftpd日志需手动建立日志文件
# touch /var/log/xferlog 
# touch /var/log/vsftpd.log
```

## 设置iptable开放21端口 
``` bash
# vim /etc/sysconfig/iptables
-A INPUT -m state --state NEW -m tcp -p tcp --dport 21 -j ACCEPT
```

## 常见问题 
### 553 Could not create file
``` bash
1、首先确保你的FTP目录 文件是可写的，ls -l 可以查看 如果不是则 chmod 777 ftpd目录
2、selinux设置问题
使用命令getsebool -a|grep ftp 查看
ftpd_disable_trans ftp_home_dir allow_ftpd_full_access 是否为on 
如果不是则使用
# setsebool allow_sftpd_full_access on 
# setsebool ftpd_disable_trans on
# setsebool ftp_home_dir on
``` 

### 读取列表失败
把iptable stop

该错误是由iptables的配置引起的,临时解决方法
``` bash
# modprobe ip_nat_ftp
```
但重启后又恢复 

为解决，需要修改/etc/sysconfig/iptables-config文件
``` bash
# vim /etc/sysconfig/iptables-config
# Load additional iptables modules (nat helpers)
#   Default: -none-
# Space separated list of nat helpers (e.g. 'ip_nat_ftp ip_nat_irc'), which
# are loaded after the firewall rules are applied. Options for the helpers are
# stored in /etc/modprobe.conf.
IPTABLES_MODULES=""

# Unload modules on restart and stop
#   Value: yes|no,  default: yes
# This option has to be 'yes' to get to a sane state for a firewall
# restart or stop. Only set to 'no' if there are problems unloading netfilter
# modules.
IPTABLES_MODULES_UNLOAD="yes"

IPTABLES_MODULES_UNLOAD="yes"的意义：每次iptables停止或者重启都会Unload modules

IPTABLES_MODULES=""的意义：每次防火墙规则应用以后加载的模块

我们需要把
IPTABLES_MODULES=""
修改为：
IPTABLES_MODULES="ip_nat_ftp"
这样重启服务器之后就不必再每次都执行mobprobe啦
```