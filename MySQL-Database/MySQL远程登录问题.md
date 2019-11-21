# MySQL远程登录问题   
#### 作者:coresu   
#### 时间:2019-11-21    


### 目录  
[一、实验准备](#一、实验准备)   
[二、开始实验](#二、开始实验)   
[三、实验总结](#三、实验总结)   
[四、【引申实验】](#四、【引申实验】)

### 一、实验准备  
#### 主机1：  
```
[root@chris ~]# mysql -uroot -poracle
......
......
Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> select user,host,password from mysql.user;
+------+--------------+-------------------------------------------+
| user | host         | password                                  |
+------+--------------+-------------------------------------------+
| root | localhost    | *2447D497B9A6A15F2776055CB2D1E9F86758182F |
| root | 127.0.0.1    | *E74858DB86EBA20BC33D0AECAE8A8108C56B17FA |
| root | ::1          | *E74858DB86EBA20BC33D0AECAE8A8108C56B17FA |
| jim  | localhost    | *E56A114692FE0DE073F9A1DD68A00EEB9703F3F1 |
| test | 192.168.9.12 | *2447D497B9A6A15F2776055CB2D1E9F86758182F |
+------+--------------+-------------------------------------------+
5 rows in set (0.00 sec)

mysql> exit
Bye

[root@chris ~]# cat /etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6

[root@chris ~]# getenforce
Permissive
[root@chris ~]# service iptables status
iptables: Firewall is not running.
```
##### 此处可以看出在主机1上，建立了一个test用户，且连接host为192.168.9.12。防火墙和selinux均处于关闭状态！   

#### 主机2： 

```
[root@ogg2 ~]# mysql -uroot -poracle
.......
.......
mysql> select user,host,password from mysql.user;
+------+-----------+-------------------------------------------+
| user | host      | password                                  |
+------+-----------+-------------------------------------------+
| root | localhost | *2447D497B9A6A15F2776055CB2D1E9F86758182F |
| root | ogg2      | *A2DFE6714EA555E2A6944BFF9573BF820DBC9C1F |
| root | 127.0.0.1 | *A2DFE6714EA555E2A6944BFF9573BF820DBC9C1F |
| root | ::1       | *A2DFE6714EA555E2A6944BFF9573BF820DBC9C1F |
+------+-----------+-------------------------------------------+
4 rows in set (0.00 sec)

mysql> exit
Bye

[root@ogg2 ~]# cat /etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4

[root@ogg2 ~]# getenforce
Disabled
[root@ogg2 ~]# service iptables status
iptables: Firewall is not running.
```
##### 此处可以看出在主机2上，不存在test用户,不会出现远程连接test用户时候的本地登录干扰问题。防火墙和selinux均处于关闭状态！

### 二、开始实验  

```
[root@ogg2 ~]# mysql -h 192.168.9.12 -utest -poracle
Warning: Using a password on the command line interface can be insecure.
ERROR 1130 (HY000): Host '192.168.9.99' is not allowed to connect to this MySQL server
```
##### 此处可以看到，主机2通过TCP/IP的方式远程登录主机1，以192.168.9.12 IP登录失败。   

```
mysql> update mysql.user set host='192.168.9.99' where user='test';
Query OK, 1 row affected (0.00 sec)
Rows matched: 1  Changed: 1  Warnings: 0

mysql> select user,host,password from mysql.user;
+------+--------------+-------------------------------------------+
| user | host         | password                                  |
+------+--------------+-------------------------------------------+
| root | localhost    | *2447D497B9A6A15F2776055CB2D1E9F86758182F |
| root | 127.0.0.1    | *E74858DB86EBA20BC33D0AECAE8A8108C56B17FA |
| root | ::1          | *E74858DB86EBA20BC33D0AECAE8A8108C56B17FA |
| jim  | localhost    | *E56A114692FE0DE073F9A1DD68A00EEB9703F3F1 |
| test | 192.168.9.99 | *2447D497B9A6A15F2776055CB2D1E9F86758182F |
+------+--------------+-------------------------------------------+
5 rows in set (0.05 sec)
```
##### 上表操作：在主机1上更改msyql.user表，设置连接host为192.168.9.99。
#### 主机2
```
[root@ogg2 ~]# mysql -h 192.168.9.12 -utest -poracle
Warning: Using a password on the command line interface can be insecure.
ERROR 1130 (HY000): Host '192.168.9.99' is not allowed to connect to this MySQL server
```
##### 仍然连接失败，但是此处非常奇怪,上面的错误提示确是Host '192.168.9.99' is not allowed。于是继续试验，猜测可能是主机1的user表没有刷新导致的！
####主机1
```
mysql> flush privileges;
Query OK, 0 rows affected (0.00 sec)
```
####主机2  
```
[root@ogg2 ~]# mysql -h 192.168.9.12 -utest -poracle
Warning: Using a password on the command line interface can be insecure.
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 56
Server version: 5.6.39 MySQL Community Server (GPL)

Copyright (c) 2000, 2018, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

```
##### 可以看出主机2连接成功，至此实验结束！！！    


#### 三、实验总结   

1. 可以看出在远程连接过程中，MySQL服务器端（主机1）在登录时检查的是远程连接客户端的的IP地址，也就是host！
2. 在MySQL服务端（主机1）修改用户的权限后，即使本地的mysql.user已经更新，还是需要flush privileges;来刷新权限，在实验中发现初次创建用户使不需要，猜测可能是更新的数据没有被读入内存无法被MySQL实例处理！  
3. 【引申】：因为客户端（主机2）在远程登录MySQL服务端时，服务器端（主机1）要求mysql.user表中host的内容为远程客户机的IP，故在host文件处的主机名可以设置为ogg2。！！！！实验过程已在【引申实验】中给出论证！
4. 【注意】：在MySQL服务端启动数据库过程中修改hosts文件需要重启数据库，才能生效对host文件中用户名的修改！！！！！！实验过程已在【引申实验】中给出论证！


### 四、【引申实验】

#### 主机1
```
mysql> create user 'hosttest'@'ogg2' identified by 'oracle';
Query OK, 0 rows affected (0.29 sec)

mysql> select user,host from mysql.user;
+----------+--------------+
| user     | host         |
+----------+--------------+
| root     | 127.0.0.1    |
| test     | 192.168.9.99 |
| root     | ::1          |
| jim      | localhost    |
| root     | localhost    |
| hosttest | ogg2         |
+----------+--------------+
6 rows in set (0.00 sec)

mysql> exit
Bye

[root@chris ~]# cat /etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
192.168.9.99 ogg2
```
##### 在MySQL服务端（主机1）上，重新创建测试用户hosttest，为接下来的host文件相关测试做准备。


#### 主机2
```
[root@ogg2 ~]# mysql -h 192.168.9.12 -uhosttest -poracle
Warning: Using a password on the command line interface can be insecure.
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 14
Server version: 5.6.39 MySQL Community Server (GPL)

Copyright (c) 2000, 2018, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
```
##### 发现主机2正常登陆！！！

#### 主机1
```
[root@chris ~]# vi /etc/hosts
这一步将hosts文件中的ogg2标识改为其他用户，

[root@chris ~]# cat /etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
192.168.9.12 ogg2
```

##### 在主机1上修改host文件，这次采用192.168.9.12，也可宜采用其他IP，主要目的是为了把ogg2的IP修改为不是192.168.9.99，这里采用12是想一举两得，直接测试MySQL服务端（主机1）是否能识别远程客户端（主机2）。

#### 主机2
```
[root@ogg2 ~]# mysql -h 192.168.9.12 -uhosttest -poracle
Warning: Using a password on the command line interface can be insecure.
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 16
Server version: 5.6.39 MySQL Community Server (GPL)

Copyright (c) 2000, 2018, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

```
##### 发现还是正常登陆，很是奇怪并没有按照猜想进行。因为，原本猜想MySQL服务端应该要求mysql.user表中的测试用户的IP是服务端（主句2）的IP啊！！！！！！！

#### 主机1
```
mysql> flush privileges;
Query OK, 0 rows affected (0.00 sec)
```
##### 不甘心的我！刷新了一下内存!!!!!!!!!!!

#### 主机2
```
[root@ogg2 ~]# mysql -h 192.168.9.12 -uhosttest -poracle
Warning: Using a password on the command line interface can be insecure.
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 17
Server version: 5.6.39 MySQL Community Server (GPL)

Copyright (c) 2000, 2018, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

```
##### 发现主机2还是正常登陆!!!!!!!!!这里非常奇怪~~~

#### 主机1
```
[root@chris ~]# vi /etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
192.0.0.0    ogg2

wq保存

[root@chris ~]# cat /etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
192.0.0.0 ogg2


mysql> flush privileges;
Query OK, 0 rows affected (0.00 sec)
```

#### 主机2
```
[root@ogg2 ~]# mysql -h 192.168.9.12 -uhosttest -poracle
Warning: Using a password on the command line interface can be insecure.
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 17
Server version: 5.6.39 MySQL Community Server (GPL)

Copyright (c) 2000, 2018, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

```
##### IP都改成这样可以了，刷完信息表后尽然还能登录！！！惊呆了😮！！

#### 主机1
```
[root@chris ~]# cat /etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
192.168.9.12 ogg2

[root@chris ~]# service mysql restart
Shutting down MySQL..                                      [  OK  ]
Starting MySQL...                                          [  OK  ]
```
##### 不甘心的我！再一次的尝试，重启了MySQL数据库服务。

#### 主机2  
```
[root@ogg2 ~]# mysql -h 192.168.9.12 -uhosttest -poracle
Warning: Using a password on the command line interface can be insecure.
ERROR 1045 (28000): Access denied for user 'hosttest'@'192.168.9.99' (using password: YES)
```

##### 终于远程客户端无法登陆，猜测可能是MySQL数据库服务没有自动重新读取/etc/hosts文件中的内容到内存中，进而无法被MySQL服务端实例处理。
##### -----至此实验结束！！！-----









