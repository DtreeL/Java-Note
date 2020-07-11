# 开关机和基本目录
```bash
sync   #将数据由内存同步到硬盘中

shutdown    #立即关机
    -h +10   #10分钟后关机
    -h now
    -h 20:25
    -r now  #立即重启
    -r +10  #10分钟后重启
    
reboot  #重启，等同于shutdown -r now

halt    #关闭系统，等同于shutdown -h now 和 poweroff
```
系统目录结构
1. 一切皆文件
2. 根目录/，所有的文件都挂载在这个节点下
3. 树形结构
```bash
/bin：bin是Binary的缩写, 存放着最经常使用的命令。

/boot： 存放的是启动Linux时使用的一些核心文件，包括一些连接文件以及镜像文件。

/dev ： dev是Device(设备)的缩写, 存放的是Linux的外部设备，在Linux中访问设备的方式和访问文件的方式是相同的。

* /etc： 用来存放所有的系统管理所需要的配置文件和子目录。

* /home：用户的主目录，在Linux中，每个用户都有一个自己的目录，一般该目录名是以用户的账号命名的。

/lib：存放着系统最基本的动态连接共享库，其作用类似于Windows里的DLL文件。

/lost+found：一般情况下是空的，当系统非法关机后，这里就存放了一些文件。

/media：linux系统会自动识别一些设备，例如U盘、光驱等等，当识别后，linux会把识别的设备挂载到这个目录下。

/mnt：系统提供该目录是为了让用户临时挂载别的文件系统的，我们可以将光驱挂载在/mnt/上，然后进入该目录就可以查看光驱里的内容了。

* /opt：这是给主机额外安装软件所摆放的目录。比如你安装一个ORACLE数据库则就可以放到这个目录下。默认是空的。

/proc：一个虚拟的目录，它是系统内存的映射，我们可以通过直接访问这个目录来获取系统信息。

* /root：该目录为系统管理员，也称作超级权限者的用户主目录。

/sbin：s就是SuperUser的意思，这里存放的是系统管理员使用的系统管理程序。

/srv：存放一些服务启动之后需要提取的数据。

/sys：这是linux2.6内核的一个很大的变化。该目录下安装了2.6内核中新出现的一个文件系统 sysfs 。

* /tmp：用来存放一些临时文件的。

* /usr：这是一个非常重要的目录，用户的很多应用程序和文件都放在这个目录下，类似于windows下的program files目录。

/usr/bin： 系统用户使用的应用程序。

/usr/sbin： 超级用户使用的比较高级的管理程序和系统守护程序。

/usr/src： 内核源代码默认的放置目录。

* /var：存放着在不断扩充着的东西，我们习惯将那些经常被修改的目录放在这个目录下。包括各种日志文件。

/run：是一个临时文件系统，存储系统启动以来的信息。当系统重启时，这个目录下的文件应该被删掉或清除。

* /www：存放服务器网站相关的资源，比如环境、网站项目等
```
# 常用的基本命令
目录管理
```bash
cd  #切换目录命令
    cd ..
    cd ~    #进入当前用户目录
    
ls  #列出目录
    -a  #查看所有文件包括隐藏
    -l  #列出所有文件(不包含隐藏)以及文件的属性和权限
    所有的linux命令可以组合使用比如 ls -al  #隐藏文件的属性和权限也一并列出
    
mkdir 目录名    #创建目录
    -p  #递归创建多层目录

pwd #查看当前用户所在目录

rmdir   #删除非空目录命令
    -p  #强制删除，亦可删层级目录
    
cp 原地方 新地方 #复制文件或者目录命令

rm  #删除文件或目录
    -f  #忽略不存在的文件，强制删除
    -r  #递归删除目录
    -i  #询问是否删除
    -rf /   #强制递归删除/目录下的所有文件，删除跑路
    
mv  #移动文件或目录
    如果是两个文件，就是从命名
    -f  #强制移动
    -u  #只替换已经更新过的文件
```
基础属性
```bash
第一个字符
    d   目录
    -   文件
    l   链接文档
    b   可随机存储装置
    c   串行端口设备，比如键盘、鼠标

接下来9个字符，3个为一组，都是rwx的组合。分别是属主权限(属于谁)、属组权限、其他用户权限
    r   可读    4
    w   可写    2
    x   可执行  1
rwx的位置不变，无此权限则是-字符



修改文件属性
    charp   更改文件属组
        -R 属组名 文件名
    
    chown   更改文件属主，也可以同时更改文件属组
        -R 属主名 文件名
        -R 属主名：属组名 文件名
    
    chmod   更改文件9个属性
        -R  xyz 文件或目录
```
文件内容查看
```bash
cat 由第一行开始显示文件内容
     -A ：相当於 -vET 的整合选项，可列出一些特殊字符而不是空白而已；

    -b ：列出行号，仅针对非空白行做行号显示，空白行不标行号！

    -E ：将结尾的断行字节 $ 显示出来；

    -n ：列印出行号，连同空白行也会有行号，与 -b 的选项不同；

    -T ：将 [tab] 按键以 ^I 显示出来；

    -v ：列出一些看不出来的特殊字符
    
tac 从最后一行开始显示，可以看出 tac 是 cat 的倒着写！

nl  显示的时候，顺道输出行号
    -b ：指定行号指定的方式，主要有两种：-b a ：表示不论是否为空行，也同样列出行号(类似 cat -n)；-b t ：如果有空行，空的那一行不要列出行号(默认值)；

    -n ：列出行号表示的方法，主要有三种：-n ln ：行号在荧幕的最左方显示；-n rn ：行号在自己栏位的最右方显示，且不加 0 ；-n rz ：行号在自己栏位的最右方显示，且加 0 ；

    -w ：行号栏位的占用的位数。
    
more 一页一页的显示文件内容
    空白键 (space)：代表向下翻一页；

    Enter     ：代表向下翻『一行』；

    /字串     ：代表在这个显示的内容当中，向下搜寻『字串』这个关键字；

    :f      ：立刻显示出档名以及目前显示的行数；

    q       ：代表立刻离开 more ，不再显示该文件内容。

    b 或 [ctrl]-b ：代表往回翻页，不过这动作只对文件有用，对管线无用。
    
less 与 more 类似，但是比 more 更好的是，他可以往前翻页！
    空白键  ：向下翻动一页；

    [pagedown]：向下翻动一页；

    [pageup] ：向上翻动一页；

    /字串   ：向下搜寻『字串』的功能；

    ?字串   ：向上搜寻『字串』的功能；

    n     ：重复前一个搜寻 (与 / 或 ? 有关！)

    N     ：反向的重复前一个搜寻 (与 / 或 ? 有关！)

    q     ：离开 less 这个程序；
    
head 只看头几行
     [-n number] 文件   默认的情况中，显示前面 10 行

tail 只看尾巴几行
    [-n number] 文件
```
Linux 链接概念

