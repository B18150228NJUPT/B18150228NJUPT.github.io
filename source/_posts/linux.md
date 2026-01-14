---
title: linux
date: 2026-01-14 10:20:38
tags: 
  - linux
categories:
  - linux
---

# linux
[(九)JVM成神路之性能调优、GC调试、各内存区、Linux参数大全及实用小技巧 - 掘金](https://juejin.cn/post/7082698950838321160)

## 进程
<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2024/png/32403817/1709819181383-1e8dd712-80db-495e-8d90-a273191ff093.png)

可重入内核

1. 局部函数或锁实现
2. 中断程序（设备中断,ctrc-c退出程序等）发生或异常发生，其他进程可以占用内核

进程地址空间

1. 进程私有栈，数据，代码区，内核态运行时访问内核的代码区和数据，栈

同步和临界区

1. 信号量
    1. 一个整数，一个链表，down(),up()函数
    2. init=1, down时-1,当为-1时将当前进程加入链表挂起，up+1,当>=0时，链表上一进程执行
    3. 更新数据结构，效率低
2. 自旋锁
    1. 没有链表，一直执行获取锁
3. 死锁问题
    1. 信号和消息队列，相当于进程间进行通信

随机内存访问(ram)

1. 几兆存储内核代码和内核静态变量
2. 虚拟存储器系统管理

内核内存分配器



