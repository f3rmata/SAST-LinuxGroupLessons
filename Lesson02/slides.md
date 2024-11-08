---
theme: seriph
title: Linux 进阶教程
class: text-center
transition: slide-left
mdc: true
overviewSnapshots: true
---

# Linux 进阶教程

软件研发部 运维组

---
layout: quote
---

## 进程，信号和服务

- 什么是进程？
  - 简单而不太严谨地来说，进程就是正在运行的程序：当我们启动一个程序的时候，操作系统会从硬盘中读取程序文件，将程序内容加载入内存中，之后 CPU 就会执行这个程序。
<!-- 进程是现代操作系统中必不可少的概念。在 Windows 中，我们可以使用「任务管理器」查看系统运行中的进程；Linux 中同样也有进程的概念。 -->

- 管理进程的程序
  - ps: 静态的输出当前进程的信息，通常和管道符配合使用，过滤出所需的进程信息。
  - top: 动态查看当前所有程序的信息，但是功能较少。
  - htop: 在top的基础上添加了彩色界面，更容易识别和查看。同时给出快捷键提示，并支持控制进程。

<!-- 这里我们选择使用htop来演示，同时为后面发送信号的内容做铺垫。 -->

---
layout: quote
---

## htop界面介绍

  > htop可以使用鼠标点击绿色这一栏来进行排序，同时也支持点击进程使用，但因此jk键被赋予了别的意义而不是上下翻页...

- 上面几行显示的就是系统整体相关信息。
  - task: 进程
  - thr: 线程
  - running: 正在运行的前台服务
  - Load Average: cpu的负载情况。三个值分别为1分钟，3分钟，15分钟内的负载情况。

<Transform :scale="1">
<img
border="rounded"
src="/htop.png"
alt=""
/>
</Transform>

<!-- 
线程和进程有什么关系？
可以这么理解做个简单的比喻：进程=火车，线程=车厢

线程在进程下行进（单纯的车厢无法运行）
一个进程可以包含多个线程（一辆火车可以有多个车厢）
不同进程间数据很难共享（一辆火车上的乘客很难换到另外一辆火车，比如站点换乘）
同一进程下不同线程间数据很易共享（A车厢换到B车厢很容易）
进程要比线程消耗更多的计算机资源（采用多列火车相比多个车厢更耗资源）
进程间不会相互影响，一个线程挂掉将导致整个进程挂掉（一列火车不会影响到另外一列火车，但是如果一列火车上中间的一节车厢着火了，将影响到所有车厢）

为什么Load Average要显示三个数据?
我们在维护服务器的时候，需要了解服务器的运行状态。这几个数据可以让我们了解这一段时间服务器的运行状况。防止出现进程阻塞的情况。 -->

- 下面会显示所有进程的列表。
  - PID: 进程的标识符。
  - N: nice值，表示进程的优先级(可被用户管理的，区分于内核管理的PRI)
  - S: 进程的状态。R: running; S: sleeping; T: traced/stopped; Z: zombie; D: disk sleep

---
layout: quote
---

## 前台和后台

<br/>

- `Ctrl + Z`
![jobs](/Ctrl+Z.png)
- `jobs`
- `fg %1`

<br/>

## 退出终端，让程序继续运行

<br/>

- 在命令前面使用`nohup`。如果想直接让它在后台运行，在命令后面加上`&`。
- 当然，我们更推荐使用`tmux`(一个终端复用器)。

<!--
在 Linux 系统中，有时我们需要执行一些运行时间较长的任务，而又不希望它们阻塞我们的工作。此时，如何在不终止任务的情况下暂时搁置它，去做其他事情呢？

Ctrl + Z 是一个非常实用的快捷键，用于将当前在前台运行的进程暂停并放入后台。这样你就可以暂时停止该进程，去做其他事情。

按下 Ctrl + Z 后，进程并没有消失，它只是被挂起并放入了后台。

如果你发现进程好像消失了，别担心！你可以通过 jobs 命令查看当前所有后台任务及其状态。

运行此命令后，你会看到类似以下输出：

其中 1 表示任务编号，1234 是进程 ID，Suspended 表示进程当前被挂起。

如果你想将挂起的进程恢复到前台，可以使用 fg 命令，后面加上任务编号。如果没有指定编号，默认会恢复最后一个挂起的任务。

