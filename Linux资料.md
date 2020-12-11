## Linux资料

> 不管是重启系统还是关闭系统，首先要运行 **sync** 命令，把内存中的数据写到磁盘中。

### 1、用户管理

```markdown
# 切换用户
su -  **切换到高级别用户，比如root**
exit  **退出当前用户登录**
su - was **切换到was用户**
# 用户管理
useradd was  **添加用户**
useradd -m 用户名
参数说明：
选项 :
    -c comment 指定一段注释性描述。
    -d 目录 指定用户主目录，如果此目录不存在，则同时使用-m选项，可以创建主目录。
    -g 用户组 指定用户所属的用户组。
    -G 用户组，用户组 指定用户所属的附加组。
    -m　使用者目录如不存在则自动建立。
    -s Shell文件 指定用户的登录Shell。
    -u 用户号 指定用户的用户号，如果同时有-o选项，则可以重复使用其他用户的标识号。

passwd 密码
userdel was  **删除用户**
	-r 作用是把用户的主目录一起删除。
# 查看文件所有者
ls -ahl
# 用户组管理
groupadd police  **增加用户组**
useradd -g police tom  **增加police组的用户Tom**
	常用的选项包括-c, -d, -m, -g, -G, -s, -u以及-o等，这些选项的意义与useradd命令中的选项一样
usermod -g police was  **改变用户的用户组**
# 修改文件所有者
**指令：chown 用户名 文件名**
案例：chown tom hello.txt
#修改文件所在组
**基本指令 chgrp 组名 文件名**
案例：chgrp police hello.txt
```

### 2、文件管理

```markdown
# 文件目录
mkdir [-mp] 目录名称
选项与参数：
-m ：配置文件的权限喔！直接配置，不需要看默认权限 (umask) 的脸色～
-p ：帮助你直接将所需要的目录(包含上一级目录)递归创建起来！
mkdir dog **创建文件夹**
mkdir -p /animal/dog **创建多级目录**  
rmdir **删除文件夹**
rm -rf 目录 **删除非空目录**
# 文件
touch aa.txt bb.txt **创建文件**
# > 和 >>
> 输出重定向 : 会将原来的文件的内容覆盖
>> 追加： 不会覆盖原来文件的内容，而是追加到文件的尾部。

1)ls -l > aa.txt （功能描述：列表的内容写入文件 aa.txt 中（覆盖写））
2)ls -l >>aa.txt （功能描述：列表的内容追加到文件 aa.txt 的末尾）
3)cat aa.txt > bb.txt （功能描述：将aa.txt的内容覆盖到bb.txt）
4)cat aa.txt >> bb.txt （功能描述：将aa.txt的内容追加到文件 bb.txt 的末尾）

# echo $PATH  输出环境变量
# zip指令
zip	[选项] XXX.zip	将要压缩的内容（功能描述：压缩文件和目录的命令）
unzip	[选项] XXX.zip （功能描述：解压缩文件）
zip 常用选项
	-r：递归压缩，即压缩目录 
unzip 的常用选项
	-d<目录> ：指定解压后文件的存放目录
# tar指令
tar 指令 是打包指令，最后打包后的文件是 .tar.gz 的文件。
•基本语法
	tar	[选项]	XXX.tar.gz	打包的内容	(功能描述：打包目录，压缩后的文件格式.tar.gz)
tar -zcvf home.tar.gz /home/
tar -zxvf home.tar.gz
# ls指令
ls [-aAdfFhilnrRSt] 目录名称
选项与参数：
-a ：全部的文件，连同隐藏文件( 开头为 . 的文件) 一起列出来(常用)
-l ：长数据串列出，包含文件的属性与权限等等数据；(常用)
# cp指令
语法：
[root@www ~]# cp [-adfilprsu] 来源档(source) 目标档(destination)
[root@www ~]# cp [options] source1 source2 source3 .... directory
选项与参数：
-a：相当於 -pdr 的意思，至於 pdr 请参考下列说明；(常用)
-p：连同文件的属性一起复制过去，而非使用默认属性(备份常用)；
-d：若来源档为连结档的属性(link file)，则复制连结档属性而非文件本身；
-r：递归持续复制，用於目录的复制行为；(常用)
-f：为强制(force)的意思，若目标文件已经存在且无法开启，则移除后再尝试一次；
-i：若目标档(destination)已经存在时，在覆盖时会先询问动作的进行(常用)
-l：进行硬式连结(hard link)的连结档创建，而非复制文件本身。
-s：复制成为符号连结档 (symbolic link)，亦即『捷径』文件；
-u：若 destination 比 source 旧才升级 destination ！
```

