go的源码及下载地址

安装参考：https://github.com/golang/go

下载并安装

二进制分布

1.进入官方  https://golang.org/dl/  下载linux的二进制包go1.8.3.linux-amd64.tar.gz。

2.通过filezilla把二进制包上传到/root/downloads目录下。

3.登录服务器创建go目录。
cd /usr/local
mkdir go

4.复制二进制安装包到/usr/local/go/目录下。
cp /root/downloads/go1.8.3.linux-amd64.tar.gz /usr/local/

或直接下载二进制文件：
wget https://golang.org/dl/go1.8.3.linux-amd64.tar.gz

5.修改安装包权限
chmod 755 go1.8.3.linux-amd64.tar.gz

6.解压文件
tar -zxvf go1.8.3.linux-amd64.tar.gz

7.删除安装包
rm -rf go1.8.3.linux-amd64.tar.gz

8.链接文件,使go在任何目录下都可执行。
sudo ln -s /usr/local/go/bin/go /usr/bin/go
sudo ln -s /usr/local/go/bin/godoc /usr/bin/godoc
sudo ln -s /usr/local/go/bin/gofmt /usr/bin/gofmt

9.安装完成后，打开终端输入：
go version  
如果有如下输出：
go version go1.8.3 darwin/amd64
表示安装完成。

配置环境变量
sudo vim /etc/profile

添加下面的内容：
export GOPATH="/root/go"
export GOROOT="/usr/local/go"
export GOBIN=$GOPATH/bin
export PATH=$GOPATH/bin:$PATH

保存退出
source /etc/profile

退出终端。
重新进入终端 

go env

在新建GOPATH下新建三个目录
mkdir src pkg bin

完成。

