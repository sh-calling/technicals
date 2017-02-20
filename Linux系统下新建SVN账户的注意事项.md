# Linux系统下新建SVN账户的注意事项

由于工作需要，这几天在CentOS系统中，以命令行的形式对已有SVN进行了创建新用户的操作。

操作的方式如下：

1. 定义到SVN所在路径，找到auth文件；

   如该文件为：`/home/svn/repository/auth.conf`

2. 使用`htpasswd`命令，创建新用户JohnSmith：

   `htpasswd /home/svn/repository/auth JohnSmith`

3. 根据提示，依次输入两次密码，大功告成。

由于权限复用了原有权限设置，故不在此详细描述。

其实上文的配置非常简单，但坑就在于若是初次创建用户文件，需要对`htpasswd`命令追加`-c`参数，即：

`htpasswd -c /home/svn/repository/auth JohnSmith`

但如果用户文件已存在，文件中也已经有了项目人员的用户信息，那么再使用**`-c`**参数，**就会将整个`auth.conf`重置**！！因此，这必须慎重！！