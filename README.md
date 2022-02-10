# 树莓派4B安装Ubuntu20.04

## 写在前面

本文主要介绍树莓派的安装流程和一些好用的软件如何安装配置，如一些好用的软件等，比较适合新手操作，讲的自认为很细。如果有错误欢迎指正。

### 1.使用前的硬件环境

windows电脑一台（暂时没用过mac），并且已经安装好以下软件：

Xshell6（用于SSH以及传输文件，如果有其他的SSH软件也可以）

Diskgenius（用于格式化之前刷过系统的SD卡）

BalenaEtcher(用于把系统刷入SD卡中，**运行的时候务必使用管理员身份运行**)

树莓派4b一个：内存4G，32G的fat32格式SD存储卡一张，读卡器一个

网络环境：

Windows这台电脑和树莓派通过有线连接到一个路由器，在192.168.31.X网段下，树莓派支持2.4G/5G双频段无线连接，目前还未尝试过使用无线网络进行初始化，具体流程等待后续更新

[以上软件可以通过Google或者是baidu软件的官网进行下载，如有能力请尽量支持正版软件]

### 2.初始化SD卡

其实不用初始化也可以刷入系统，刷入系统的BalenaEtcher可以把你的SD卡初始化,但是为了保险起见，建议有这一步。
如果本来你的SD就是一张空卡，那么可以跳过初始化SD卡的步骤

如果你的SD卡并不是一张空卡，或者说里面有一些自己的资料，请务必转移到其他介质，然后进行如下初始化操作。如果你的SD卡是一个空卡，那么可以忽略本节操作。

 打开diskgenius（我的版本5.3.0，各个版本差距应该不大），在左侧找到自己的sd卡（我的是32GB），右键-删除所有分区-是。

然后点击上方的新建分区-确定。

左上角点击保存更改-是，之后会提醒是否需要格式化，点击是即可，之后你的Windows会自动识别这个新创建的u盘，也就是我们的SD卡，成功的还原为空卡了

![](Picture\1.png)

![avator](Picture\2.png)

### 3.刷入系统

系统的刷入我这里选择的软件在前言中说过，这个软件记得一定要用右键-管理员权限打开，否则一定会安装失败。最左边选择我们的镜像：ubuntu-20.04.1-preinstalled-server-arm64+raspi.img.xz

![](Picture\3.png)

点击flash开始烧录。这个软件有三个状态，解压-写入-验证，正常状况使用Windows电脑验证可能不会成功，所以只要写入没有问题，验证阶段可以点击skip跳过，整个时间大约在五分钟左右。

## 第一次启动

刷入好之后就可以把这张sd卡插入树莓派中开机了，开机的时候绿灯会闪，表明正在读取sd卡中的内容，如果绿灯没有读取，建议等待一分钟
,如果绿灯显示常亮，说明没有读取到SD卡，建议关闭树莓派电源等待十秒钟后重新开启电源。`Ubuntu server 20.04 LTS arm` 版本的开机第一次默认账号和密码都是ubuntu，并且默认是开启SSH服务端的，可以直接通过SSH连接管理，无需对sd卡内容进行调整。

### 1. SSH链接教程

首先要知道树莓派的ip地址，开机之后等待一分三十秒之后可以通过各种方法查看树莓派的ip，可以通过内网扫描软件，也可以直接打开路由器的管理界面查看当前的所有设备，树莓派设备会显示ubuntu，我这里通过登陆小米路由器管家观察到树莓派IP地址为192.168.31.237（如果路由器有绑定功能建议绑定）。
打开xshell软件-新建会话，名称为自己喜欢的名称，主机填上树莓派的IP地址，端口号为22，点击链接，如果ip地址正确应该会提醒你是否接收秘钥，这里点击接收并保存秘钥。
![](Picture\4.png)

然后输入用户名为：ubuntu，点击记住用户名。
然后输入默认的密码：ubuntu，这里不用点击记住密码。然后点击确定即可。
登录成功后会提醒你修改密码，所以现在记住临时密码还没有用。

![](Picture\图片5.png)

先输入一遍默认的密码：ubuntu，然后他会提醒你输入两次新密码，之后客户端会断开连接，此时只需要点击左上角文件-重新连接即可。重新连接的时候会让你输入刚才设置的新密码，输入完毕后点击记住当前密码即可登录树莓派。

![](Picture\图片6.png)

![](Picture\图片7.png)

### 2.软件镜像设置

成功登录之后，最好不要进行其他操作，先更新镜像源为阿里云镜像，个人感觉比清华的镜像源更快，接下来的操作请注意和普通ubuntu不一样。
因为我们需要的是arm架构的镜像源而不是amd64的镜像源

