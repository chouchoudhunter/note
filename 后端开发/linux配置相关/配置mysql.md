# 配置Mysql

## 1.安装

### 1.下载对应安装包

https://dev.mysql.com/downloads/mysql/

### 2.把下载的安装包移动到/usr/local/下

### 3.解压

解压tar.gz `tar zxvf 文件名`

解压tar格式 `tar xvf 文件名`

### 4.移动压缩包到usr/local目录下,并重命名文件

`mv 文件地址 /usr/local/mysql`

### 5.创建 mysql 用户组和 mysql 用户

`groupadd mysql`

`useradd -g mysql mysql`

### 6.改变 mysql 目录权限

`chown -R mysql.mysql /usr/local/mysql/`

### 7.初始化数据库

`bin/mysqld --initialize --user=mysql --basedir=/usr/local/mysql --datadir=/usr/local/mysql/data`

如果出现
`error while loading shared libraries: libaio.so.1: cannot open shared object file: No such file or directory`

安装`yum install  libaio-devel.x86_64`

### 8.配置mysql

`vim /etc/my.cnf`

[mysqld]
basedir=/usr/local/mysql
datadir=/usr/local/mysql/data
socket=/usr/local/mysql/mysql.sock
character-set-server=utf8
port = 3306

### 9建立服务

`cp -a ./support-files/mysql.server /etc/init.d/mysql`

`chmod +x /etc/init.d/mysql`

`chkconfig --add mysql`

`chkconfig --list mysql`

### 10.启动服务

`service mysql start`

如果出现`log-error set to '/var/log/mariadb/mariadb.log'` 创建路径文件, 并给mysql用户授权

`mkdir /var/log/mariadb`

`touch /var/log/mariadb/mariadb.log`

`chown -R mysql:mysql /var/log/mariadb/`

## 2.配置

登录前

`ln -sf /usr/local/mysql/bin/mysql  /usr/bin`

`ln -fs /usr/local/mysql/mysql.sock /tmp`

mysql -u root -p



Can't connect to local MySQL server through socket '/tmp/mysql.sock'