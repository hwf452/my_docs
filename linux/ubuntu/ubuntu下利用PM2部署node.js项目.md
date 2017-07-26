利用PM2部署node.js项目的方法教程

pm2 = P (rocess) M (anager)2，是可以用于生产环境的Nodejs的进程管理工具，并且它内置一个负载均衡。下面这篇文章主要给大家介绍了利用PM2部署node.js项目的方法教程，需要的朋友可以参考借鉴，下面来一起看看吧。

前言

大家在开发中应该发现了，如果直接通过node start 来启动，如果报错了可能直接停在整个运行，supervisor感觉只是拿来用作开发环境的。再网上找到pm2.目前似乎最常见的线上部署nodejs项目的有forever,pm2这两种。下面本文将详细介绍利用PM2部署node.js项目的方法教程，需要的朋友们下面来一起看看详细的介绍：

使用场合:

    supervisor是开发环境用。
    forever管理多个站点，每个站点访问量不大，不需要监控。
    pm2 网站访问量比较大,需要完整的监控界面。

PM2的主要特性:

    内建负载均衡（使用Node cluster 集群模块）
    后台运行
    0秒停机重载，我理解大概意思是维护升级的时候不需要停机.
    具有Ubuntu和CentOS 的启动脚本
    停止不稳定的进程（避免无限循环）
    控制台检测
    提供 HTTP API
    远程控制和实时的接口API ( Nodejs 模块,允许和PM2进程管理器交互 )


pm2安装 命令行全局安装pm2(必须全局安装)
sudo npm install -g pm2

启动app项目
pm2 start ./bin/www

列出由pm2管理的所有进程信息，还会显示一个进程会被启动多少次，因为没处理的异常。 
pm2 list 

监视每个node进程的CPU和内存的使用情况
pm2 monit 

其它常用命令：

$ pm2 logs 显示所有进程日志

$ pm2 stop all 停止所有进程

$ pm2 restart all 重启所有进程

$ pm2 reload all 0秒停机重载进程 (用于 NETWORKED 进程)

$ pm2 stop 0 停止指定的进程

$ pm2 restart 0 重启指定的进程

$ pm2 startup 产生 init 脚本 保持进程活着

$ pm2 web 运行健壮的 computer API endpoint (http://localhost:9615)

$ pm2 delete 0 杀死指定的进程

$ pm2 delete all 杀死全部进程