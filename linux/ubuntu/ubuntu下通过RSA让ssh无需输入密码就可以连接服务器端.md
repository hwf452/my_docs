1.在/Users/skyinno（用户根目录下）创建 .ssh文件夹。
mkdir .ssh

2.进入.ssh目录下用命令行ssh-keygen，并按三下回车生成RSA公私钥对,这样就可以在.ssh目录下生成id_rsa（私钥）和id_rsa.pub（公钥）两个文件。
cd .ssh
ssh-keygen

3.登录服务器，并创建.ssh文件夹。
mkdir .ssh

4.通过命令行把id_rsa.pub拷贝到远程服务器
scp ~/.ssh/id_rsa.pub root@203.78.141.233:/root/.ssh/authorized_keys
//如果想用webhooks也要把私钥上传到服务器端，并且在码云git上创建公钥并输入公钥字符串。这样码云和服务器端也可以无密码ssh登录。木
scp ~/.ssh/id_rsa root@203.78.141.233:/root/.ssh/

5.这样用户用终端登录方式：ssh -p 端口 用户名@主机IP  就可以无密码远程ssh登录服务器了。
ssh -p 22 root@213.178.41.233