如果你不想将进程恢复到前台，而是希望继续在后台运行，可以使用 bg 命令。这样进程会在后台继续执行，直到完成。不过值得一提的是，如果软件仍然把内容发到标准输出，会把你的终端弄得乱七八糟。

有时我们需要让某个进程在后台运行，尤其是当我们需要退出当前终端会话时，仍希望进程保持运行。这里有两种常见的做法：使用 nohup 和 &。

nohup（即“no hang up”）命令可以使得一个进程在终端关闭后仍然继续运行。这意味着即使你退出当前的会话，进程也不会因为挂起信号（SIGHUP）而被终止。

一个终端（硬件概念）只有一套鼠标键盘，只能有一个 shell 主持一个 session，那如果我在 SSH 连接的时候有几个程序需要同时交互的话，只有一个前台进程很不方便。而且上面说过如果 SSH 网络断开，视为终端被关闭，也就意味着前后台一起收到 SIGHUP 一起退出，好不容易设置好的临时变量什么的还得重设。

开启多个 SSH 连接似乎可以解决这个问题。但是如果程序既需要交互，又想保证不因意外断线而停止程序，就是 nohup 也帮不了。

这时 tmux 的出现，解决了会话保持与窗口复用的问题。正如上图所示，tmux 是一个分屏的、运行在命令行的模拟终端，意味着只要有命令行可用，就可以将多个交互进程集成在在一个窗口上。该窗口不因断开连接或者暂时登出而消失，而是会保存在后台，下一次登录时可以立即还原。
-->

---
layout: quote
---

![tmux](/tmux.png)

<!--
1. 工作目录
在 tmux 中启动新窗格时，它通常会继承你当前的工作目录。这对日常工作很方便，比如你可以在一个目录中打开多个 pane，每个 pane 都可以执行不同的任务，而无需重新定位路径。
这就意味着你可以专注于你的工作路径和环境，无需反复地调整当前目录。

2. 很多 tmux 配置会使用绿色状态栏，通常位于窗口底部。这个状态栏显示了会话的基本信息，包括时间、窗口编号和会话名称。这个小绿条对管理多个会话和 pane 非常有用，让你时刻了解当前处于哪个 pane。

3. 窗口底部这个状态栏显示了会话的基本信息，包括时间、窗口编号和会话名称。这个小绿条对管理多个会话和 pane 非常有用，让你时刻了解当前处于哪个 pane。

4. 默认情况下，tmux 不支持鼠标滚动，直接使用鼠标滚动会无法查看 pane 的历史输出。这对习惯使用鼠标滚动的人来说会有些不便。虽然可以通过配置打开鼠标支持，但是我更推荐命令后面跟上|less 来筛选，跟上-S 可以左右移动。
演示一下
-->

---
layout: quote
---

## tmux  

```shell
sudo apt install tmux # 安装tmux
tmux # 运行tmux
```

很好，现在我们已经创建并进入tmux的一个会话了！tmux会保留我们在之前shell所在的目录。接下来是一些常用的命令：

- `ctrl + b`: 这是tmux的修饰键，我们在tmux里面按下的快捷键都会发到tmux内的终端。按下这个键，我们可以告诉tmux：我要开始管理这个会话了。
  - d: 分离终端，回到原来的shell。
  - c: 新建窗口。
  - %: 左右拆分。
  - ": 上下拆分。
  - 方向键: 切换拆分后的窗口。
  - z: 放大当前的终端。

---

## 信号

- 那么，进程间怎么进行一些通信呢？
  - 由于进程之间不共享内存空间，也就无法直接发送信息，必须要操作系统帮忙，于是信号机制就产生了。  

- 当我们在htop中按下k的时候，会让我们选择一系列信号。这里挑选一些常见的信号。
  - SIGKILL (9)：强制终止程序。无法被捕获或忽略，适用于需要立即终止进程的情况。
  - SIGTERM (15)：请求程序正常终止。可以被捕获和忽略，通常用于优雅关闭应用。
  - SIGINT (2)：从终端发送中断信号，通常由 Ctrl+C 触发，用于终止前台进程。
  - SIGTSTP (20): 作用是暂停进程的执行。这个信号是由用户在终端中通过按下 Ctrl+Z 发送的。
  - SIGCONT (18)：用于继续运行进程，也就是我们上面提到的`fg`和`bg`命令。
  - SIGHUP (1)：终端挂起信号，在退出终端的时候，会向与终端关联的进程发送这个信号。也就是我们上面提到的`nohup`。

