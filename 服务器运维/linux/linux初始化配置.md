## # 初始化工作

## 1.开启ssh

开启 `service sshd start`

检查是否启用`netstat -anp|grep :22`

## 2.关闭防火墙

关闭 `systemctl top firewalld`

查看`systemctl status firewalld`

启动 `systemctl start firewalld`

## 3.修改root密码

`passwd`