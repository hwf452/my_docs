1. 在/root下新建一个脚本bootstart.sh
vi bootstart.sh
并在bootstart.sh中输入想要执行的命令。

2.让bootstart.sh有执行权限
chmod 777 bootstart.sh

3. 执行如下命令将/etc/rc.d/rc.local文标记为可执行文件
在centos7中,/etc/rc.d/rc.local文件的权限被降低了,开机的时候执行在自己的脚本是不能起动一些服务的,执行下面的命令可以文件标记为可执行的文件
chmod 777 /etc/rc.d/rc.local

4.打开/etc/rc.d/rc.local文件.
vi /etc/rc.d/rc.local
在最后面exit0之前添加如下脚本
/root/bootstart.sh

5.这样,bootstart.sh这个脚本在开机的时候就会被执行了,以后再这里面写启动服务的命令就可以了