### 3、权限管理

>读(r=4) 写(w=2) 执行(x=1)    ---777

```markdown
# 权限介绍
ls	-l 中显示的内容如下：
-rwxrw-r-- 1 root root 1213 Feb 2 09:39 abc
0-9 位说明
1)第 0 位确定文件类型(d, - , l , c , b)
	当为[ d ]则是目录
    当为[ - ]则是文件；
    若是[ l ]则表示为链接文档 ( link file )；
    若是[ b ]则表示为装置文件里面的可供储存的接口设备 ( 可随机存取装置 )；
	若是[ c ]则表示为装置文件里面的串行端口设备，例如键盘、鼠标 ( 一次性读取装置 )。
2)第 1-3 位确定所有者（该文件的所有者）拥有该文件的权限。---User
3)第 4-6 位确定所属组（同用户组的）拥有该文件的权限，---Group
4)第 7-9 位确定其他用户拥有该文件的权限 ---Other
第一个字符代表文件类型： 文件 (-),目录(d),链接(l)
其余字符每 3 个一组(rwx) **读(r) 写(w) 执行(x) **
第一组 rwx : 文件拥有者的权限是读、写和执行
第二组 rw- :  与文件拥有者同一组的用户的权限是读、写但不能执行
第三组 r-- :	不与文件拥有者同组的其他用户的权限是读不能写和执行
可用数字表示为: r=4,w=2,x=1  因此 rwx=4+2+1=7

# rwx 作用到文件
1)	[ r ]代表可读(read):  可以读取,查看
2)	[ w ]代表可写(write): 可以修改,但是不代表可以删除该文件,删除一个文件的前提条件是对该文件所在的目录有写权限，才能删除该文件.
3)	[ x ]代表可执行(execute):可以被执行
# rwx 作用到目录
1)	[ r ]代表可读(read):  可以读取，ls 查看目录内容
2)	[ w ]代表可写(write):  可以修改,目录内创建+删除+重命名目录
3)	[ x ]代表可执行(execute):可以进入该目录
# 修改权限
**通过 chmod 指令，可以修改文件或者目录的权限**
第一种方式：+ 、-、=  变更权限
规则：u:所有者 g:所有组	o:其他人a:所有人(u、g、o 的总和)
1)	chmod	u=rwx,g=rx,o=x	文件目录名
2)	chmod	o+w	文件目录名
3)	chmod	a-x	文件目录名
案例：chmod u=rwx,g=rx,o=rx abc.txt
第二种方式：通过数字变更权限
规则：r=4 w=2 x=1		,rwx=4+2+1=7 
案例：chmod 755 abc.txt
**修改文件所有者-chown**
chown newowner	file	改变文件的所有者
	-R 如果是目录 则使其下所有子文件或目录递归生效
**修改文件所在组-chgrp**
chgrp newgroup file	改变文件的所有组
```

### 4、服务管理

