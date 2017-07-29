系统架构

服务器部署Node的应用，并在8888端口进行监听。本地代码开发测试后，更新到Git码云私人仓库。然后通过pm2部署远程服务器。

服务器端安装必要的软件

拟安装如下应用：Node、npm、pm2。
1.通过预留的账号密码登录系统，终端中输入：ssh -p 22 root@213.178.41.233

2.在服务器端把git和 node.js pm2 装上。




因为pm2的部署是通过ssh进行的，因此需要开通本地到远程服务器的无密码登录

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



本地pm2的ecosystem配置

在本地的目标应用下，输入：
pm2 ecosystem

生成pm2的部署配置模板文件如下：

/**
* Application configuration section
* PM2 - Application Declaration
*/
apps : [
// First application
{
name      : "API",
script    : "app.js",
env: {
COMMON_VARIABLE: "true"
},
env_production : {
NODE_ENV: "production"
}
},
// Second application
{
name      : "WEB",
script    : "web.js"
}
],
/**
* Deployment section
* PM2 - Deployment
*/
deploy : {
production : {
user : "node",
host : "212.83.163.1",
ref  : "origin/master",
repo : "git@github.com:repo.git",
path : "/var/www/production",
"post-deploy" : "npm install && pm2 startOrRestart ecosystem.json --env production"
},
dev : {
user : "node",
host : "212.83.163.1",
ref  : "origin/master",
repo : "git@github.com:repo.git",
path : "/var/www/development",
"post-deploy" : "npm install && pm2 startOrRestart ecosystem.json --env dev",
env  : {
NODE_ENV: "dev"
}
}
}
}


应为目前我们仅部署一个应用，因此，先把不必要的信息删除，即删除apps部分的第二项。同时把我们的目标文件改为你应用的入口文件，此处修改为Express.js的默认设置，即：

script    : "./bin/www",


apps部分就设置完毕了，然后再设置deploy部分。其中production用于生产环境，dev用于开发环境，为了演示，我们只设置production部分。

production : {
user : "登录远程服务器的用户名，此处填写我们创建的yishi",
host : "远程服务器的IP或hostname，此处可以是数组同步部署多个服务器，不过鉴于我们只有一个服务器，因此我们填写123.57.205.23",
ref  : "远端名称及分支名，此处填写origin/master",
repo : "git仓库地址，此处填写git@github.com:e10101/pm2app.git",
path : "远程服务器部署目录，需要填写user具备写入权限的目录，此处填写/home/yishi/www/production",
"post-deploy" : "部署后需要执行的命令，此处填写npm install && pm2 startOrRestart ecosystem.json --env production"
},

整理后，按照我们的设置，应为：

production: {
user: "root",
host: "123.57.205.23",
ref: "origin/master",
repo: "git@git.oschina.net:edaozhuhai/expressTest.git",
path: "/root/www/production",
"post-deploy": "npm install && pm2 startOrRestart ecosystem.config.js --env production"
},


看看整理完毕的ecosystem配置文件，如下：

{
/**
* Application configuration section
* PM2 - Application Declaration
*/
apps : [
// First application
{
name      : "pm2app",
script    : "./bin/www",
env: {
COMMON_VARIABLE: "true"
},
env_production : {
NODE_ENV: "production"
}
}
],
/**
* Deployment section
* PM2 - Deployment
*/
deploy : {
production : {
user: "root",
host: "123.57.205.23",
ref: "origin/master",
repo: "git@git.oschina.net:edaozhuhai/expressTest.git",
path: "/root/www/production",
"post-deploy": "npm install && pm2 startOrRestart ecosystem.config.js --env production"
}
}
}

在本地应用目录下，执行pm2 deploy命令：
pm2 deploy ecosystem.config.js production setup


提示错误：

Host key verification failed.
fatal: Could not read from remote repository.
Please make sure you have the correct access rights and the repository exists.
failed to clone
Deploy failed

此时主要是在远程服务器中，并未将http://github.com加入known_hosts，在服务器端通过如下命令设置：
ssh-keyscan -t rsa git.oschina.net >> ~/.ssh/known_hosts


在本地继续执行部署命令：
pm2 deploy ecosystem.config.js production setup

此时，如无其他问题，输出应提示：
○ setup complete
--> Success

至此，pm2的部署设置完毕。

pm2部署

pm2的部署设置完毕后，接下来就是实际部署了。

在部署前，现将本地代码修改并进行git提交：
git add .
git commit -m "update ecosystem"
git push

提交后，在本地应用目录，输入如下命令进行生产环境的部署：
pm2 deploy ecosystem.config.js production

可以看到如下输出：

[PM2][WARN] Applications pm2app not running, starting...
[PM2] App [pm2app] launched (1 instances)
┌──────────┬────┬──────┬──────┬────────┬─────────┬────────┬─────────────┬──────────┐
│ App name │ id │ mode │ pid  │ status │ restart │ uptime │ memory      │ watching │
├──────────┼────┼──────┼──────┼────────┼─────────┼────────┼─────────────┼──────────┤
│ pm2app   │ 0  │ fork │ 1028 │ online │ 0       │ 0s     │ 11.246 MB   │ disabled │
└──────────┴────┴──────┴──────┴────────┴─────────┴────────┴─────────────┴──────────┘
Use `pm2 show <id|name>` to get more details about an app
○ hook test
○ successfully deployed origin/master
--> Success


部署成功

可以看到应用默认部署的3000端口已经开放了。通过浏览器打开：

http://123.57.205.23:3000/


