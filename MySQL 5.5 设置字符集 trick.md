# MySQL 5.5 设置字符集的小把戏

在MySQL 5.5中出现了中文乱码，想通过修改MySQL配置文件的字符集为utf8的方式一次性解决问题。

没有想到，用平时的方法修改后，启动MySQL一直报错。查看MySQL日志后，发现出现如下报错：

`[ERROR] /usr/sbin/mysqld: unknown variable 'default-character-set=utf8'`

通过查阅资料，发现如下方法可以解决问题，暂不刨根问底，先上干货：

1. 打开/etc/my.cnf；

2. 去除配置文件中出现的所有`default-character-set=utf8`；

3. 在`[mysqld]`下添加以下配置：

   `init_connect='SET collation_connection = utf8_unicode_ci'`
   `init_connect='SET NAMES utf8'`
   `character-set-server=utf8`
   `collation-server=utf8_unicode_ci`
   `skip-character-set-client-handshake`

4. 重启MySQL，果不其然，启动成功了。通过`show variables like 'character%'`，发现`character_set_server`变为`utf8`了。

原文链接：

http://outofcontrol.ca/blog/comments/change-mysql-5.5-default-character-set-to-utf8

MySQL官方手册：

https://dev.mysql.com/doc/refman/5.7/en/mysqld-option-tables.html