```markdown
# CentOS6.5 防火墙：
service iptable status
servcie iptables stop --临时关闭防火墙
chkconfig iptables off --永久关闭防火墙
# CentOS 7.2 防火墙
CentOS 7.0默认使用的是firewall作为防火墙
苦逼code
CentOS7打开关闭防火墙与端口的基本命令
1、firewalld的基本使用
启动： systemctl start firewalld
关闭： systemctl stop firewalld
查看状态： systemctl status firewalld 
开机禁用  ： systemctl disable firewalld
开机启用  ： systemctl enable firewalld
# centos 7.0 相关命令
2.systemctl是CentOS7的服务管理工具中主要的工具，它融合之前service和chkconfig的功能于一体。

启动一个服务：systemctl start firewalld.service
关闭一个服务：systemctl stop firewalld.service
重启一个服务：systemctl restart firewalld.service
显示一个服务的状态：systemctl status firewalld.service
在开机时启用一个服务：systemctl enable firewalld.service
在开机时禁用一个服务：systemctl disable firewalld.service
查看服务是否开机启动：systemctl is-enabled firewalld.service
查看已启动的服务列表：systemctl list-unit-files|grep enabled
查看启动失败的服务列表：systemctl --failed
3.配置firewalld-cmd
查看版本： firewall-cmd --version
查看帮助： firewall-cmd --help
显示状态： firewall-cmd --state
查看所有打开的端口： firewall-cmd --zone=public --list-ports
更新防火墙规则： firewall-cmd --reload
查看区域信息:  firewall-cmd --get-active-zones
查看指定接口所属区域： firewall-cmd --get-zone-of-interface=eth0
拒绝所有包：firewall-cmd --panic-on
取消拒绝状态： firewall-cmd --panic-off
查看是否拒绝： firewall-cmd --query-panic

查看已经开放的端口：
	firewall-cmd --list-ports
开启端口
	firewall-cmd --zone=public --add-port=80/tcp --permanent
命令含义：
    –zone #作用域
    –add-port=80/tcp #添加端口，格式为：端口/通讯协议
    –permanent #永久生效，没有此参数重启后失效
firewall-cmd --reload #重启firewall
systemctl stop firewalld.service #停止firewall
systemctl disable firewalld.service #禁止firewall开机启动
firewall-cmd --state #查看默认防火墙状态（关闭后显示notrunning，开启后显示running）
那怎么开启一个端口呢
添加 firewall-cmd --zone=public --add-port=80/tcp --permanent（--permanent永久生效，没有此参数重启后失效）
重新载入 firewall-cmd --reload
查看 firewall-cmd --zone= public --query-port=80/tcp
删除 firewall-cmd --zone= public --remove-port=80/tcp --permanent
```

### 5、网络管理

```markdown
# centos7默认没有 netstat 命令，需要安装 net-tools 工具，yum install -y net-tools
**查看监听的端口**
netstat -lnpt
**检查端口被哪个进程占用**
netstat -lnpt |grep 5672
**查看系统所有的网络服务**
netstat -anp 
**正在开放的TCP端口**
netstat -at
**重启网络命令**
	service network restart
source /etc/profile
# 开通8080端口

firewall-cmd --zone=public --add-port=8080/tcp --permanent
进行8080端口开放配置；
命令解析：
--zone #作用域
--add-port=8080/tcp #添加端口，格式为：端口/通讯协议
--permanent #永久生效，没有此参数设置，重启后会失效。
firewall-cmd --reload
```

### 6、RPM、YUM管理

```markdown
# rpm
rpm -qa :查询所安装的所有 rpm 软件包
	rpm -qa|grep jdk
rpm -qi  软件包名 ：查询软件包信息
rpm -ql  软件包名 :查询软件包中的文件
rpm -qf 文件全路径名 查询文件所属的软件包
卸载 rpm 包:rpm -e firefox
安装 rpm 包：rpm -ivh	RPM 包全路径名称
```

### 7、linux自启动

```markdown
# 查看服务开机启动状态  查看防火墙
systemctl list-unit-files|grep firewall
# 开启或关闭
systemctl disable firewalld
systemctl enable firewalld

# chkconfig指令  --用于检查，设置系统的各种服务。
```

> 8080端口如何开通

```shell
#查看防火墙
firewall-cmd --state
#开启防火墙
systemctl start firewalld.service
# 开通8080端口
firewall-cmd --zone=public --add-port=8080/tcp --permanent
#重启防火墙
systemctl restart firewalld.service
#重新加载配置
firewall-cmd --reload
#端口号是否开启
firewall-cmd --query-port=8080/tcp
#查看端口号
netstat -lnpt
```

### 8、文件目录管理

