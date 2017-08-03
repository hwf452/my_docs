安装Delve，推荐 brew install go-delve/delve/delve ，不用自己配置很多麻烦的东西。

如果遭遇错误，应该就是/usr/local存在权限问题，sudo chmod -R 777 /usr/local  。

 

安装完毕后，打开Terminal，输入 dlv version 。

如果看到Delve Debugger的版本信息，则表明Delve安装成功了！


如果安装不成功则需要如下：




[重要的功能: 在Mac OS X系统让Visual Studio Code支持Go语言调试]
步骤1: 打开终端输入如下命令, 安装Go语言的调试工具"delve"
go get -v -u github.com/peterh/liner github.com/derekparker/delve/cmd/dlv
这个东西安装好之后, 会在你的GOPATH/bin目录下出现"dlv"执行文件, 在GOPATH/src目录出现dlv的源码文件目录:github.com/derekparker/delve
步骤2: 虽然安装好了Go语言调试工具"delve", 但由于该调试工具没有代码签名, 因此Visual Studio Code是无法激活此调试工具来调试Go语言. 因此我们需要通过如下重要的步骤来来解决这个问题:
如果你英文好可以看原文描述:https://github.com/derekparker/delve/wiki/Building
如果英文不好, 那就看我粗糙的翻译:
2.1> 打开"钥匙串访问"
2.2> 打开菜单 钥匙串访问/证书助理/创建证书...
2.3> 名称: dlv-cert 身份类型: 自签名证书 证书类型: 代码签名 并 选择"让我覆盖这些默认值"
2.4> 单击"继续", 有效期(天数): 365 这里你可以自己修改, 我改为3650
2.5> 一路继续下去, 直到看到"指定用于该证书的位置" 钥匙串 选择 "系统" 并单击"创建"按钮
2.6> 重启系统之后, 再打开"钥匙串访问", 选择"系统", 就会看到创建好的"dlv-cert"证书.
2.7> 右键"dlv-cert"证书, 选择"显示简介"->"信任"->"代码签名" 修改为: 始终信任
2.8> 打开终端然后cd命令进入之前你安装好的"GOPATH/src目录下的dlv源码文件目录:github.com/derekparker/delve"
2.9> 输入如下命令: GO15VENDOREXPERIMENT=1 CERT=dlv-cert make install 这样就可以重新编译出一个带有代码签名的dlv执行程序



原文【链接】MacOSX下VisualStudioCode搭建Golang(Go语言)开发环境
http://blog.csdn.net/code_godfather/article/details/51209841




[序言]
自从会Go语言之后, 唯一让我纠结的是没有强大的Go语言开发环境. 苦苦支撑了很久, Visual Studio Code 1.0.0版本横空发布. 并有着完美的Go语言整合插件, 也并完美的支持了Go语言的调试. 这让我无比的兴奋, 以后写Go语言不在是一种危害生命的行为.

[声明]
1> 这篇文章不仅试用于Windows系统, 也适用于Mac OS X系统. 但文章的重点在于Mac OS X系统的环境下部署.
2> Mac OS X的相关技术分享, 本人不会采取免费模式, 如果您在阅读本文时, 遇到困难并想获得近一步技术, 可联系我并支付一定费用来获取对应的技术支持.
备注: Windows用户为免费模式
3> 由于Go语言属于Google产品, 因此为了保证你能成功部署Go语言开发环境, 请自行准备好相关的"科学上网技术".

[开始搭建具备有调试Go语言能力的开发环境]
步骤1: 进入Go语言官方网站:https://golang.org/ 下载对应操作系统版本的安装包. 特别注意提醒: Mac OS X系统用户, 要注意配置好"GOPATH"这个环境变量, 不然你会无法成功部署.
步骤2: 进入https://www.visualstudio.com/zh-cn/products/code-vs.aspx网站下载Visual Studio Code
步骤3: 开始部署Visual Studio Code的Go语言扩展. 参考如下文章:
http://www.cnblogs.com/JerryNo1/p/5412864.html
备注: 该文章适用于Windows用户, Mac OS X用户(自己变通一下)

[重要的功能: 在Mac OS X系统让Visual Studio Code支持Go语言调试]
步骤1: 打开终端输入如下命令, 安装Go语言的调试工具"delve"
go get -v -u github.com/peterh/liner github.com/derekparker/delve/cmd/dlv
这个东西安装好之后, 会在你的GOPATH/bin目录下出现"dlv"执行文件, 在GOPATH/src目录出现dlv的源码文件目录:github.com/derekparker/delve
步骤2: 虽然安装好了Go语言调试工具"delve", 但由于该调试工具没有代码签名, 因此Visual Studio Code是无法激活此调试工具来调试Go语言. 因此我们需要通过如下重要的步骤来来解决这个问题:
如果你英文好可以看原文描述:https://github.com/derekparker/delve/wiki/Building
如果英文不好, 那就看我粗糙的翻译:
2.1> 打开"钥匙串访问"
2.2> 打开菜单 钥匙串访问/证书助理/创建证书...
2.3> 名称: dlv-cert 身份类型: 自签名证书 证书类型: 代码签名 并 选择"让我覆盖这些默认值"
2.4> 单击"继续", 有效期(天数): 365 这里你可以自己修改, 我改为3650
2.5> 一路继续下去, 直到看到"指定用于该证书的位置" 钥匙串 选择 "系统" 并单击"创建"按钮
2.6> 重启系统之后, 再打开"钥匙串访问", 选择"系统", 就会看到创建好的"dlv-cert"证书.
2.7> 右键"dlv-cert"证书, 选择"显示简介"->"信任"->"代码签名" 修改为: 始终信任
2.8> 打开终端然后cd命令进入之前你安装好的"GOPATH/src目录下的dlv源码文件目录:github.com/derekparker/delve"
2.9> 输入如下命令: GO15VENDOREXPERIMENT=1 CERT=dlv-cert make install 这样就可以重新编译出一个带有代码签名的dlv执行程序

通过以上的9个步骤, 就可以完成"在Mac OS X系统让Visual Studio Code支持Go语言调试"的环境部署.