<!--
当我们谈论 进程间通信 时，首先要了解进程是如何进行信息传递的。由于进程之间不共享内存，无法直接交换数据，它们需要依靠操作系统的帮助来进行通信，而 信号 就是其中一种关键机制。

在 Linux 系统中，信号是一种用于通知进程事件的机制。通过发送信号，操作系统可以告诉进程发生了什么（比如错误、终止请求等）。

信号的接收者是进程，进程可以选择如何响应信号：捕获、忽略或默认响应。

python演示SIGINT  
docker的停止来演示SIGKILL和SIGTERM
-->

---
layout: quote
---

## 服务

> 说到「服务」，我们可能会想到服务器，上面运行着各式各样的网络服务。但是这里的「服务」不止于此，系统的正常运行也需要关键服务的支撑。在 Windows 中，我们可以从任务管理器一窥 Windows 中的「服务」；Linux 也有服务的概念，下面将作介绍。

服务的特征，意味着服务进程必须独立于用户的登录，不能随用户的退出而被终止。

---
layout: quote
---

## 从PID1说起

首先Linux内核启动，然后在用户空间中启动init进程，再启动其他系统进程。在系统启动完成完成后，init将变为守护进程监视系统其他进程。

<Transform :scale="0.9">
<img
border="rounded"
src="/pid1.png"
alt=""
/>
</Transform>
<br/>

- `SysV Init` --> `Systemd`
- Unix 设计哲学—— “Do one thing and Do it well”  
- *有兴趣可以看看后面的参考资料*

<!-- PID 1 这个进程非常特殊，其主要就任务是把整个操作系统带入可操作的状态。比如：启动 UI – Shell 以便进行人机交互，或者进入 X 图形窗口。传统上，PID 1 和传统的 Unix System V 相兼容的，所以也叫 sysvinit，这是使用得最悠久的 init 实现。Unix System V 于 1983 年 release。  
但是由于各种历史包袱，SysV Init显现出了各种缺点：慢，拓展性差，后人在前人的基础上进行了各种改进，比如Upstart。但是，在2010这一年，Systemd他来了...  
Systemd可以说是管理系统全能的工具。d一般来说指的是daemon，也就是守护进程。顾名思义，Systemd想做的就是守护整个系统。
但是很多人不同意，对于把一件事情做好来说，Systemd干的事情太多了：systemctl, journalctl, localectl, timedatectl...所以面世以来产生了不少争议，我认为这是有进步的，你们觉得呢？-->

---
layout: quote
---

## Systemd

Systemd 可以管理所有系统资源。不同的资源统称为 Unit（单位）。我们后面会看到的Target，其实是各种的Unit构成的组。

- 查看系统服务运行情况: `systemctl status`

- 查看某个服务最后的日志: `journalctl -eu <单元名>`
  - `-e`: 滚动到底部
  - `-u`: 后面跟上单元名
  - 当然可以用`--since`和`--until`来指定某个时间段的日志，具体的可以在man里面查看。

<Transform :scale="0.5">
<img
border="rounded"
src="/systemctl-status.png"
alt=""
/>
</Transform>

<!--
此要管理服务，首先我们要清楚系统内有哪些服务。这里使用的是`less`翻页器~~支持j和k翻页，vim赛高~~  
如果作为服务启动，通常日志都不会给到标准输出上。
-->

---
layout: quote
---

## 创建一个自己的服务！

包括三个节：[Unit], [Service], [Install]
一个简单的示例：

创建文件
`sudo vim /etc/systemd/system/ping.service`

写入：

```ini
[Unit]
Description=Test Network
After=network.target

[Service]
ExecStart=ping 1.1.1.1
ExecStop=/bin/kill -15 $MAINPID | echo $MAINPID

[Install]
WantedBy=multi-user.target
```

重载配置文件：