```markdown
# 以下是对这些目录的解释：

/bin：bin是Binary的缩写, 这个目录存放着最经常使用的命令。

/boot： 这里存放的是启动Linux时使用的一些核心文件，包括一些连接文件以及镜像文件。

/dev ： dev是Device(设备)的缩写, 存放的是Linux的外部设备，在Linux中访问设备的方式和访问文件的方式是相同的。

/etc： 这个目录用来存放所有的系统管理所需要的配置文件和子目录。

/home：用户的主目录，在Linux中，每个用户都有一个自己的目录，一般该目录名是以用户的账号命名的。

/lib：这个目录里存放着系统最基本的动态连接共享库，其作用类似于Windows里的DLL文件。

/lost+found：这个目录一般情况下是空的，当系统非法关机后，这里就存放了一些文件。

/media：linux系统会自动识别一些设备，例如U盘、光驱等等，当识别后，linux会把识别的设备挂载到这个目录下。

/mnt：系统提供该目录是为了让用户临时挂载别的文件系统的，我们可以将光驱挂载在/mnt/上，然后进入该目录就可以查看光驱里的内容了。

/opt：这是给主机额外安装软件所摆放的目录。比如你安装一个ORACLE数据库则就可以放到这个目录下。默认是空的。

/proc：这个目录是一个虚拟的目录，它是系统内存的映射，我们可以通过直接访问这个目录来获取系统信息。

/root：该目录为系统管理员，也称作超级权限者的用户主目录。

/sbin：s就是Super User的意思，这里存放的是系统管理员使用的系统管理程序。

/srv：该目录存放一些服务启动之后需要提取的数据。

/sys：这是linux2.6内核的一个很大的变化。该目录下安装了2.6内核中新出现的一个文件系统 sysfs 。

/tmp：这个目录是用来存放一些临时文件的。

/usr：这是一个非常重要的目录，用户的很多应用程序和文件都放在这个目录下，类似于windows下的program files目录。

/usr/bin： 系统用户使用的应用程序。

/usr/sbin： 超级用户使用的比较高级的管理程序和系统守护程序。

/usr/src： 内核源代码默认的放置目录。

/var：这个目录中存放着在不断扩充着的东西，我们习惯将那些经常被修改的目录放在这个目录下。包括各种日志文件。

/run：是一个临时文件系统，存储系统启动以来的信息。当系统重启时，这个目录下的文件应该被删掉或清除。
```

### 9、软连接 和硬链接

Linux 链接分两种，一种被称为硬链接（Hard Link），另一种被称为符号链接（Symbolic Link）。

**ln** 命令产生硬链接。

```markdown
硬连接:硬连接的作用是允许一个文件拥有多个有效路径名，这样用户就可以建立硬连接到重要文件，以防止“误删”的功能。
软连接:类似于 Windows 的快捷方式,删除源文件，快捷方式也访问不了

touch f1     # 创建一个测试文件f1
ln f1 f2     # 创建f1的一个硬连接文件f2
ln -s f1 f3  # 创建f1的一个符号连接文件f3
ls -li       # -i参数显示文件的inode节点信息
# echo 字符串输出 >> f1 输出到 f1文件
echo "I am f1 file" >>f1

# 删除符号连接f3,对f1,f2无影响；
# 删除硬连接f2，对f1,f3也无影响；
# 删除原文件f1，对硬连接f2没有影响，导致符号连接f3失效；
# 同时删除原文件f1,硬连接f2，整个文件会真正的被删除。
```

### 10、Vim

> 三种模式

基本上 vi/vim 共分为三种模式，分别是**命令模式（Command mode）**，**输入模式（Insert mode）**和**底线命令模式（Last line mode）**。这三种模式的作用分别是：

**命令模式：**

用户刚刚启动 vi/vim，便进入了命令模式。

此状态下敲击键盘动作会被Vim识别为命令，而非输入字符。比如我们此时按下i，并不会输入一个字符，i被当作了一个命令。

以下是常用的几个命令：

- **i** 切换到输入模式，以输入字符。
- **x** 删除当前光标所在处的字符。
- **:** 切换到底线命令模式，以在最底一行输入命令。

若想要编辑文本：启动Vim，进入了命令模式，按下i，切换到输入模式。

命令模式只有一些最基本的命令，因此仍要依靠底线命令模式输入更多命令。

**输入模式：**

在命令模式下按下i就进入了输入模式。

在输入模式中，可以使用以下按键：

- **字符按键以及Shift组合**，输入字符
- **ENTER**，回车键，换行
- **BACK SPACE**，退格键，删除光标前一个字符
- **DEL**，删除键，删除光标后一个字符
- **方向键**，在文本中移动光标
- **HOME**/**END**，移动光标到行首/行尾
- **Page Up**/**Page Down**，上/下翻页
- **Insert**，切换光标为输入/替换模式，光标将变成竖线/下划线
- **ESC**，退出输入模式，切换到命令模式

**底线命令模式**

在命令模式下按下:（英文冒号）就进入了底线命令模式。