Windows上登录阿里云镜像网站：
`https://developer.aliyun.com/mirror/`

点击第一个ubuntu，下拉找到ubuntu 20.04(folcal)配置，复制全部内容后回到我们的xshell输入：
`cd /etc/apt/`【进入apt文件夹】

`sudo cp sources.list sources.list.bk`
【备份源的文件为sources.list.bk】
这里的备份一份十分重要，要养成良好的备份习惯，如果下面的修改源的步骤出错还有机会重新修改，把系统捣鼓坏了可又要从头开始重新烧录，十分麻烦。

`sudo vim sources.list`
【编辑源文件】
此时刚打开的时候文件内容如下所示，现在我们开始修改镜像源,此时按住d键不放，直到内容全部删除，
接下来按i键，进入插入模式（左下角出现insert）后再复制粘贴，否则会丢失一部分文档。复制完成后在每一句的ubuntu之后加上“-ports”(不包括双引号)，否则无法安装软件。也就是你应该把配置文件改成如下：

`deb http://mirrors.aliyun.com/ubuntu-ports/ focal main restricted universe multiverse`

`deb-src http://mirrors.aliyun.com/ubuntu-ports/ focal main restricted universe multiverse`

`deb http://mirrors.aliyun.com/ubuntu-ports/ focal-security main restricted universe multiverse`

`deb-src http://mirrors.aliyun.com/ubuntu-ports/ focal-security main restricted universe multiverse`

`deb http://mirrors.aliyun.com/ubuntu-ports/ focal-updates main restricted universe multiverse`

`deb-src http://mirrors.aliyun.com/ubuntu-ports/ focal-updates main restricted universe multiverse`

`deb http://mirrors.aliyun.com/ubuntu-ports/ focal-proposed main restricted universe multiverse`

`deb-src http://mirrors.aliyun.com/ubuntu-ports/ focal-proposed main restricted universe multiverse`

`deb http://mirrors.aliyun.com/ubuntu-ports/ focal-backports main restricted universe multiverse`

`deb-src http://mirrors.aliyun.com/ubuntu-ports/ focal-backports main restricted universe multiverse`

![](Picture\图片8.png)

这里说一下vim的粘贴方法，按shift+冒号进入底行模式，输入set paste然后回车，这时候按i会显示insert（paste）进入专门的粘贴模式，
能够防止格式出错。如果不想用这种方法，并且右键粘贴没出错的话，那么就当我没说过。~~个人来说比较喜欢用vim，那些nano啥的感觉有点奇怪，vi感觉没有vim完美~~。
(Nano简单易懂，YYDS)

输入完毕后，保存退出即可。

保存退出的方法：按esc键退出操作模式，按住shift加上冒号键进入底行模式，输入wq后按回车保存退出。

此时输入sudo apt update 和 sudo apt upgrade 就可以更新软件了，建议使用之前更新一下（不是建议，99%的人都会这么干的）。
如果出现问是否继续，输入Y就好。Update的时间相比upgrade的时间要短很多，根据网速，upgrade的时间可能会在十五分钟左右，这个时间可以好好的摸摸鱼，玩玩手机。

###3.相关问题的出现和解决（可以提交issue）

![](Picture\图片9.png)

这是由于正有其他进程在使用`sudo apt update`这串代码，接下来教你如何操作

####方法一、关闭重启树莓派（简单易用）
`sudo halt`关机，然后重新接通电源开机，SSH连接上后用`sudo apt update && sudo apt upgrade`进行更新。
####方法二、温柔一点的方法
`ps -A | grep apt`找到相关的进程PID，然后使用`sudo kill -9 PID`（这里的pid对应前面找到的进程号）即可，如果无法解决建议重启。

###4.安全问题不能忘

上面修改过了Ubuntu用户的密码，别忘记设置root用户的密码！

`sudo su`进入超级用户模式

`passwd`修改当前用户密码

到现在为止，基本的软件配置已经完成了，下面为你介绍一些好用的软件配置以及一些能够提高可玩度的应用。

##相关软件安装（我可能用不到，但是我必须要安装）

一句话代码安装所有可能需要的软件：
`sudo apt update && sudo apt upgrade && sudo apt install -y gcc g++ python3-pip 
openjdk-11-jdk ffmpeg cmatrix sl hollywood samba samba-common tmux htop s-tui 
python3-dev jq proxychains zsh iperf3 unzip curl screenfetch git cmake clang && sudo 
apt autoremove`

更新日期：2022年2月10日20:23:58