```shell
sudo systemctl daemon-reload`
```

<!--
演示一
-->

---
layout: quote
---

## 使用systemctl控制服务

```shell
sudo systemctl enable/disable ping # 启用服务，指的是开机启动
sudo systemctl start ping #开启服务，systemctl里，.service可以省略
sudo journalctl -xeu/-xfu ping # 查看日志，滚动到底部/跟随输出
sudo systemctl stop ping # 停止服务
```

---
layout: quote
---

# 下一章：用户, 权限

---
layout: quote
---

## 用户和用户组

- 早期系统没有用户的概念(MS-DOS)
- 服务器环境需要为不同的用户分配不同的权限，从而限制访问资源的能力。
- 为程序/服务创建用户(系统用户)，从而减小被攻击的时候蒙受的损失。
- 用户组则方便统一管理所需权限相似的用户。

<!-- 早期的操作系统没有用户的概念（如 MS-DOS），或者有「用户」的概念，但是几乎不区分用户的权限（如 Windows 9x）。而现在，这不管对于服务器，还是个人用户来说，都是无法接受的。  

在服务器环境中，「用户」的概念是明确的：服务器的管理员可以为不同的使用者创建用户，分配不同的权限，保障系统的正常运行；也可以为网络服务创建用户（此时，用户就不再是一个有血有肉的人），通过权限限制，以减小服务被攻击时对系统安全的破坏。  

而对于个人用户来说，他们的设备不会有第二个人在使用。此时，现代操作系统一般区分使用者的用户与「系统用户」，并且划分权限，以尽可能保证系统的完整性不会因为用户的误操作或恶意程序而遭到破坏。  

举例文件服务器，使用setfacl在系统层面控制能访问的文件-->

---
layout: quote
---

## 看看系统中有哪些用户和用户组？

`/etc/passwd`

![passwd](/passwd.png)

`/etc/groups`

![shadow](/shadow.png)

<!-- 这里的冒号用于分隔内容，每一行依次是用户名，密码x代表隐藏，存储哈希于shadow中，UID，PID，用户介绍，home目录`~`，登录用的shell(nologin表示不能登录，因为是系统用户) -->

---
layout: quote
---

## root用户

- 聪明的你一定发现，我们前面会使用`sudo`命令，这个就是以root身份运行程序。
- root具有系统上最大的权限。你也许听说过：

> 我们信任您已经从系统管理员那里了解了日常注意事项。  
> 总结起来无外乎这三点：  
> #1) 尊重别人的隐私。  
> #2) 输入前要先考虑(后果和风险)。  
> #3) 权力越大，责任越大。

<!--
1. **root 用户的权限：**
   - **root** 用户是系统上权限最高的用户，拥有完全的控制权。
   - 通过 `sudo`，我们可以执行许多普通用户无法执行的操作，例如修改系统配置、安装和删除软件等。这也是为什么 `sudo` 要求输入密码的原因。

2. **系统管理员的警告：**
   - 在系统中，尤其是在使用 `sudo` 时，系统管理员会向你提醒几项基本的注意事项。为了安全和系统的稳定，使用 **root 权限** 时必须格外小心。
   - 这也正是很多系统会给出类似的警告和提示，确保我们意识到操作时可能的风险。

3. **三点总结：**
  root给我们很大的控制力，但同样也要求我们承担更大的责任。我们必须小心操作，避免造成不可挽回的损失。
-->

---
layout: quote
---

## 用户组的妙用

- 把用户加入admin/sudo组，避免了修改sudoers的麻烦。
- 把用户加入docker组，该用户就不需要sudo就可以使用docker命令。

<!--
我们通常可以通过将用户添加到 admin 或 sudo 组，来赋予该用户使用 sudo 命令的权限。
这样做的好处是避免直接修改 /etc/sudoers 文件，确保系统更安全且易于管理。
只需将用户添加到 sudo 组，系统就会自动给予该用户执行 sudo 命令的权限，而无需更改敏感配置文件。

若希望某个用户能够使用 Docker 命令，但又不想每次都使用 sudo，可以将该用户添加到 docker 组。
这样，该用户就能直接使用 Docker 命令，无需每次前缀加 sudo。这对于频繁使用 Docker 的开发者或管理员来说，非常便捷。

通过将用户加入适当的组，系统管理员可以简化权限管理，避免频繁编辑 sudoers 文件，同时提高系统的安全性和易管理性。
记住，将用户加入组时，要确保他们只拥有执行所需操作的最低权限，避免过度授权，这也是最小权限的原则。
-->

---
layout: quote
---

## 对用户进行操作

```shell
useradd # 添加用户
usermod # 修改用户参数
userdel # 删除用户
```

这些命令可以用-h参数来获取提示，接下来我们进行演示。

<!-- useradd ruanxiaoyan -m -s /bin/bash  
usermod ruanxiaoyan -aG sudo 加到sudo组，就无需修改sudoers文件  
userdel ruanxiaoyan -r 删除用户目录和邮件   -->

---
layout: quote
---

## 文件权限

当我们使用`ls -lh`的时候，会输出类似下面的内容：

```shell
drwxr-xr-x  4 texsd texsd 4.0K 10月31日 09:56 Videos
drwxr-xr-x  4 texsd texsd 4.0K 10月14日 19:40 yay-git
drwxr-xr-x  3 texsd texsd 4.0K  9月15日 20:47 .zim
-rw-------  1 texsd texsd 1.5K  9月15日 20:14 .bash_history
-rw-r--r--  1 texsd texsd   21  8月27日 13:59 .bash_logout
-rw-r--r--  1 texsd texsd   57  8月27日 13:59 .bash_profile
```

- 对于文件来说，第一列的字符串从左到右意义分别是：  
  - 文件类型（一位）  
  - 文件所属用户的权限（三位）  
  - 文件所属用户组的权限（三位）  
  - 其他人的权限（三位）

<!--
接下来，我们将讨论 文件权限 的管理，这是 Linux 系统中非常重要的一部分。文件权限决定了不同用户对文件或目录的访问级别，理解这些权限对于保证系统安全和高效管理至关重要。

我们可以使用 ls -lh 命令来查看文件和目录的权限。该命令输出的信息看起来像这样：

第一列的字符串代表了文件或目录的权限结构，格式为 rwxrwxrwx，其中：

    第1位：表示文件类型（d 表示目录，- 表示文件）。
    第2到第4位：表示文件所有者（用户）的权限。
    第5到第7位：表示文件所属用户组的权限。
    第8到第10位：表示其他用户的权限。
对于文件夹来说，rwx代表的含义有点不太一样，r是能否使用ls命令列出，w是修改内部的文件名，增加和删除文件，x是能够cd进入目录。
-->

---
layout: quote
---

## 

```shell
drwxr-xr-x  4 texsd texsd 4.0K 10月31日 09:56 Videos
drwxr-xr-x  4 texsd texsd 4.0K 10月14日 19:40 yay-git
drwxr-xr-x  3 texsd texsd 4.0K  9月15日 20:47 .zim
-rw-------  1 texsd texsd 1.5K  9月15日 20:14 .bash_history
-rw-r--r--  1 texsd texsd   21  8月27日 13:59 .bash_logout
-rw-r--r--  1 texsd texsd   57  8月27日 13:59 .bash_profile
```

- 对于每个权限：
  - 第一位 r 代表读取 (Read) - 4  
  - 第二位 w 代表写入 (Write) - 2  
  - 第三位 x 代表执行 (Execute) - 1  
  - `-` 代表没有对应的权限。

  要修改它们，使用`chmod <644|755|600> <文件>`修改。

- 第二位指的是硬链接数。
- 第三位，第四位分别是所有者，所有组；分别用`chown`和`chgrp`修改，这两个后面都先跟着用户名/组名，然后再接着文件。

<!--
在 ls -lh 输出的第二列显示的是 硬链接数，即指向该文件的 硬链接 数量。
什么是硬链接呢？简单来说，硬链接是一个文件的多个不同路径。每当您创建一个硬链接时，系统实际上会为同一个文件分配一个新的名字。
重要的是，硬链接的存在并不会增加文件的实际存储空间，而是让多个文件名指向相同的存储区域。换句话说，硬链接指向的是文件的数据块，而不是文件本身的路径。
-->

---
layout: quote
---

## 配置一个C语言环境

赶紧掏出我们那具有**超级牛力**的apt吧：

```shell
sudo apt install build-essential
```

然后试试gcc:

```shell
gcc -v
```

<!-- 这里的build-essential包含了编译需要的很多工具 -->

---
layout: quote
---

## 了解编译的过程

当我们要完成大型项目的时候，makefile是我们的得力助手，它定义了哪些文件需要编译，编译的顺序以及编译的依赖关系。

```makefile
main: fun.o main.o
g++ -Wall fun.o main.o -o main