底线命令模式可以输入单个或多个字符的命令，可用的命令非常多。

在底线命令模式中，基本的命令有（已经省略了冒号）：

- q 退出程序
- w 保存文件

按ESC键可随时退出底线命令模式。

### 11、磁盘管理

>Linux磁盘管理常用命令为 df、du。df ：列出文件系统的整体磁盘使用量      du：检查磁盘空间使用量

```markdown
# df指令
语法：
df [-ahikHTm] [目录或文件名]
选项与参数：
    -a ：列出所有的文件系统，包括系统特有的 /proc 等文件系统；
    -k ：以 KBytes 的容量显示各文件系统；
    -m ：以 MBytes 的容量显示各文件系统；
    -h ：以人们较易阅读的 GBytes, MBytes, KBytes 等格式自行显示；
    -H ：以 M=1000K 取代 M=1024K 的进位方式；
    -T ：显示文件系统类型, 连同该 partition 的 filesystem 名称 (例如 ext3) 也列出；
    -i ：不用硬盘容量，而以 inode 的数量来显示
# du指令
语法：
du [-ahskm] 文件或目录名称
选项与参数：
    -a ：列出所有的文件与目录容量，因为默认仅统计目录底下的文件量而已。
    -h ：以人们较易读的容量格式 (G/M) 显示；
    -s ：列出总量而已，而不列出每个各别的目录占用容量；
    -S ：不包括子目录下的总计，与 -s 有点差别。
    -k ：以 KBytes 列出容量显示；
    -m ：以 MBytes 列出容量显示；
```

> 磁盘挂载与卸除

根文件系统之外的其他文件要想能够被访问，都必须通过“关联”至根文件系统上的某个目录来实现，此关联操作即为“挂载”，此目录即为“挂载点”,解除此关联关系的过程称之为“卸载”

Linux 的磁盘挂载使用mount命令，卸载使用umount命令。

磁盘挂载语法：

```
mount [-t 文件系统] [-L Label名] [-o 额外选项] [-n] 装置文件名 挂载点
```

磁盘卸载命令 umount 语法：

```
umount [-fn] 装置文件名或挂载点
```

选项与参数：

- -f ：强制卸除！可用在类似网络文件系统 (NFS) 无法读取到的情况下；
- -n ：不升级 /etc/mtab 情况下卸除。

### 12、进程管理

> 命令

**ps**查看当前系统中正在执行的各种进程信息

ps -xx:

	+ -a 显示当前终端运行的所有进程信息
	+ -u 用户的信息显示进程
	+ -x 显示后台运行进程的参数

```shell
# ps -aux 查看所有进程
ps -aux|grep mysql

# | 管道符
# grep 查看文件中符合条件的字符串 
```

**ps -ef|grep mysql 可以查看父进程的信息**

```bash
ps -ef |grep mysql  # 看父进程可以使用目录树查看

pstree -pu
	-p 显示父id
	-u 显示用户组
```



## dokcer资料

### 1、基本资料

> docker基本组成

**镜像（image）：**

docker镜像就好比是一个目标，可以通过这个目标来创建容器服务，tomcat镜像==>run==>容器（提供服务器），通过这个镜像可以创建多个容器（最终服务运行或者项目运行就是在容器中的）。

**容器（container）:**

Docker利用容器技术，独立运行一个或者一组应用，通过镜像来创建的.

启动，停止，删除，基本命令

目前就可以把这个容器理解为就是一个简易的 Linux系统。

**仓库（repository）:**

仓库就是存放镜像的地方！

仓库分为公有仓库和私有仓库。(很类似git)

Docker Hub是国外的。

阿里云…都有容器服务器 (配置镜像加速!)

### 2、安装Docker

> 安装

```shell
#1.卸载旧版本
 yum remove docker \
>                   docker-client \
>                   docker-client-latest \
>                   docker-common \
>                   docker-latest \
>                   docker-latest-logrotate \
>                   docker-logrotate \
>                   docker-engine

#2.需要的安装包
yum install -y yum-utils

#3.设置镜像的仓库
#推荐使用国内的
yum-config-manager \
    --add-repo \
    https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
  
#更新软件包索引
yum makecache fast

#4.安装docker docker-ce 社区版 而ee是企业版
yum install docker-ce docker-ce-cli containerd.io # 这里我们使用社区版即可

#5.启动docker
systemctl start docker

#6.使用docker version 查看是否安装成功
docker version

#7.测试
docker run hello-world

#8.查看一下下载的hello-world镜像
[root@localhost /]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
hello-world         latest              bf756fb1ae65        5 months ago        13.3kB

```