Linux 链接分两种，一种被称为硬链接（Hard Link），另一种被称为符号链接（Symbolic Link）。

情况下，ln 命令产生硬链接。

- 硬连接指通过索引节点来进行连接。保存在磁盘分区中的文件不管是什么类型都给它分配一个编号，称为索引节点号(Inode Index)。
    - 硬连接的作用是允许一个文件拥有多个有效路径名，这样用户就可以建立硬连接到重要文件，以防止“误删”的功能。
    - 复制一份，但是内容同步
- 符号连接（Symbolic Link），也叫软连接。
    - 软链接文件有类似于 Windows 的快捷方式
    - 在符号连接中，文件实际上是一个文本文件，其中包含的有另一文件的位置信息。
    - 快捷方式

# Vim编辑器
vim通过一些插件可以实现跟IDE一样的功能
![](https://note.youdao.com/yws/api/personal/file/62428501A4F04F64A18A39D5DB8E724E?method=download&shareKey=7b3bcea53f84649c69569cab76e1097f)
## 命令模式
用vim打开一个文件就是命令模式，此时输入的是命令
```bash
i   编辑模式
x   删除光标所在的行
：  切换底线命令模式
```
## 输入模式
```bash
字符按键以及Shift组合，输入字符

ENTER，回车键，换行

BACK SPACE，退格键，删除光标前一个字符

DEL，删除键，删除光标后一个字符

方向键，在文本中移动光标

HOME/END，移动光标到行首/行尾

Page Up/Page Down，上/下翻页

Insert，切换光标为输入/替换模式，光标将变成竖线/下划线

ESC，退出输入模式，切换到命令模式
```
## 底线命令模式
```bash
底线命令模式可以输入单个或多个字符的命令，可用的命令非常多。

在底线命令模式中，基本的命令有（已经省略了冒号）：

q 退出程序

w 保存文件

按ESC键可随时退出底线命令模式。
```
# 账号管理
Linux系统是一个多用户多任务的分时操作系统，任何一个要使用系统资源的用户，都必须首先向系统管理员申请一个账号，然后以这个账号的身份进入系统。
## 用户账号的管理
添加用户账号就是在系统中创建一个新账号，然后为新账号分配用户号、用户组、主目录和登录Shell等资源。
```bash
useradd 选项 用户名
    -c comment 指定一段注释性描述。

    -d 目录 指定用户主目录，如果此目录不存在，则同时使用-m选项，可以创建主目录。

    -g 用户组 指定用户所属的用户组。

    -G 用户组，用户组 指定用户所属的附加组。

    -m　使用者目录如不存在则自动建立。

    -s Shell文件 指定用户的登录Shell。

    -u 用户号 指定用户的用户号，如果同时有-o选项，则可以重复使用其他用户的标识号。

su username     #切换用户
    普通用户切换到root用户，还可以使用命令：sudo su
    在终端输入exit或logout或使用快捷方式ctrl+d，可以退回到原来用户
    在切换用户之后使用新用户的工作环境，可以在su和username之间加-
    
删除用户账号就是要将/etc/passwd等系统文件中的该用户记录删除，必要时还删除用户的主目录。
    userdel 选项 用户名
        -r，它的作用是把用户的主目录一起删除。
        
修改用户账号就是根据实际情况更改用户的有关属性，如用户号、主目录、用户组、登录Shell等。
    usermod 选项 用户名
        常用的选项包括-c, -d, -m, -g, -G, -s, -u以及-o等，这些选项的意义与useradd命令中的选项一样，可以为用户指定新的资源值。

用户管理的一项重要内容是用户口令的管理。用户账号刚创建时没有口令，但是被系统锁定，无法使用，必须为其指定口令后才可以使用，即使是指定空口令。
    passwd 选项 用户名
        -l 锁定口令，即禁用账号。

        -u 口令解锁。

        -d 使账号无口令。

        -f 强迫用户下次登录时修改口令。
```
## 用户组管理
每个用户都有一个用户组，系统可以对一个用户组中的所有用户进行集中管理。

Linux下的用户属于与它同名的用户组，这个用户组在创建用户时同时创建。

组的增加、删除和修改实际上就是对/etc/group文件的更新。
```bash
groupadd 选项 用户组
    -g GID 指定新用户组的组标识号（GID）。

    -o 一般与-g选项同时使用，表示新用户组的GID可以与系统已有用户组的GID相同。
    
groupdel 用户组

groupmod 选项 用户组
    -g GID 为用户组指定新的组标识号。

    -o 与-g选项同时使用，用户组的新GID可以与系统已有用户组的GID相同。

    -n新用户组 将用户组的名字改为新名字
    
一个用户同时属于多个用户组，那么用户可以在用户组之间切换，以便具有其他用户组的权限。
    newgrp
    
/etc/passwd文件是用户管理工作涉及的最重要的一个文件
    Linux系统中的每个用户都在/etc/passwd文件中有一个对应的记录行，它记录了这个用户的一些基本属性。这个文件对所有用户都是可读的。
    
    用户名:口令:用户标识号:组标识号:注释性描述:主目录:登录Shell
    
    用户登录后，要启动一个进程，负责将用户的操作传给内核，这个进程是用户登录到系统后运行的命令解释器或某个特定的程序，即Shell。
        用户的登录Shell也可以指定为某个特定的程序
        利用这一特点，我们可以限制用户只能运行指定的应用程序，
    
/etc/shadow中的记录行与/etc/passwd中的一一对应，它由pwconv命令根据/etc/passwd中的数据自动产生
    登录名:加密口令:最后一次修改时间:最小时间间隔:最大时间间隔:警告时间:不活动时间:失效时间:标志

/etc/group
    用户组的所有信息都存放在/etc/group文件中。
    组名:口令:组标识号:组内用户列表
```
# 磁盘管理
Linux磁盘管理常用命令为 df、du。

- df ：列出文件系统的整体磁盘使用量
    ```bash
    df [-ahikHTm] [目录或文件名]
        -a ：列出所有的文件系统，包括系统特有的 /proc 等文件系统；

        -k ：以 KBytes 的容量显示各文件系统；

        -m ：以 MBytes 的容量显示各文件系统；

        -h ：以人们较易阅读的 GBytes, MBytes, KBytes 等格式自行显示；

        -H ：以 M=1000K 取代 M=1024K 的进位方式；

        -T ：显示文件系统类型, 连同该 partition 的 filesystem 名称 (例如 ext3) 也列出；

        -i ：不用硬盘容量，而以 inode 的数量来显示
    ```
- du：检查磁盘空间使用量
    ```bash
    du [-ahskm] 文件或目录名称
        -a ：列出所有的文件与目录容量，因为默认仅统计目录底下的文件量而已。

        -h ：以人们较易读的容量格式 (G/M) 显示；

        -s ：列出总量而已，而不列出每个各别的目录占用容量；

        -S ：不包括子目录下的总计，与 -s 有点差别。

        -k ：以 KBytes 列出容量显示；

        -m ：以 MBytes 列出容量显示；
    ```
磁盘挂载与卸载
```bash
根文件系统之外的其他文件要想能够被访问，都必须通过“关联”至根文件系统上的某个目录来实现，此关联操作即为“挂载”，此目录即为“挂载点”,解除此关联关系的过程称之为“卸载”

将外部设备挂载来访问
    mount [-t 文件系统] [-L Label名] [-o 额外选项] [-n] 装置文件名 挂载点
    
    umount [-fn] 装置文件名或挂载点
        -f ：强制卸除！可用在类似网络文件系统 (NFS) 无法读取到的情况下；

        -n ：不升级 /etc/mtab 情况下卸除。
```
# 进程管理
每个程序都有自己的一个进程，每一个进程都有一个id号

每个进程都会有父进程

ps  查看当前系统中正在执行的各种进程的信息
```bash
    -a  显示运行的所有进程信息
    -u  以用户的信息显示进程
    -x  显示后台进行进程的参数

    -aux    查看所有进程

    |   管道符  A|B A的输出结果传给B命令

    grep    查找文件中符合条件的字符串

ps -aux|grep mysql  查看所有mysql的进程

    -ef 看父进程的信息

pstree  #进程树
    -p 显示父id
    -u 显示用户组

kill -9 进程的id
```
# 环境安装
安装软件
    - rpm(jdk,在线发布SpringBoot项目)
    - 解压缩(tomcat,启动并通过外网访问，发布网站)
    - yum在线安装(docker,直接安装运行跑起来)
## rpm安装JDK
1. 下载JDK的rpm包
2. 检查是否有java环境。java -version
    - 如果有的话需要卸载。rpm -qa|grep jdk    检测jdk版本信息
    - rpm -e --nodeps 查出来的信息
3. 安装。rpm -ivh rpm包名
4. 配置环境变量 vim /etc/profile
    ```bash
    JAVA_Home=/usr/java/jdk1.8.0_251-amd64
    CLASSPATH=%JAVA_HOME%/lib;%JAVA_HOME%/jre/lib
    PATH=$JAVA_HOME/bin;$JAVA_HOME/jre/bin
    export PATH CLASSPATH JAVA_HOME
    ```
5. 让新的配置生效source /etc/profile
6. 用maven的package命令打出jar包，上传到服务器
7. 确保Linux的防火墙端口是开启的，如果是阿里云，需要保证阿里云的安全组策略是开放的
    ```bash
    # 查看firewall服务状态
    systemctl status firewalld
    
    # 开启、重启、关闭、firewalld.service服务
    # 开启
    service firewalld start
    # 重启
    service firewalld restart
    # 关闭
    service firewalld stop

    # 查看防火墙规则
    firewall-cmd --list-all    # 查看全部信息
    firewall-cmd --list-ports  # 只看端口信息

    # 开启端口
    开端口命令：firewall-cmd --zone=public --add-port=80/tcp --permanent
    重启防火墙：systemctl restart firewalld.service

    命令含义：
    --zone #作用域
    --add-port=80/tcp  #添加端口，格式为：端口/通讯协议
    --permanent   #永久生效，没有此参数重启后失效
    ```
8. 前台执行 java -jar；后台执行nohup -jar

## 解压缩安装tomcat
ssm war包就需要放到tomcat中运行
1. 下载.tar.gz格式
2. 解压。tar -zxvf
3. bin目录下。
    - ./startup.sh
    - ./shutdown.sh
4. 公网ip，8080端口即可访问
5. 上传完毕的项目可以直接购买自己的域名，备案解析替换公网ip即可
6. 域名解析后，如果端口是80或者443可以直接访问，如果是9000,8080需要通过Apcahe或者Nginx做以下反向代理、配置文件即可

## yum安装Docker
yum install -y yum源

1. cat /etc/redhat-release查看CentOS版本，确定是7以上
2. yum安装gcc相关（需要确保 虚拟机可以上外网 ）
    - yum -y install gcc
    - yum -y install gcc-c++
3. 卸载旧版本
    ```bash
    yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
    ```
4. 安装需要的软件包yum install -y yum-utils device-mapper-persistent-data lvm2
5. 设置阿里云镜像yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
6. 更新yum软件包索引yum makecache fast
7. 安装Docker CE。yum -y install docker-ce docker-ce-cli containerd.io
8. 启动docker。systemctl start docker
9. 测试
    ```bash
    docker version

    docker run hello-world

    docker images
    ```
    
# Docker
环境配置很麻烦，换了机器就要重来一次，能不能从根本上解决问题，软件带着环境一起安装，直接把原始环境一模一样复制过来，利用Docker可以消除“在我的机器上可以正常运行”的问题。

把家原模原样的移动到另一个地方。这个家的整体就叫镜像images

虚拟机是一种带环境安装的一种解决方案，对于windows来说就是一个文件，直接复制到另一台电脑上，直接打开并没有任何区别只是移动了一个地方而已。
- 但是虚拟机太笨重了，linux开发了一种虚拟技术linux容器。Linux容器不是模拟一个完整的操作系统，把需要的资源打包到一个隔离的容器中，需要什么加载什么
- Docker某种程度讲是缩小版精细版的虚拟机

## Docker三要素
1. 仓库
2. 镜像
3. 容器

Docker Hub与Git Hub很像，git命令从仓库拉取，那么docker命令也可以从仓库拉取，拉取下来的软件环境就封装成一个镜像也就是鲸鱼上的集装箱

镜像就是类，容器就是对象。
- 容器是用镜像创建的运行实例，可以被启动、开始、停止、删除
- 可以把容器看错是一个简易版的Linux环境和运行在其中的应用程序
- 仓库是集中存放镜像的场所
- Docker是一个容器运行载体或者称为管理引擎
- 镜像拉下来变成容器，一个容器运行一种服务，客户端通过命令启动或者停止这项服务

Docker容器
- 与宿主机共享OS，而虚拟机在宿主OS上运行虚拟机的OS
- 镜像小，而虚拟机的镜像大
- 几乎无额外性能损失，而虚拟机的操作系统耗费额外CPU和内存
- 面向软件开发者，而虚拟机面向硬件运维者
- 部署快速，秒级，而虚拟机较慢

## docker命令
```bash
帮助命令
    docker version
            info
            --help  #linux的man命令可以知道一个指令的解释，而这个命令可以列出来docker命令以及解释

镜像命令
    docker images   #列出本地镜像
                -a  #列出本地所有镜像含中间映像层
                -q  #只显示镜像ID
                --digests   #显示镜像的摘要信息
                --no-trunc  #显示完整的镜像信息
            
    docker search tomcat   #从https://hub.docker.com中查出镜像的信息
                    --no-trunc  #显示完整镜像描述
                    -s  #列出收藏数不小于指定值的镜像
                    --automated #只列出automated bulid类型的镜像
    
    docker pull 镜像名:版本  #下载镜像，不指明版本下载最新版
    
    docker rmi 镜像名或id   #删除镜像
                    -f 镜像名或id
                    -f 镜像名:TAG 镜像名2：TAG
                    -f $(docker images -qa) #删除全部
    
容器命令
    docker run [OPTIONS] image [COMMAND] [ATG..] #新建并启动进入容器
                -i  #以交互模式运行容器，通常与-t同时使用
                -t  #为容器分配一个伪输入终端，登录容器后在容器中弹出来一个终端
                -p 8888::8080 docker对外暴露的端口对应docker里的端口
            
            ps  #列出所有正在运行的容器
            
            exit    #容器停止退出
            crtl+P+Q    #容器不停止退出
            
            start   #启动容器
            restart
            stop
            kill
            
            rm
            
    docker exec -t 容器id ls    #直接在宿主机返回容器里执行命令的结果
    
    docker cp id:/文件路径  宿主目标路径
```
## 镜像原理
docker的镜像实际上由一层层的文件系统组成，这种层级的文件系统是UnionFS(可以一次加载多个文件系统，从外面看只是一个文件系统，联合加载会把各层文件系统叠加起来)

bootfs
- bootloader
    - 引导加载kernel。Linux刚启动时加载bootfs文件系统，Docker镜像的最底层是bootfs
- kernel

rootfs：bootfs之上就是Linux系统中的/dev,/bin等标准目录和文件，rootfs就是各种不同操作系统发行版

分层镜像，如一个tomcat需要有jdk等待环境，所以会下载很多依赖的镜像组装成一个镜像

docker commit   如果在一个容器上做了修改，需要保存下来使用commit使之成为一个新的镜像，下次就可以不用原来的直接用自己上次修改的
- docker commit -m=:"" -a="作者" 容器id 镜像名:[标签名]

## 容器数据卷
容器的数据持久化，容器间共享数据

数据卷
- 容器内添加
    - 直接命令添加
        ```bash
        docker run -it -v /宿主机绝对路径目录:/容器内目录 镜像名
        数据互相共享和对接
        ```
    - DockerFile添加
        ```bash
        .java  --> .class
        image  --> dockerFile
        ```

数据卷容器
    - 容器间传递共享

## DockerFile
```bash
maven bulid --> 编写dockerFile，docker bulid
jar --> image
java -jar --> docker run

每条指令都会创建一个新的镜像层，并对镜像进行提交

FROM    基于哪个基础镜像

MAINTAINER  维护作者和邮箱

RUN 容器构建时需要运行的命令

EXPOSE  暴露镜像实例后的容器端口号

WORKDIR 指定在创建容器后，终端默认登录进来的工作目录

ENV 用来构建镜像过程中设置环境变量

ADD 将宿主机目录下的文件拷贝进镜像，会自动处理url加解压缩

COPY

VOLUME  容器数据卷

CMD 容器启动时要运行的命令，只有最后一个生效

ENTRYPOINT  CMD命令，不同的是追加命令

ONBUILD    当构建一个被继承的DockerFile时运行命令，父镜像在被子继承后父镜像的onbulid会被触发

docker build -t 新镜像名:TAG .

docker run -it 新镜像名:TAG
```

# Nginx
高性能http和反向代理的web服务器，可以作为静态资源的web服务器、也可以作为邮件代理服务器

```bash
usr/local/nginx
    - html  网页文件
        - index.html 欢迎页
        - 50x.html  错误页
    - conf  配置文件
        - nginx.conf
    - logs  日志文件
    - sbin 主要二进制程序
````

## 反向代理
正向代理：访问不了某网站，但是能访问一个代理服务器，这个代理服务器能访问那个不能访问的网站。连接上代理服务器，告诉它需要哪些内容，代理服务器去取然后返回回来

反向代理：客户端对代理是无感知的，因为客户的不需要配置就可以访问，只需要将请求发送到反向代理服务器，由反向代理服务器去选择目标服务器获取数据返回。
- 反向代理服务器和目标服务器看上去就是一个服务器，隐藏了真实服务器。
- 代理服务器接收连接请求，然后将请求转发给内部网络上的服务器，将得到的结果返回给客户端
```bash
server{
    listen  #nginx的端口
    server_name     #nginx的ip
    
    #location  [ =, ~, ~*, ^~] uri
        = 严格匹配
        ~ 正则区分大小写匹配
        ~* 正则不分大小写匹配
        ^~ 不含正则
    location /{     #所有请求都在这处理
        proxy_pass      #这样就一访问nginx的地址，就会转发到这个地址中，向它请求
    }
}
```
## 负载均衡
安排请求与服务器的分配
```bash
http{
    upstream 服务器容器名{  #要做负载均衡的服务器
        server ip地址；
        server ip地址;
    }
    
    server{
        location uri{
            proxy_pass  http://服务器容器名。说明请求会按照算法分发请求到该服务器容器里的服务器去，实现负载均衡，
            proxy_connection_timeout
        }
    }
}
```
## 动静分离

## 配置文件
/usr/local/nginx/conf/nginx.conf
```bash
1. 全局块（基本配置）
    影响nginx服务器全局运行的配置
    
    user nobody; #配置worker进程运行用户
    worker_processes 1; #配置工作进程数目，根据硬件调整，通常等于cpu数量或者CPU数量的两倍
    
    error_log logs/error.log;   #配置全局错误日志类型，默认是error
    
    pid     logs/nginx.pid; #配置进程pid文件

2. events块
    naginx服务器与用户的网络连接
    
    worker_connections 1024;    #配置每个worker进程连接数上限

3. http块（一个http块可以有多个server块，每个server块就相当于一个虚拟主机）
    - http全局块（文件引入、MIME-TYPE定义、日志自定义、连接超时时间、单链接请求数上限等）
    
    include 
    
    - server（和虚拟主机有密切关系）
        - 全局server块
            本虚拟机主机的监听配置和本虚拟主机的名称和ip配置
        
        - 多个location块
            nginx服务器接收到的请求字符串，对特定的请求进行处理
    
    
```