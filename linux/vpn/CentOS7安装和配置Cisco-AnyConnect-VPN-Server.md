
在 CentOS 7 上搭建 Cisco AnyConnect VPN
===========================================================

# 1.简介 #

因为最近的干扰力度变大，考虑到 AnyConnect 是思科的安全远程接入解决方案，隐蔽性要好一些，所以决定在服务器上搭建 AnyConnect 以提供给 iOS 设备使用，原来的 Cisco IPSec VPN 废弃，Shadowsocks 保留用于安卓和PC的连接。

AnyConnect 有以下优势：

待机不会断开
能够下发路由表给客户端（未测试）
稳定
耗电量较低

2015-11-26 更新：添加证书登录方式

2015-12-02 更新：更新转发规则

# 2. 安装 ocserv (OpenConnect Server) #

ocserv 是一个 OpenConnect SSL VPN 协议服务端，0.3.0 版后兼容使用 AnyConnect SSL VPN 协议的终端。
官方主页：http://www.infradead.org/ocserv/

ocserv 已经在 epel 仓库中提供了，所以可以直接通过 yum 安装
//先升级yum到最新版本再安装
$yum update
$ yum install epel-release
$ yum install ocserv


# 3. 配置 ocserv #

## 3.1. 创建工作文件夹 ##

```shell
$ mkdir /etc/ocserv
$ mkdir anyconnect
$ cd anyconnect
```



## 3.2. CA 证书 ##

我们创建一个 `CA` 模板文件（`ca.tmpl`），其中内容如下，你可以修改其中的 `cn` 和 `organization` 字段：

```bash
$ vim ca.tmpl

cn = "VPN CA"
organization = "Cisco Inc."
serial = 1
expiration_days = 3650
ca
signing_key
cert_signing_key
crl_signing_key
```

然后，生成一个随机的 `CA key`，并用这个 `key` 和 `ca.tmpl` 模板生成 `CA 证书`（`ca-cert.pem`），如下：

```shell
$ certtool --generate-privkey --outfile ca-key.pem
$ certtool --generate-self-signed --load-privkey ca-key.pem --template ca.tmpl --outfile ca-cert.pem
```

## 3.3. Server 证书 ##

接下来，我们创建一个 `server` 证书模板（`server.tmpl`），内容如下。请注意其中的 `cn` 字段，它必须是你的服务器的域名或者 `IP` 地址。

```bash
$ vim server.tmpl

cn = "202.103.204.68"
organization = "Cisco Inc."
serial = 2
expiration_days = 3650
signing_key
encryption_key
tls_www_server
```

例如，修改成这样，如果你的服务器没有域名，则 `cn` 字段必须使用服务器的 `IP` 地址（这里一定要写正确，否则是连不上的），例如：

```bash
cn = "202.103.108.20"
organization = "Microsoft Inc."

或者

cn = "www.example.com"
organization = "Cisco AnyConnect"
```

（注：这里的 `organization` 字段可以跟 `ca.tmpl` 里的 `organization` 名字不一样。）

然后，生成 `server key`，并使用这个 `server key`、`CA key`、`CA 证书` 以及 `server.tmpl` 模板生成 `server 证书`（`server-cert.pem`），如下：

```shell
$ certtool --generate-privkey --outfile server-key.pem
$ certtool --generate-certificate --load-privkey server-key.pem --load-ca-certificate ca-cert.pem --load-ca-privkey ca-key.pem --template server.tmpl --outfile server-cert.pem
```

## 3.4. 拷贝证书 ##

新建一个 `/etc/ocserv` 目录，拷贝 `server 证书`  `server-key.pem`和 `server key` 到 `/etc/ocserv` 目录下：
```shell
$ sudo mkdir /etc/ocserv
$ sudo cp server-key.pem server-cert.pem server-key.pem /etc/ocserv
```

## 3.6. ocserv 配置文件 ##

在 `ocserv` 源代码里有一个简单的配置范例文件：`/ocserv-0.9.2/doc/sample.config`，把它复制到 `/etc/ocserv/` 目录下：

```shell
$ sudo cp ~/ocserv/ocserv-0.9.2/doc/sample.config /etc/ocserv/ocserv.conf
```
或者 
```shell
$ sudo vim /etc/ocserv/ocserv.conf
```
主要修改以下部分

编辑这个配置文件 `/etc/ocserv/ocserv.conf`，找到与下文相同的配置选项，并修改成下文里展示的内容，找到那几个关于 `route` 的配置项，像下面展示的一样，把它们都注释掉。最后一项 “`cisco-client-compat = true`” 在文件比较后面的地方，原本是被注释了的，将其注释去掉。