> 配置镜像加速

```shell
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://vvqp2don.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```

> 卸载

```shell
#1.卸载依赖
yum remove docker-ce docker-ce-cli containerd.io

#2. 删除资源
rm -rf /var/lib/docker
# /var/lib/docker 是docker的默认工作路径！
```

> docker出现的问题及解决方式

```markdown
# curl: (56) Recv failure: Connection reset by peer
# 原因：docker网卡地址冲突
# 解决方法：
brctl命令需要安装： yum install -y bridge-utils
- 停止docker
	systemctl stop docker
- docker0
	ip link set dev docker0 down
- 删除docker0网桥
	brctl delbr docker0
- 增加docker0 网桥
	brctl addbr docker0
- 增加网卡
	ip addr add 172.16.10.1/24 dev docker0
- 启用网卡
	ip link set dev docker0 up
- 重启docker服务
	systemctl restart docker
```

### 3、Docker常见命令

#### 帮助命令

```shell
docker version     # 显示docker的版本信息
docker info        # 显示docker的系统信息，包括镜像和容器的数量
docker 命令 --help  # 帮助命令
```

#### 镜像命令

##### docker images

```shell
[root@localhost /]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
hello-world         latest              bf756fb1ae65        5 months ago        13.3kB

#解释
REPOSITORY  镜像的仓库源
TAG         镜像标签
IMAGE ID    镜像id
CREATED     镜像的创建时间
SIZE        镜像的大小

#可选项
Options:
  -a, --all             # 列出所有镜像
  -q, --quiet           # 只显示镜像id
  
# docker images -qa
```

##### docker search

```shell
docker search mysql
#可选项，通过收藏来过滤
--filter=STARS=3000  #搜索出来的镜像就是STARS大于3000的
```

##### docker pull

```shell
# 下载镜像 docker pull 镜像名[:tag]
不指定版本就是下载最新的
# 指定版本下载
[root@localhost /]# docker pull mysql:5.7
```

##### docker rmi

```shell
[root@localhost /]# docker rmi -f 镜像id  				 #删除指定镜像
[root@localhost /]# docker rmi -f 镜像id 镜像id 镜像id  	   #删除多个镜像
[root@localhost /]# docker rmi -f $(docker images -aq)     #删除全部镜像
```

#### 容器命令

##### docker run 

```shell
# 有了镜像才可以创建容器，linux,下载一个centos镜像来学习
docker pull centos
# 新建容器并启动
docker run [可选参数] image

# 参数说明
--name="Name"	容器名字 tomcat01 tomcat02 ，用来区分容器
-d              后台方式运行
-it             使用交互方式运行，进入容器查看内容
-p              指定容器的端口 -p 8080:80
	-p  ip:主机(即宿主机)端口：容器端口
	-p  主机端口：容器端口  #这种方式常用
	-p  容器端口
	容器端口P
-P              随机指定端口(大写P)

# 测试，启动并进入容器
[root@localhost /]# docker run -it centos /bin/bash

-it 两个参数的作用是，为docker创建一个伪终端
/bin/bash的作用表示载入容器后运行bash，docker中必须保持一个进程的运行，要不然整个容器启动后马上kill itself
这个/bin/bash表示启动容器后启动bash
```

##### docker ps

```shell
# docker ps 命令
(不加） # 列出当前正在运行的容器
-a     # 列出当前正在运行的容器 + 带出历史运行过的容器
-n=?   # 显示最近创建的容器
-q    # 只显示当前容器的编号
```

##### 退出容器

```shell
exit   # 直接退出容器
Ctrl + P + Q  # 容器不停止退出
```

##### docker rm

```sehll
docker rm 容器id				   # 删除指定容器，不能删除正在运行的容器，如果要强制删除 rm -f
docker rm -f $(docker ps -aq)    # 删除所有容器 
```

##### 启动和停止容器的操作

```shell
docker start 容器id     # 启动容器
docker restart 容器id   # 重启容器
docker stop 容器id      # 停止当前正在运行的容器
docker kill 容器id      # 强制停止当前正在运行的容器
```

