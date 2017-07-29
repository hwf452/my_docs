# 前言

在我们本地开发好一个 NodeJS 项目，如果想要给别人看的话一般来说都是需要部署到服务器上面的。如果你使用 github 或者 coding 这里代码托管的服务，只需要在服务器安装好环境且安装好 git 之后，把项目 clone 下来然后使用 pm2 来启动自己的 NodeJS 项目就行了。

但是，如果我更新了代码到了远程仓库去了，而服务器还是以前的老代码，你还是需要登录到服务器上面并且用 git 命令把最新的代码拉下来，在重启一次项目。久而久之，如果更改代码频繁的话，每次都是需要自己登录到服务器上手动部署也不是一个好办法。

刚好我们又用到了一些代码托管的服务，且有 WebHook 这种好东西，怎么不拿来用一用呢！

# 编写自动部署脚本

1.在我们确定使用 WebHook 来实现自动部署后，我们需要一个脚本去执行一些命令。在使用 Linux 的服务器下我们可以使用 shell 来写这个自动部署脚本，这里我把文件命名为auto_build.sh：
在项目根目录下创建名为 auto_build.sh 的文件，内容如下：。

sudo git reset --hard origin/master
sudo git clean -f
sudo git pull origin master
sudo chmod 755 auto_build.sh
sudo npm install
sudo npm run pm2restart



2.在我们的项目根目录下创建名为webhook.js文件，内容如下：。

const http = require('http');
const process = require('child_process')

const server = http.createServer()

const handleRequest=function (req, res) {
  console.log('收到客户端请求url:'+req.url)

  const url = req.url
  if (url==='/') {
    process.exec('cd /root/expressTest | bash ./auto_build.sh',function (error, stdout, stderr) {
      if (error !== null) {
        console.log('exec error: ' + error);
      }
    });
    res.end('success')
  }
}

server.on('request',handleRequest)

server.listen(8888,function () {
  console.log('server is running')

})


3.提交代码到git仓库。

4.登录码云git，进入项目，进入管理，点左边的webhooks，在url处输入：http://203.78.141.233:8888，并勾选Push.  点   提交。

5.进入码云修改个人资料栏的 点击 ssh公钥 输入标题，  公钥  ，点提交。

5.服务器端安装好 git，Node.js环境，pm2。

6.服务器创建.ssh文件夹
mkdir .ssh

7.把私钥 id_rsa从 mac端上传放到 .ssh文件夹下。
scp ~/.ssh/id_rsa root@253.78.121.233:/root/.ssh/

8.服务器进入/root目录把项目从git上克隆代码下来。
git clone git@git.oschina.net:edaozhuhai/expressTest.git

9.进入项目根目录,执行sh
cd expressTest
sh auto_build.sh



10.用 pm2 启动webhooks.js
pm2 start webhooks.js

11.启动项目。
npm run pm2start



结束。。。。。   