main.o: main.cpp
g++ -Wall -c main.cpp -o main.o

fun.o: fun.cpp fun.h
g++ -Wall -c fun.cpp -o fun.o

.PHONY: clean
clean:
rm *.o
```

<!-- 将目标文件 fun.o 和 main.o 链接为可执行文件 main。-Wall 参数启用所有警告，帮助识别潜在错误。最终生成的可执行文件为 main。  
将 main.cpp 编译为目标文件 main.o。-c 表示只编译不链接。-Wall 会启用所有编译警告。  
将 fun.cpp 编译为目标文件 fun.o，依赖头文件 fun.h。-c 表示不进行链接，仅生成目标文件。-Wall 用于开启警告。  
声明 clean 为伪目标，用于清理编译生成的文件。运行时会删除所有 .o 文件和 main 可执行文件。 -->

---
layout: quote
---

## 探测内存泄漏

对于C/C++来说，内存泄漏是个问题，我们在Linux上可以用valgrind来探测。
写一个带malloc的c程序，但是不释放：

```c
#include <stdio.h>
#include <stdlib.h>

void memoryLeakExample() {
    int* leakyArray = (int*)malloc(10 * sizeof(int));  // 动态分配内存
    for (int i = 0; i < 10; ++i) {
        leakyArray[i] = i;
    }
    // 未释放 leakyArray，造成内存泄漏
}