##### docker inspect

```shell
# 命令
docker inspect 容器id

# 测试
[root@localhost /]# docker inspect ce989f90023d
```

##### 后台运行容器

```shell
# 命令 docker run -d 镜像名
```

##### 进入当前正在运行的容器

```shell
# 命令
docker exec -it 容器id bashShell

# 测试
[root@localhost /]# docker exec -it ce989f90023d /bin/bash

# 方式二
docker attach 容器id
# 测试
[root@localhost /]# docker attach ce989f90023d

# docker exec		# 进入容器后开启一个新的终端，可以在里面操作（常用）
# docker attach 	# 进入容器正在执行的终端，不会启动新的进程
```

##### docker cp

```shell
docker cp 容器id:容器内目标文件路径  目的主机路径
# 将docker内文件拷贝到主机上
[root@localhost home]# docker cp ce989f90023d:/home/test.java /home
# 拷贝是一个手动过程，未来我们使用 -v 卷的技术，可以实现自动同步 
```

### 4、容器数据卷

> 什么是数据卷

容器之间可以有一个数据共享的技术！Docker容器中产生的数据，同步到本地！

这就是卷技术！目录的挂载，将我们容器内的目录，挂载到Linux上面！

总结一句话：容器的持久化和同步操作！容器间也是可以数据共享的！

#### 使用数据卷

> 方式一：直接使用命令来挂载

```shell
docker run -it -v 主机目录:容器目录

# 测试
[root@localhost home]# docker run -it -v /home/ceshi:/home  centos  /bin/bash

# 启动起来的时候，我们可以通过docker inspect 容器id 来查看挂载情况：

"Mounts": [
    {
    "Type": "bind",
    "Source": "/home/test",   # 主机内路径
    "Destination": "/home/ceshi",  # docker容器内地址
    "Mode": "",
    "RW": true,
    "Propagation": "rprivate"
    }
],
```

##### 实战练习：MySQL

```SHELL
# 获取镜像
[root@localhost home]# docker pull mysql:5.7

# 运行容器，需要做数据挂载！ # 安装mysql,需要配置密码，这是要注意的点！
# 官方测试：docker run --name some-mysql -e MYSQL_ROOT_PASSWORD=my-secret-pw -d mysql:tag

# 启动我们的MySQL容器
-d	后台运行
-p	端口映射
-v	卷挂载
-e  环境配置
--name  容器名字
[root@localhost home]# docker run -d -p 3310:3306 -v /home/mysql/conf:/etc/mysql/conf.d -v /home/mysql/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 --name mysql01 mysql:5.7

# 启动成功之后，我们在本地使用sqlyog 连接测试一下
# sqlyog —— 连接到服务器的3310 —— 3310和容器内的3306映射，这个时候我们就可以连接上了！

# 本地测试创建一个数据库，查看一下我们的映射的路径是否ok!

# grant all privileges on *.* to root@'%' identified by "123456";
# flush privileges;
```

##### 具名和匿名

```shell
# 匿名挂载
-v 容器内路径
docker run -d -P --name nginx01 -v /etc/nginx nginx


# 查看所有卷的情况
[root@localhost data]# docker volume ls
DRIVER              VOLUME NAME
local               2dd0379216c9ee4441ed56f8ce53461c19abe78b8cfd024ac5fbe07c3b8f09ba

# 这里发现，这种就是匿名挂载，我们在 -v 后只写了容器内的路径，没有写容器外的路径！

# 具名挂载
[root@localhost home]# docker run -d -P --name nginx02 -v juming-nginx:/etc/nginx nginx
5ba5708389bf71b2156fdbcedc50a62b16ac27adb2a3dfac42c52e9da5ace79f
[root@localhost home]# docker volume ls
DRIVER              VOLUME NAME
local               juming-nginx

# 通过 -v 卷名：容器内路径
# 查看一下这个卷  # 先找到卷所在路径 docker volume inspect 卷名

# 如何确定是具名挂载还是匿名挂载，还是指定路径挂载！
-v	容器内路径		       # 匿名挂载
-v	卷名:容器内路径	 	 # 具名挂载
-v	/宿主机路径:容器内路径   # 指定路径挂载！
```

### 5、DockerFile







