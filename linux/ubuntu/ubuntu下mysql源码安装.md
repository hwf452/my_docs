1.通过fz上传源码到/root/downloads下

2.解压包
tar -xf mysql-5.5.57.tar

3.安装必要依赖
sudo apt-get install make bison g++ build-essential libncurses5-dev cmake

4.安装

cmake . -DCMAKE_INSTALL_PREFIX=/usr/local/mysql -DMYSQL_DATADIR=/usr/local/mysql/data -DSYSCONFDIR=/etc -DWITH_INNOBASE_STORAGE_ENGINE=1 -DWITH_ARCHIVE_STORAGE_ENGINE=1 -DWITH_BLACKHOLE_STORAGE_ENGINE=1 -DWITH_PARTITION_STORAGE_ENGINE=1 -DWITH_PERFSCHEMA_STORAGE_ENGINE=1 -DWITHOUT_EXAMPLE_STORAGE_ENGINE=1 -DWITHOUT_FEDERATED_STORAGE_ENGINE=1 -DDEFAULT_CHARSET=utf8 -DDEFAULT_COLLATION=utf8_general_ci -DWITH_EXTRA_CHARSETS=all -DENABLED_LOCAL_INFILE=1 -DWITH_READLINE=1 -DMYSQL_UNIX_ADDR=/usr/local/mysql/mysql.sock -DMYSQL_TCP_PORT=3306 -DCOMPILATION_COMMENT="lq-edition"-DENABLE_DTRACE=1 -DWITH_DEBUG=1

编译：
make
安装：
sudo make install

配置MySQL
（1）新建运行Mysql的用户和组
sudo groupadd mysql
sudo useradd -g mysql mysql

（2）设置Mysql安装目录的权限
cd /usr/local/mysql
sudo chown -R mysql:mysql ./



5.安装完成后进入安装目录，将配置文件放到/etc下面
cp /root/downloads/mysql-5.5.57/support-files/my-medium.cnf /etc/my.cnf

（3）建立配置文件
cp support-files/my-default.cnf /etc/my.cnf
sudo chown mysql:mysql /etc/my.cnf
修改配置文件：
sudo vi /etc/my.cnf
[client]
port = 3306
socket = /usr/local/mysql/data/mysql.sock
[mysqld]
port = 3306
socket = /usr/local/mysql/data/mysql.sock
basedir = /usr/local/mysql
datadir  = /usr/local/mysql/data


（4）初始化数据库
cd /usr/local/mysql
sudo scripts/mysql_install_db --user=mysql --basedir=/usr/local/mysql --datadir=/usr/local/mysql/data/


（5）启动mysql服务

cd /usr/local/mysql/
cp support-files/mysql.server /etc/init.d/mysql
设置文本的权限：
sudo chmod 755 /etc/init.d/mysql
启动：
sudo /etc/init.d/mysql start
(关闭mysql服务：sudo /etc/init.d/mysql stop)
或者
sudo service mysql start
(关闭mysql服务：sudo service mysql stop)



8. 常用命令软连接，设置环境变量
ln -s /usr/local/mysql/bin/mysql /usr/bin
ln -s /usr/local/mysql/bin/mysqladmin /usr/bin

9.启动成功后创建root用户密码（路径是安装目录下的bin）
/etc/init.d/mysql start

mysqladmin -u root password 'skyinno251'
mysqladmin -u root password 'skyinno251'

apt-get install flush





检查MySQL服务是否启动：
ps -ef |grep mysql

查看端口号
netstat -lntup|grep 3306


启动：
sudo /etc/init.d/mysql start
(关闭mysql服务：sudo /etc/init.d/mysql stop)
或者
sudo service mysql start
(关闭mysql服务：sudo service mysql stop)

vi /etc/rc.local

在后面加上：
/etc/init.d/mysql start
保存退出 重启 。








一、创建用户：
1、使用命令 useradd
例：useradd user1——创建用户user1
useradd –e 12/30/2009 user2——创建user2,指定有效期2009-12-30到期
用户的缺省UID从500向后顺序增加，500以下作为系统保留账号，可以指定UID，
例：useradd –u 600 user3

2、使用 passwd 命令为新建用户设置密码
例：passwd user1
注意：没有设置密码的用户不能使用。

3、命令 usermod 修改用户账户
例：将用户 user1的登录名改为  u1，
usermod –l u1 user1
例：将用户 user1 加入到 users组中，
usermod –g users user1

例：将用户 user1 目录改为/users/us1
usermod –d /users/us1 user1

4、使用命令 userdel 删除用户账户
例：删除用户user2
userdel user2
例：删除用户 user3，同时删除他的工作目录
userdel –r user3

5、查看用户信息
id命令查看一个用户的UID和GID, 例：查看user4的id
id user4
finger命令 ——可以查看用户的主目录、启动shell、用户名、地址、电话等信息
例：finger user4


二、用户组：
6、命令 groupadd创建用户组
groupadd –g 888 mysql
创建一个组users，其GID为888

7、命令 gpasswd为组添加用户
只有root和组管理员能够改变组的成员：
例：把 user1加入users组
gpasswd –a mysql mysql
例：把 user1退出users组
gpasswd –d user1 users
8、命令groupmod修改组
groupmod –n user users       修改组名user为users

9、groupdel删除组
groupdel users    删除组users