```bash
$ cd /etc/ocserv
$ sudo vim ./ocserv.conf

# ocserv 支持多种认证方式，这是自带的密码认证，使用 ocpasswd 创建密码文件
# ocserv 还支持证书认证，可以通过 Pluggable Authentication Modules (PAM) 使用 radius 等认证方式
auth = "plain[/etc/ocserv/ocpasswd]"

# 最大客户端连接数，默认值是 10
max-clients = 16

# 同一个用户名最大同时登陆连接数，默认值是 2
max-same-clients = 8

# tcp 和 udp 端口，默认值是 443，可以不用改
tcp-port = 443
udp-port = 443

# 默认是 false, 修改为 true
try-mtu-discovery = true

# 证书路径
server-cert = /etc/ocserv/server-cert.pem
server-key = /etc/ocserv/server-key.pem

# 运行用户和组，默认值是 nobody 和 daemon
run-as-user = ocserv
run-as-group = ocserv

# DNS 设置
dns = 8.8.8.8
dns = 8.8.4.4

# 请参考下面，注释掉所有的 route, noroute 的定义，这样的效果是 VPN 客户端所有的访问都通过 VPN 代理转发。
# 其中 route 字段表示使用 VPN 代理转发的网段，noroute 字段表示不使用 VPN 代理转发的网段。
# 注：最多仅支持 60 条 route 规则或 60 条 noroute 规则。这些路由规则是下发到 VPN 客户端的。

#route = 10.10.10.0/255.255.255.0
#route = 192.168.0.0/255.255.0.0
#route = fef4:db8:1000:1001::/64
#no-route = 192.168.5.0/255.255.255.0

cisco-client-compat = true
```

由于我们的配置文件里指定了 `ocserv` 用户和 `ocserv` 组，所以我们要添加这个用户和组，命令如下：

```shell
$ sudo groupadd -f -r -g 21 ocserv
$ sudo useradd -M -s /sbin/nologin -g ocserv ocserv
$ sudo passwd ocserv
$ sudo gpasswd -a ocserv sudo
```

其中第三步的时候会让你输入两遍密码，该密码不是很重要，但由于是系统用户，最好记得密码，且密码不能过于简单。

# 4. 其他配置 #

## 4.1. 创建 ocserv 用户 ##

创建 `ocserv` 用户的命令格式是：

```shell
$ sudo ocpasswd -c /etc/ocserv/ocpasswd username
```

例如，要创建一个用户叫 `test`，命令如下：

```shell
$ sudo ocpasswd -c /etc/ocserv/ocpasswd test
```

接着它会要求你输入两次密码，以便确认。

## 4.2. 打开 IP 转发 ##

由于 `VPN` 内部需要 `NAT` 功能，所以必须打开 `ipv4` 的转发，设置为如下值：

```shell
$ sudo vim /etc/sysctl.conf

net.ipv4.ip_forward = 1
```

修改保存后，让配置生效，执行下列命令：

```shell
$ sudo /usr/sbin/sysctl -p /etc/sysctl.conf
$ sudo sysctl -p
```

## 4.3. 配置 iptables 规则 ##

打开了 `IP` 转发，还需要启用和配置 `NAT`：

```shell
$ sudo iptables -t nat -A POSTROUTING -j MASQUERADE
```

可以通过下面的命令查看 `NAT` 的设置：

```shell
$ sudo iptables -t nat --list

Chain PREROUTING (policy ACCEPT)
target     prot opt source               destination         

Chain INPUT (policy ACCEPT)
target     prot opt source               destination         

Chain OUTPUT (policy ACCEPT)
target     prot opt source               destination         

Chain POSTROUTING (policy ACCEPT)
target     prot opt source               destination         
MASQUERADE  all  --  anywhere             anywhere  
```

其中最后一条就是我们刚才添加的路由规则。

由于路由配置重启了会失效，所以我们要把它保存到一个文件里，然后在重启的时候，再从这个文件来恢复路由配置。

下面的命令即是保存 `iptables` 的配置到 `/etc/iptables` 文件里：

```shell
$ sudo touch /etc/iptables
$ sudo iptables-save > /etc/iptables
```

在系统启动时，恢复 `iptables` 的配置

```shell
$ chmod 777 /etc/rc.d/rc.local
$ vi /etc/rc.d/rc.local
在最后面exit0之前添加如下脚本
/sbin/iptables-restore < /etc/iptables
exit 0
```

# 5. 启动 ocserv #

启动的命令如下：

```shell
$ sudo ocserv -c /etc/ocserv/ocserv.conf -f -d 1
```

如果没有问题，那么就可以配置成开机运行了。如下：

1. 在/root下新建一个脚本bootstart.sh
vi bootstart.sh
并在bootstart.sh中输入想要执行的命令。
systemctl start ocserv

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

或 

```shell
$ sudo systemctl enable ocserv
```


搞定，Enjoy it now !!

# 6. 参考文章 #

1. [Setup OpenConnect VPN Server for Cisco AnyConnect on Ubuntu 14.04 x64](https://www.vultr.com/docs/setup-openconnect-vpn-server-for-cisco-anyconnect-on-ubuntu-14-04-x64)

2. [在 CentOS 7 上搭建 Cisco AnyConnect VPN](http://blog.csdn.net/y87329396/article/details/48264731)

<.End.>