int main() {
    memoryLeakExample();
    return 0;
}
```

---
layout: quote
---

使用gcc编译：`gcc -Wall -g leak.c -o leak`  
使用`valgrind --leak-check=full ./leak`，valgrind会输出：  

```shell
...
==10612== 40 bytes in 1 blocks are definitely lost in loss record 1 of 1
==10612==    at 0x4C2B9BB: malloc (in /usr/lib/valgrind/vgpreload_memcheck-amd64-linux.so)
==10612==    by 0x10916A: memoryLeakExample (leak.c:5)
==10612==    by 0x109189: main (leak.c:11)
...
==10612== ERROR SUMMARY: 1 errors from 1 contexts (suppressed: 0 from 0)
```

添加`free(leakyArray); `

```shell
...
==10612== All heap blocks were freed -- no leaks are possible.
...
==10612== ERROR SUMMARY: 0 errors from 0 contexts (suppressed: 0 from 0)
```

此时内存泄漏就被解决了。

---
layout: quote
---

# 搭建MC服务器

1. 我们选择用java版，所以先安装jdk：

```shell
sudo apt install openjdk-21-jdk-headless tmux
```

    确认安装：

```shell
java --version
```

2. 创建一个跑mc服务器的用户：

```shell
useradd -m -r -s /bin/bash mc
sudo -i
su -- mc
cd ~
```

3. 从[官方](https://www.minecraft.net/zh-hans/download/server)下载服务端二进制文件。

```shell
mkdir server && cd server
wget https://piston-data.mojang.com/v1/objects/45810d238246d90e811d896f87b14695b7fb6839/server.jar
```

<!--
我要记得装vim！！  
把下面的发到群里：  
命令行：
wget https://piston-data.mojang.com/v1/objects/45810d238246d90e811d896f87b14695b7fb6839/server.jar
不行的话手动下载：
https://www.minecraft.net/zh-hans/download/server
-->

---
layout: quote
---

4. 先运行并同意EULA。

```shell
java -jar server.jar # 等一会会自动退出
nano eula.txt #把true改成false
nano server.properties # 把online-mode这一项后面改成false(关闭验证)
```

5. 查看自己网卡接口地址，再次启动验证。

```shell
ip addr
java -Xms1024M -Xmx2048M -jar server.jar --nogui
```

此时应该能运行了。

<!-- 一个用nano另一个用vim -->

---
layout: quote
---

6. 使用tmux开启服务器

```shell
tmux
java -Xms1024M -Xmx2048M -jar server.jar --nogui
```

`ctrl + b``d`返回shell  
现在可以安全的关闭ssh连接了！(服务器版)

---
layout: quote
---

# 搭建个人博客

---
layout: quote
---

## 搭建环境

前面的课程知识点十分密集，专业术语也非常多，接下来来一些简洁直观的内容吧 ~~

博客是一个很好的展示自己，记录生活，分享知识的地方，我们身边很多大佬都有自己的博客，常阅读可以丰富我们的知识面。

比如橙子学长的博客 [萤火之光](https://alampy.com/) 就是一个相当高质量的博客。

我也有我自己的博客 [树 - 洞](https://blog.insmtr.cn/) 。

既然我们已经掌握了 Linux 的基本操作了，那么我们不妨尝试搭建一个属于自己的博客。

---
layout: quote
---

## 搭建环境

1. 市面上有许多成熟的开源博客框架，比如 Hexo、Jekyll、Hugo 等，我们以 Hexo 为例搭建一个简单的博客。

2. 安装 Node.js

```shell
$ sudo apt update
$ sudo apt install curl
$ curl -o- https://ghp.ci/https://github.com/nvm-sh/nvm/raw/refs/heads/master/install.sh | bash
$ source ~/.bashrc
$ nvm install 20
$ npm config set registry https://repo.nju.edu.cn/repository/npm/
```

3. 安装 Hexo

```shell
$ npm install -g hexo-cli
```

4. 初始化博客

```shell
$ hexo init blog
$ cd blog
$ npm install
```

---
layout: quote
---

## 搭建环境

5. 启动博客

```shell
$ hexo server
```

6. 在虚拟机浏览器内访问 `http://localhost:4000` 即可看到博客页面。  
   同样，在物理机浏览器内访问 `http://<虚拟机IP>:4000` 也可以看到博客页面。

