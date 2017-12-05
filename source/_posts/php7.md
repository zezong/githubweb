---
title: php7安装
date: 2017-12-05 12:29:40
tags:
---

linux安装php7，安装多版本php
#### 下载
``` bash
# cd /usr/local/src
# http://cn2.php.net/distributions/php-7.1.12.tar.gz
```
#### 解压 
``` bash
# tar -xzvf php-7.1.12.tar.gz
```
#### 进入安装目录
``` bash
# cd php-7.1.12
```
#### 编译
``` bash
# ./configure --prefix=/opt/application/php7 --enable-fpm 
--with-fpm-user=nginx --with-fpm-group=nginx --with-mysqli --with-zlib 
--with-curl --with-gd --with-jpeg-dir --with-png-dir --with-freetype-dir 
--with-openssl --enable-mbstring --enable-xml --enable-session --enable-ftp 
--enable-pdo -enable-tokenizer --enable-zip 
--with-pdo-mysql=/opt/application/mysql --enable-mysqlnd
```
--prefix  php安装目录 <br />
--with-pdo-mysql 设置mysql连接方式为pdo_mysql，这里写mysql安装目录 
#### 编译成功会出现类似下面的提示
``` bash
+--------------------------------------------------------------------+
| License:                                                           |
| This software is subject to the PHP License, available in this     |
| distribution in the file LICENSE.  By continuing this installation |
| process, you are bound by the terms of this license agreement.     |
| If you do not agree with the terms of this license, you must abort |
| the installation process at this point.                            |
+--------------------------------------------------------------------+
Thank you for using PHP.
```
#### 安装 
``` bash
# make
# make install
```
#### 配置
``` bash
# cp /usr/local/src/php-7.1.12/php.ini-development  /opt/application/php7/lib/php.ini
# vim /opt/application/php7/lib/php.ini
```
修改pdo_mysql连接，查找pdo_mysql.default_socket,修改为
``` bash
pdo_mysql.default_socket = /home/data/3307/mysql.sock
```
修改时区
``` bash
date.timezone = Asia/Shanghai
```
#### 验证安装 
``` bash
# /opt/application/php7/bin/php -v
PHP 7.1.12 (cli) (built: Dec  5 2017 10:15:50) ( NTS )
Copyright (c) 1997-2017 The PHP Group
Zend Engine v3.1.0, Copyright (c) 1998-2017 Zend Technologies
```
配置php-fpm
``` bash
# cp /opt/application/php7/etc/php-fpm.conf.default /opt/application/php7/etc/php-fpm.conf
# cp /opt/application/php7/etc/php-fpm.d/www.conf.default /opt/application/php7/etc/php-fpm.d/www.conf
```
#### 修改 www.conf
``` bash
# vim /opt/application/php7/etc/php-fpm.d/www.conf
```
将
``` bash
user = nobody
group = nobody
```
修改为
``` bash
user = nginx
group = nginx
```
修改要使用的端口
``` bash
listen = 127.0.0.1:9001
```
#### 配置php-fpm启动脚本
``` bash
# 可直接运行
# /opt/application/php7/sbin/php-fpm
```
配置成服务启动
``` bash
# cp /usr/local/src/php-7.1.12/sapi/fpm/php-fpm.service /usr/lib/systemd/system/
```
``` bash
# vim /usr/lib/systemd/system/php-fpm.service
```
将
``` bash
PIDFile=${prefix}/var/run/php-fpm.pid
ExecStart=${exec_prefix}/sbin/php-fpm --nodaemonize --fpm-config ${prefix}/etc/php-fpm.conf
```
修改成
``` bash
PIDFile=/opt/application/php7/var/run/php-fpm.pid
ExecStart=/opt/application/php7/sbin/php-fpm --nodaemonize --fpm-config /opt/application/php7/etc/php-fpm.conf
```
重新载入 systemd
``` bash
# systemctl daemon-reload
```
让 php-fpm 随机启动
``` bash
# systemctl enable php-fpm
ln -s '/usr/lib/systemd/system/php-fpm.service' '/etc/systemd/system/multi-user.target.wants/php-fpm.service'
```
立即启动 php-fpm
``` bash
# systemctl start php-fpm
```