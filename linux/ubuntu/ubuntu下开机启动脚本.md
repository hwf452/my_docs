要让ubuntu开机启动后执行sh脚本通常有两三种方法：

1.直接执行rc.local脚本

rc.local脚本是一个ubuntu开机后会自动执行的脚本，我们可以在该脚本内添加命令行指令。该脚本位于/etc/路径下，需要root权限才能修改。

 vi /etc/rc.local
 在exit 0之前加入要执行的命令。
 
 注意: 一定要将命令添加在 exit 0之前


2.在rc.local脚本下执行外部的sh


1. 在/root下新建一个脚本bootstart.sh
vi bootstart.sh
并在bootstart.sh中输入想要执行的命令。

2.让bootstart.sh有执行权限
chmod 777 bootstart.sh

3.编辑rc.local脚本，在该脚本内添加执行外部sh命令行指令。
vi /etc/rc.local
在exit 0之前加入如下命令。
/root/bootstart.sh


3.给ubuntu添加一个开机启动脚本

1. 在/root下新建一个脚本bootstart.sh
vi bootstart.sh
并在bootstart.sh中输入想要执行的命令。

2.让bootstart.sh有执行权限
chmod 777 bootstart.sh

3,把脚本放置到启动目录下
sudo cp /root/bootstart.sh /etc/init.d/

4,将脚本添加到启动脚本
执行如下指令，在这里90表明一个优先级，越高表示执行的越晚
cd /etc/init.d/
sudo update-rc.d bootstart.sh defaults 90



如果要移除Ubuntu开机脚本
sudo update-rc.d -f bootstart.sh remove
