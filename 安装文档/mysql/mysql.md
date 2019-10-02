Centos 7 + 8.0.17 MySQL Community Server

卸载：

```shell
#查看yum安装列表
yum list installed mysql*
# 卸载yum安装
yum remove mysql-community-client mysql-community-common mysql-community-libs mysql-community-libs-compat mysql-community-server mysqlxx-community-release
rm -rf /var/lib/mysql  
rm /etc/my.cnf

#卸载rpm
rpm -qa | grep -i mysql
rpm -e mysql57-community-release-el7-9.noarch
rpm -e mysql-community-server-5.7.17-1.el7.x86_64
rpm -e mysql-community-libs-5.7.17-1.el7.x86_64
rpm -e mysql-community-libs-compat-5.7.17-1.el7.x86_64
rpm -e mysql-community-common-5.7.17-1.el7.x86_64
rpm -e mysql-community-client-5.7.17-1.el7.x86_64
cd /var/lib/  
rm -rf mysql/
#删除文件
whereis mysql
mysql: /usr/bin/mysql /usr/lib64/mysql /usr/local/mysql /usr/share/mysql /usr/share/man/man1/mysql.1.gz
#删除上面的文件夹
rm -rf /usr/bin/mysql

#删除配置

rm –rf /usr/my.cnf
rm -rf /root/.mysql_sercret
#剩余配置检查

chkconfig --list | grep -i mysql
chkconfig --del mysqld
```





获取rpm

https://dev.mysql.com/downloads/repo/yum/



```shell
#新建文件夹
mkdir -p /usr/share/mysql
#下载rpm
yum -y install wget
wget https://repo.mysql.com//mysql80-community-release-el7-3.noarch.rpm
#配置安装源
yum -y localinstall mysql80-community-release-el7-3.noarch.rpm 
#安装mysql
yum -y install mysql-community-server
#启动服务
systemctl start mysqld
```

启动报错：

<img src="C:\Users\admin\AppData\Roaming\Typora\typora-user-images\1569644124256.png" alt="1569644124256" style="zoom:80%;" />

文件未删干净，重新卸载安装

![1569648779285](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\1569648779285.png)

```shell
#设计为开机自启
systemctl enable mysqld
systemctl daemon-reload
```

mysql安装默认密码在：

```shell
vi /var/log/mysqld.log
```

![1569649008452](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\1569649008452.png)

通过默认密码登录后，修改密码：

```shell
ALTER USER 'root'@'localhost' IDENTIFIED BY '123456';
#mysql5.7默认密码策略要求密码必须是大小写字母数字特殊字母的组合，至少8位
```

设置允许远程登录，权限：

https://www.cnblogs.com/sos-blue/p/6852945.html

```mysql
-- 授权root用户可以远程登陆
GRANT ALL ON *.* TO 'root'@'%';
flush privileges;
ALTER USER 'root'@'localhost' IDENTIFIED BY 'yourpassword' PASSWORD EXPIRE NEVER; 
ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY 'yourpassword';
FLUSH PRIVILEGES; 
-- 授权 myuser 用户对指定库的所有表，所有权限并设置远程访问,库和表都可用* 替换
GRANT ALL ON 指定库.* TO 'myuser'@'%';

-- 更新 该 用户密码
ALTER USER 'myuser'@'%' IDENTIFIED WITH mysql_native_password BY 'yourpassword'; 
-- 刷新权限

flush privileges;
```

注意：MySQL8中密码加密规则改变，在Navicat连接验证错误，需要更改密码加密规则，为 mysql_natice_password

zhao   zhao@126.COM

root	zxzhao1994@126.COM