##### shell
<!-- 这是一张图片，ocr 内容为：表2-1 LINUX命令参数的长格式与短格式示例 长格式 --HE LP MAN 短格式 -H MAN -->
![](https://cdn.nlark.com/yuque/0/2023/png/32403817/1673578372562-9f5fcea9-7468-4288-972b-25c04dc9e315.png)

<!-- 这是一张图片，ocr 内容为：如前面所提到的,在LINUX系统中的命令参数有长短格式之分,长格式和长格式之 间不能合并,长格式和短格式之间也不能合并,但短格式和短格式之间是可以合并的, 合并后仅保留一个减号(-)即可.另外PS命令可允许参数不加减号(-),因此可直接 写成PS AUX 的样子. -->
![](https://cdn.nlark.com/yuque/0/2023/png/32403817/1673594172038-c28450e6-2b64-4a8d-b28e-1e6913b5aeb7.png)

###### 接受参数
<!-- 这是一张图片，ocr 内容为：$6,第6个位置参数 EFOURFIVESIX [ROOTQLINUXPROBE~]#./EXAMPLE.SH ONE TWO THREE FO $1,第1个位置参数 $2,第2个位置参数 -->
![](https://cdn.nlark.com/yuque/0/2023/png/32403817/1675134129266-9b3dc7e4-6538-4b6f-bcc3-8367229b3958.png)

$0 当前脚本名称

$1 第1个参数

$# 参数个数

$? <font style="color:rgb(0,0,0);">上一次命令的执行返回值</font>

<font style="color:#080808;background-color:#ffffff;">$* 所有传递给脚本或函数的参数</font>

###### <font style="color:rgb(0,0,0);">判断参数</font>
<!-- 这是一张图片，ocr 内容为：测试语句格式:[条件表达式] 两边均应有一个空格 -->
![](https://cdn.nlark.com/yuque/0/2023/png/32403817/1675134328085-23c76c0b-d650-4565-86cb-1aa2a858ffda.png)

**文件判断参数**

-d 测试文件是否为目录类型

-e 测试文件是否存在

-f 判断是否为一般文件

-r 测试当前用户是否有权限读取

-w 测试当前用户是否有权限写入

-x 测试当前用户是否有权限执行

**数值比较参数**

-eq 是否等于

-ne 是否不等于

-gt 是否大于

-lt 是否小于

-le 是否等于或小于

-ge 是否大于或等于

**字符串比较**

= 比较字符串内容是否相同

!= 比较字符串内容是否不同

-z 判断字符串内容是否为空

-n 判断字符串内容是否非空

###### 条件语句
```shell
while
#!/bin/bash
PRICE=$(expr $RANDOM % 1000)
TIMES=0
echo "商品实际价格为 0-999 之间，猜猜看是多少？"
while true
do
  	read -p "请输入您猜测的价格数目：" INT
  	let TIMES++
  	if [ $INT -eq $PRICE ] ; then
  		echo "恭喜您答对了，实际价格是 $PRICE"
  		echo "您总共猜测了 $TIMES 次"
  		exit
  	elif [ $INT -gt $PRICE ] ; then
  		echo "太高了！"
  	else
  		echo "太低了！"
  	fi
done

case
#!/bin/bash
read -p "请输入一个字符，并按 Enter 键确认：" KEY
case "$KEY" in
  [a-z]|[A-Z])
  	echo "您输入的是 字母。"
  	;;
  [0-9])
  	echo "您输入的是 数字。"
  	;;
  *)
  	echo "您输入的是 空格、功能键或其他控制字符。"
esac
```

> <font style="color:rgb(0,0,0);">/dev/null </font><font style="color:rgb(0,0,0);">是一个被称作 </font><font style="color:rgb(0,0,0);">Linux </font><font style="color:rgb(0,0,0);">黑洞的文件，把输出信息重定向到这个文件等同于删 </font>
>
> <font style="color:rgb(0,0,0);">除数据（类似于没有回收功能的垃圾箱），可以让用户的屏幕窗口保持简洁</font>
>

###### ~/.bash_history
shell history

##### 环境变量
**<font style="color:rgb(0, 0, 0);background-color:rgb(254, 254, 242);">全局：cat /etc/profile</font>**

**<font style="color:rgb(0, 0, 0);background-color:rgb(254, 254, 242);">用户：cat ~/.bash_profile，即当前用户目录下，HOME/.bash_profile</font>**

**<font style="color:rgb(0, 0, 0);background-color:rgb(254, 254, 242);">export: 导出为全局变量</font>**

##### **基础命令**
cd - <font style="color:rgb(0,0,0);">令返回到上一次所处的目录</font>

<font style="color:rgb(0,0,0);">find -exec {} \</font>

```bash
find / -user linuxprobe -exec cp -a {} /root/findresults/ \;
#在整个文件系统中找出所有归属于 linuxprobe 用户的文件并复制到/root/findresults 目录中
#{}表示 find 命令搜索出的每一个文件，并且命令的结尾必须是“\;”。
locate
yum install mlocate
updatedb
locate filename
```

top process cpu，内存信息

ls

ps process 所有进程信息

netstat  网络连接，端口号使用情况

ifconfig 挂载的网卡信息

lsof 系统当前打开的文件句柄

ss -tnlp | grep pid 查看进程占用端口

cat

```bash
more 
# 前10行
head -n 10
# 尾部10行
tail -n 10
tail -f filename 
```

rm

grep

kill

ctrl +c 切换到终端

ctrl +z 退出数据库

1. ps -elf | grep 进程名 根据进程名查进程ID

2)netstat -nap | grep 2862 根据ID查询进程开启端口

3)netstat -nap | grep 8080 根据端口号查看使用的进程

sudo 管理员权限

free 内存使用情况

##### history
ctrl + r : 搜索历史命令  
ctrl + p , ctrl + n : 上下翻查

##### file
+ which 查找命令
+ whereis 二进制文件，源代码，文档
    -  whereis ls  #如果上述三者有，则三者都会显示。  
       ls: /bin/ls /usr/share/man/man1/ls.1.gz  
       whereis -m ls #只查看ls的帮助手册  
       ls: /usr/share/man/man1/ls.1.gz  
       whereis -b ls #只查找ls的二进制文件  
       ls: /bin/ls  
       whereis stdio.h #查找stdio.h头文件，和帮助手册  
       stdio: /usr/include/stdio.h /usr/share/man/man3/stdio.3.gz  
       同样地，它不能查找到内置命令
+ locate
    - locate -r /locate.log$  #查找以/locate.log结尾的文件
    - locate -i locate.zip  忽略大小写查找  
      /home/hyb/workspaces/shell/locate/LOCATE.zip
+ find
    - find ./ -name "sort*" 当前目录下查找以sort开头的文件
+ tee
    - 将进程信息通过管道输出到标准输出（终端）并覆盖写入到文件中。
ps -ef |tee info_a.log info_b.log
- 将进程信息通过管道输出到标准输出（终端）并追加写入到文件中。   
ps -ef |tee -a info_a.log info_b.log



##### **管道命令**
【1】[linux管道命令学习（一） - mrzero - 博客园](https://www.cnblogs.com/mrzero/p/3985302.html)

##### 通配符
<!-- 这是一张图片，ocr 内容为：含义 通配符 任意字符 * 单个任意字符 I 单个小写字母 [A-Z] 续表 通配符 含义 单个大写字母 [A-Z] 单个字母 [A-Z] 单个数字 [0-9] 任意字母 [[:ALPHA:]] 任意大写字母 [[:UPPER:]] 任意小写字母 [[:LOWER:]] 所有数字 [[:DIGIT:]] 任意字母加数字 [[:ALNUM:]] 标点符号 [[:PUNCT: :]] -->
![](https://cdn.nlark.com/yuque/0/2023/png/32403817/1675132449086-e1abc767-511d-43d0-a3a0-149e43d7ecb6.png)

<font style="color:rgb(0,0,0);">touch {AA,BB,CC}.conf</font>

##### <font style="color:rgb(0,0,0);">ssh</font>
```sql
ssh
yum install -y openssh-server
vi /etc/ssh/sshd_config
	PubkeyAuthentication yes
systemctl restart sshd.service
logout #quit

tmux
tmux ls 
0: 1 windows (created Tue Jan 31 01:17:19 2023) [157x52] (attached)
tmux attach -t 0
exit
-- 共享ssh
-- a
ssh 192.168.10.10
tmux new -s share
-- b
ssh 192.168.10.10
tmux attach-session -t share
```

##### sftp & scp
```plain
sftp ip
get 远程服务器文件名
get -r 远程服务器文件夹名
put 本地服务器文件名
exit

scp /appl/app-schedule/aps-schedule-service.jar aps@10.98.101.45:/appl/app-schedule/new
```

##### mtux
```plain
prefix : ctrl + b
tmux
prefix % 左右创建
prefix " 上下创建
prefix o 切换
prefix x 关闭
```

##### log
```sql
journalctl 
```

##### <font style="color:rgb(0,0,0);">转义符</font>
```shell
“”：保留其中的变量属性，不进行转义处理
[root@linuxprobe~]# PRICE=5
[root@linuxprobe~]# echo "Price is $PRICE"
Price is 5

$$: 显示当前进程iD
[root@linuxprobe~]# echo "Price is $$PRICE"
Price is 3767PRICE

\: 使反斜杠后面的一个变量变为单纯的字符
[root@linuxprobe~]# echo "Price is \$$PRICE"
Price is $5

``:返回其中命令执行结果
[root@linuxprobe~]# echo `uname -a`
Linux linuxprobe.com 4.18.0-80.el8.x86_64 #1 SMP Wed Mar 13 12:02:46 UTC 2019
x86_64 x86_64 x86_64 GNU/Linux

'':转义其中所有的变量为单纯的字符串
```

##### 部署项目
```shell
nohup java -server -Xms256M -Xmx2048M  -jar  myserver.jar &>  mylog.log
2>&1
// 1表示标准输出，2表示标准错误输出，2>&1表示将标准错误输出重定向到标准输出
> 从头写
>> 追加写
2> 错误内内容输出
&>> 不区分标准输出和错误输出，追加写
< 读入文件信息
```

+ <font style="color:rgb(51, 51, 51);">&代表在后台运行。</font>

<font style="color:rgb(51, 51, 51);">特定：当前ssh窗口不被锁定，但是当窗口关闭时，程序中止运行。</font>

+ <font style="color:rgb(51, 51, 51);">nohup 意思是不挂断运行命令,当账户退出或终端关闭时,程序仍然运行</font>

<font style="color:rgb(51, 51, 51);">当用 nohup 命令执行作业时，缺省情况下该作业的所有输出被重定向到nohup.out的文件中。</font>

+ <font style="color:rgb(51, 51, 51);">command >out.file</font>

<font style="color:rgb(51, 51, 51);">command >out.file是将command的输出重定向到out.file文件，即输出内容不打印到屏幕上，而是输出到out.file文件中。</font>

+ job

<font style="color:rgb(0, 0, 0);">使用jobs命令查看后台任务</font>

##### <font style="color:rgb(0, 0, 0);">文件</font>
###### 文件结构
/boot 开机所需文件—内核、开机菜单以及所需配置文件等

/dev 以文件形式存放任何设备与接口

/etc 配置文件

/home 用户主目录

/bin 存放单用户模式下还可以操作的命令

/lib 开机时用到的函数库，以及/bin 与/sbin 下面的命令要调用的函数

/sbin 开机过程中需要的命令

/media 用于挂载设备文件的目录

/opt 放置第三方的软件

/root 系统管理员的家目录

/srv 一些网络服务的数据文件目录

/tmp 任何人均可使用的“共享”临时目录

/proc 虚拟文件系统，例如系统内核、进程、外部设备及网络状态等

/usr/local 用户自行安装的软件

/usr/sbin Linux 系统开机时不会使用到的软件/命令/脚本

/usr/share 帮助与说明文件，也可放置共享文件

/var 主要存放经常变化的文件，如日志

/lost+found 当文件系统发生错误时，将一些丢失的文件片段存放在这里

###### 磁盘
```sql
df -h
du -sh /*
```

###### raid
raid0

<!-- 这是一张图片，ocr 内容为：RAID O BLOCK BLOCK  2 BLOCK 3 BLOCK 4 BLOCK 6 BLOCK 5 BLOCK 7 BLOCK 8 硬盘B 硬盘A -->
![](https://cdn.nlark.com/yuque/0/2023/png/32403817/1675147331464-42d9f943-0728-4c5c-b13c-2845aec56da9.png)

<font style="color:rgb(0,0,0);">追求最大容量和速度；但是任何一块硬盘损坏，数据将全部异常</font>

raid1

<!-- 这是一张图片，ocr 内容为：RAID 1 BLOCK 1 BLOCK 1 BLOCK 2 BLOCK 2 BLOCK 3 BLOCK 3 BLOCK 4 BLOCK 4 硬盘A 硬盘B -->
![](https://cdn.nlark.com/yuque/0/2023/png/32403817/1675147521515-c2c8e3c8-a7a8-4c35-97eb-0398e45228ce.png)

<font style="color:rgb(0,0,0);">追求最大安全性，只要阵列中有一块硬盘可用，数据就不受影响</font>

<font style="color:rgb(0,0,0);">raid5</font>

<!-- 这是一张图片，ocr 内容为：RAID 5 BLOCK 2 BLOCK PARITY BLOCK PARITY BLOCK 4 BLOCK 6 PARITY BLOCK 5 硬盘C 硬盘A 硬盘B -->
![](https://cdn.nlark.com/yuque/0/2023/png/32403817/1675148096658-7bf6ac44-3188-4e68-8ed8-b2efedd887f0.png)

<font style="color:rgb(0,0,0);">磁盘阵列中数据的奇偶校验信息并不是单独保存到某一块硬盘设备中，而是存储到除自身以外的其他每一块硬盘设备上;</font>

<font style="color:rgb(0,0,0);">Parity 部分存放的就是数据的奇偶校验信息;当硬盘设备出现问题后通过奇偶校验信息来尝试重建损坏的数据;</font>

<font style="color:rgb(0,0,0);">兼顾了硬盘设备的读写速度、数据安全性与存储成本问题</font>

<font style="color:rgb(0,0,0);">raid10</font>

<!-- 这是一张图片，ocr 内容为：RAID 10 RAID0 RAID RAID 1 BLOCK BLOCK 2 BLOCK BLOCK BLOCK 3 BLOCK 4 BLOCK 3 BLOCK BLOCK 5 BLOCK 5 BLOCK BLOCK 6 BLOCK 8 BLOCK 7 BLOCK 7 BLOCK 8 硬盘D 硬盘 硬盘A 硬盘B -->
![](https://cdn.nlark.com/yuque/0/2023/png/32403817/1675148317883-178b9135-eb32-47dd-a522-3ba734c4f9b9.png)

<font style="color:rgb(0,0,0);">综合 RAID 1 和 RAID 0 的优点，追求硬盘的速度和安全性，允许有一半硬盘出现异常（不可发生在同一阵列中），且数据不受影响</font>

###### <font style="color:rgb(0,0,0);">lvm</font>
<!-- 这是一张图片，ocr 内容为：VG 卷组) PV(物理卷) (物理卷) PE PE PE LV(逻辑卷) LV(逻辑卷) -->
![](https://cdn.nlark.com/yuque/0/2023/png/32403817/1675149186175-d82042d5-7899-421a-bdef-2b06ade88921.png)

##### <font style="color:rgb(51, 51, 51);">查看文件</font>
[使用Linux命令快速查看某一行 - 掘金](https://juejin.cn/post/7009583589142560781)

```shell
查看第200行
cat temp.log | sed -n '200p'  //sed命令能直接用行号匹配，匹配到的行用p指令输出就行了
查看前200行
cat temp.log | head -n 200 
查看第90到100行
cat temp.log | sed -n '90,100p'
查看符合行开始往后200行
cat out.log | grep -A 200 '异步调用算法'

cat 1.log | less
ctrl + F - 向前移动一屏
ctrl + B - 向后移动一屏
G - 移动到最后一行
g - 移动到第一行
h - 显示 less 的帮助文档
```

##### <font style="color:rgb(51, 51, 51);">统计文件</font>
```shell
find -name "*.js" | wc -l // 统计当前文件夹下 js 文件的数量
ls -l | grep "^-" | wc -l // 统计当前目录下文件的个数（不包括目录）
ls -lR| grep "^-" | wc -l // 统计当前目录下文件的个数（包括子目录）
find . -name filename | wc -l // 统计当前文件夹下叫某某的文件的数量

```

##### 文件类型
```sql
Linux中第一个字符代表这个文件是目录、文件或链接文件等等。
当为[ d ]则是目录
当为[ - ]则是文件；
若是[ l ]则表示为链接文档(link file)；
若是[ b ]则表示为装置文件里面的可供储存的接口设备(可随机存取装置)；
若是[ c ]则表示为装置文件里面的串行端口设备，例如键盘、鼠标(一次性读取装置)。
接下来的字符中，以三个为一组，且均为『rwx』 的三个参数的组合

设置隐藏属性
chattr +a filename
lsattr filename
```

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2022/png/32403817/1666958119581-788cd2f8-a74c-4568-8d20-173dd4de4b5a.png) + uerId+groupId

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2024/png/32403817/1709781624760-9ed2e99e-1a89-40a6-98c5-f9375b60ced7.png)

##### 内存占用查看
[https://juejin.cn/post/6878221167572811784](https://juejin.cn/post/6878221167572811784)

```shell
ps aux | sort -k4nr | head -n 10
比如说sort -k 1 -k 2 test.txt，那么就会根据test.txt文件中的第一列和第二列进行排序。

-n：依照数值的大小排序；
-r：以相反的顺序来排序

1）USER: 行程拥有者  
2）PID: 进程的ID  
3）%CPU: 占用的 CPU 使用率  
4）%MEM: 占用的记忆体使用率  
5）VSZ: 占用的虚拟记忆体大小  
6）RSS: 占用的记忆体大小  
7）TTY: 终端的次要装置号码 (minor device number of tty)  
8）STAT: 该行程的状态:  
        D: 不可中断的静止  
        R: 正在执行中  
        S: 静止状态  
        T: 暂停执行  
        Z: 不存在但暂时无法消除  
        W: 没有足够的记忆体分页可分配  
        <: 高优先序的行程  
        N: 低优先序的行程  
        L: 有记忆体分页分配并锁在记忆体内  
9）START: 行程开始时间  
10）TIME: 执行的时间  
11）COMMAND:所执行的指令
```



##### [CentOS 7 容器内替换 apt-get 源为阿里源](https://www.cnblogs.com/Areas/p/13613364.html)
<font style="color:rgb(51, 51, 51);">sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak </font>

<font style="color:rgb(51, 51, 51);">echo</font><font style="color:rgb(51, 51, 51);"> </font><font style="color:rgb(51, 51, 51);">""</font><font style="color:rgb(51, 51, 51);">>sources.list </font>

<font style="color:rgb(51, 51, 51);">echo</font><font style="color:rgb(51, 51, 51);"> </font><font style="color:rgb(51, 51, 51);">"deb https://mirrors.aliyun.com/debian  stable main contrib non-free"</font><font style="color:rgb(51, 51, 51);">>>sources.list </font>

<font style="color:rgb(51, 51, 51);">echo "deb https://mirrors.aliyun.com/debian  stable-updates main contrib non-free">>sources.list</font>

[VirtualBox扩容CentOS-7虚拟机磁盘 - Javaer1995 - 博客园](https://www.cnblogs.com/Javaer1995/p/18246309)

##### << eof
[linux <<eof - 潇湘灬隐者 - 博客园](https://www.cnblogs.com/xiaojianblogs/p/6486902.html)

##### ~
<font style="color:rgb(0, 128, 0);background-color:rgb(245, 245, 245);">~/ 表示的目录是 /home/<用户目录>/ 或者 /root/</font>

##### <font style="background-color:rgb(245, 245, 245);">rysnc</font>
##### kswapd0 cpu 100%
[https://blog.csdn.net/baidu_29679655/article/details/124266619](https://blog.csdn.net/baidu_29679655/article/details/124266619)

##### 安装jdk8
```plain
java -version 
wget https://repo.huaweicloud.com/java/jdk/8u181-b13/jdk-8u181-linux-x64.tar.gz

tar -zvxf jdk-8u181-linux-x64.tar.gz 

vim /etc/profile

export JAVA_HOME=/usr/local/java/jdk1.8   # java解压的路径
export PATH=$PATH:$JAVA_HOME/bin
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
export JRE_HOME=$JAVA_HOME/jre

source /etc/profile

java -version
```

# mvn
##### mvn package -DskipTests
#### nexus
##### 类型
+ group(仓库组类型)：又叫组仓库，用于方便开发人员自己设定的仓库；
+ hosted(宿主类型)：内部项目的发布仓库（内部开发人员，发布上去存放的仓库）
+ proxy(代理类型)：从远程中央仓库中寻找数据的仓库（可以点击对应的仓库的 Configuration 页签下 Remote Storage Location 属性的值即被代理的远程仓库的路径）
    - 这里就是代理的意思，代理远程中央 Maven 仓库，当 项目构建访问中央库的时候，先通过代理去远程中央仓库下载依赖包到Nexus 仓库，然后再从Nexus仓库下载到本地。私服我们部署在内网服务器，只要其中一个人从远程中央库下来了，以后相同的依赖包就都是从Nexus私服上进行下载，这样大大加快下载速度，不怕远程中央仓库出现问题

##### 上传jar
###### 本地仓库
```shell
mvn install:install-file 
-DgroupId=org.example 
-DartifactId=OssSpringBootStarter 
-Dversion=1.0-SNAPSHOT -Dpackaging=jar 
-Dfile=D:\1\spirng\testJAR\OssSpringBootStarter\target\OssSpringBootStarter-1.0-SNAPSHOT.jar

```

###### 私服
[Maven安装Jar包到本地仓库和上传到私服仓库 - 掘金](https://juejin.cn/post/6862983277691535374)

> 私服仓库类型必须为hosted
>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0 http://maven.apache.org/xsd/settings-1.0.0.xsd">
    <localRepository>D:\maven\ycc_repo</localRepository>
    <servers>
        <server>
            <id>fs</id>
            <username>admin</username>
            <password>admin123</password>
        </server>
    </servers>
    <mirrors>
        <mirror>
            <id>alimaven</id>
            <name>aliyunmaven</name>
            <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
            <mirrorOf>central</mirrorOf>
        </mirror>
    </mirrors>
    <profiles>
        <profile>
            <id>default</id>
            <activation>
                <jdk>1.8</jdk>
            </activation>
            <repositories>
                <repository>
                    <id>fs</id>
                    <name>fs</name>
                    <url>http://192.168.137.73:8081/repository/fs/</url>
                    <releases>
                        <enabled>true</enabled>
                    </releases>
                    <snapshots>
                        <enabled>true</enabled>
                    </snapshots>
                </repository>
            </repositories>
        </profile>
    </profiles>
    <activeProfiles>
        <activeProfile>default</activeProfile>
    </activeProfiles>
</settings>

```

1. pom引入

```xml
<distributionManagement>
  <!-- 这里可以配置多个比如snapshots快照仓库 这里只配置一个 -->
  <repository>
    <id>fs</id>
    <name>fs-hostes</name>
    <url>http://192.168.137.73:8081/repository/fs/</url>
    <uniqueVersion>true</uniqueVersion>
  </repository>
</distributionManagement>
```

2. mvn deploy

```shell
mvn deploy:deploy-file 
-Dfile=本地jar的路径 -DgroupId=你的jar包groupId -DartifactId=你的jar包名称  
-Dversion=你的jar包版本号 -Dpackaging=jar -Durl=你的私服路径 -DrepositoryId=你的仓库ID

mvn deploy:deploy-file -Dfile=D:\1\spirng\testJAR\nexusTest\target\nexusTest-1.0-SNAPSHOT.jar 
-DgroupId=com.fs -DartifactId=nexusTest  -Dversion=1.0-SNAPSHOT -Dpackaging=jar 
-Durl=http://192.168.137.73:8081/repository/fs/ -DrepositoryId=fs -e
```

<font style="color:rgb(0, 0, 0);background-color:rgb(245, 245, 245);"></font>