---
layout: quote
---

## 切换主题

默认的主题还是太难看了，我们可以选择一个更好看的主题。

Hexo 主题是开放的，任何人都可以开发自己的主题，也可以使用别人开发的主题。

我们可以在 [Hexo 主题官网](https://hexo.io/themes/) 上找到适合自己的主题，比如 [hexo-theme-icarus](https://github.com/ppoffice/hexo-theme-icarus) 。

下载主题并应用：

```shell
$ npm install hexo-theme-icarus
$ hexo config theme icarus
$ hexo server
```

---
layout: quote
---

## 开始写作

1. 创建一篇新文章

```shell
$ hexo new "Hello World"
```

2. 编辑文章

在 `source/_posts` 目录下找到 `Hello World.md` 文件，编辑文章内容。

```shell
$ nano "source/_posts/Hello World.md"
```

3. 预览文章

```shell
$ hexo serve
```

你已经成功搭建了一个属于自己的博客！

---
layout: quote
---

## 编译

此时我们的博客还只是必须在命令行中输入命令启动，这样子服务效率低下。而且这些网页都是固定不变的，我们可以将博客编译成静态文件，然后上传到服务器上。

```shell
$ hexo generate
```

在 `public` 目录下生成了静态文件，我们可以将这些文件上传到服务器上，然后使用 Nginx 或 Apache 等 Web 服务器进行部署。

在本地，我们可以借助 Python 的 http.server 模块来预览编译后的博客。

```shell
$ cd public
$ python3 -m http.server 8080
```

在浏览器中访问 `http://localhost:8080` 即可预览编译后的博客。

事实上，国外有一些免费的静态网站托管服务，比如 GitHub Pages、Netlify、Cloudflare Pages 等，我们可以将编译后的博客上传到这些平台上，实现免费的博客托管。这样，我们就可以在全世界范围内访问我们的博客了。

---
layout: quote
---

## 参考资料

- [USTC Linux101](https://101.lug.ustc.edu.cn/)
- [进程和线程有什么区别？](https://www.zhihu.com/question/21535820/answer/411196449)
- [Systemd 入门教程：命令篇](https://ruanyifeng.com/blog/2016/03/systemd-tutorial-commands.html)
- [LINUX PID 1和SYSTEMD 专题](https://www.cnblogs.com/softidea/p/7219455.html)
- [教程:架设Java版服务器](https://zh.minecraft.wiki/w/Tutorial:%E6%9E%B6%E8%AE%BEJava%E7%89%88%E6%9C%8D%E5%8A%A1%E5%99%A8?variant=zh-cn#%E9%85%8D%E7%BD%AE%E7%8E%AF%E5%A2%83)

---
layout: end
---

Thanks!
