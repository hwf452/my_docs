go的源码及下载地址

安装参考：https://github.com/golang/go

下载并安装

二进制分布

1.进入官方二进制发行版可从  https://golang.org/dl/  获取,下载苹果安装包。

2.下载二进制版本后，请访问  https://golang.org/doc/install 或在您的Web浏览器中加载doc / install.html以获取安装说明。

3.下载后按提示安装 pkg包。

4.安装完成后，打开终端输入：
go version  
如果有如下输出：
go version go1.8.3 darwin/amd64
表示安装完成。

配置环境变量
cd
mkdir go
cd go
mkdir src pkg bin
cd 
vim .bash_profile

在末尾加入如下：
export GOPATH=/Users/skyinno/go
export GOBIN=$GOPATH/bin
export PATH=$PATH:$GOBIN

保存退出。
退出终端
重新进入终端
go env


完成。
