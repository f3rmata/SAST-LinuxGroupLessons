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

> 对于一些运行时间长的进程，我们要怎么先把他们搁置，然后干一些别的事情呢？ 答案是`ctrl + z`!
> 按完之后，进程不见了啊？

运行`jobs`!  
此时你会看到显示为1号的进程，就是刚才挂起到后台的任务。  
如果要指定编号，记得加%，像这样`fg %1`  
在后台运行吧: `bg` 回到前台: `fg`

> 如果我想退出终端，但是程序继续运行呢？

- 在命令前面使用`nohup`。如果想直接让它在后台运行，在命令后面加上`&`。
- 当然，我们更推荐使用`tmux`(一个终端复用器)。

---
layout: quote
---

## tmux  

```shell
sudo apt install tmux # 安装tmux
tmux # 运行tmux
```

很好，现在我们已经创建并进入tmux的一个会话了！tmux会保留我们在之前shell所在的目录。接下来是一些常用的命令：

- `ctrl +b`: 这是tmux的修饰键，我们在tmux里面按下的快捷键都会发到tmux内的终端。按下这个键，我们可以告诉tmux：我要开始管理这个会话了。
  - d: 分离终端，回到原来的shell。
  - c: 新建窗口。
  - %: 左右拆分。
  - ": 上下拆分。
  - 方向键: 切换拆分后的窗口。
  - z: 放大当前的终端。

<!-- 一个终端（硬件概念）只有一套鼠标键盘，只能有一个 shell 主持一个 session，那如果我在 SSH 连接的时候有几个程序需要同时交互的话，只有一个前台进程很不方便。而且上面说过如果 SSH 网络断开，视为终端被关闭，也就意味着前后台一起收到 SIGHUP 一起退出，好不容易设置好的临时变量什么的还得重设。

开启多个 SSH 连接似乎可以解决这个问题。但是如果程序既需要交互，又想保证不因意外断线而停止程序，就是 nohup 也帮不了。

这时 tmux 的出现，解决了会话保持与窗口复用的问题。正如上图所示，tmux 是一个分屏的、运行在命令行的模拟终端，意味着只要有命令行可用，就可以将多个交互进程集成在在一个窗口上。该窗口不因断开连接或者暂时登出而消失，而是会保存在后台，下一次登录时可以立即还原。 -->

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
<!-- python演示SIGINT  
docker的停止来演示SIGKILL和SIGTERM -->

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

- `SysV Init` --> `Systemd`
- Unix 设计哲学—— “Do one thing and Do it well”

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
  - ·-u`: 后面跟上单元名
  - 当然可以用`--since`和`--until`来指定某个时间段的日志，具体的可以在man里面查看。

<!-- 此要管理服务，首先我们要清楚系统内有哪些服务。这里使用的是`less`翻页器~~支持j和k翻页，vim赛高~~  
如果作为服务启动，通常日志都不会给到标准输出上。 -->

---
layout: quote
---

## 创建一个自己的服务！

包括三个节：[Unit], [Service], [Install]
一个简单的示例：

```ini
[Unit]
Requires=network.target
Description=

[Service]
ExecStart=/usr/bin/<...>
ExecStop=kill -9 $MAINPID

[Install]
WantedBy=multi-user.target
```

后续我们会用一些服务来讲解~

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

存储在`/etc/passwd`和`/etc/groups`内，使用你喜欢的编辑器打开吧。  

<!-- 此处应该有图片，指出自己，系统用户和root，稍微讲讲x，uid -->

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

---
layout: quote
---

## 用户组的妙用

- 把用户加入admin/sudo组，避免了修改sudoers的麻烦。
- 把用户加入docker组，该用户就不需要sudo就可以使用docker命令。

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


<!-- 对于文件夹来说，rwx代表的含义有点不太一样，r是能否使用ls命令列出，w是修改内部的文件名，增加和删除文件，x是能够cd进入目录。 -->

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
- 第三位，第四位分别是所有者，所有组；分别用`chown``chgrp`修改，这两个后面都先跟着用户名/组名，然后再接着文件。

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

<!-- 我要记得装vim！！  
把下面的发到群里：  
https://mcversions.net/download/1.21.1  
wget https://piston-data.mojang.com/v1/objects/59353fb40c36d304f2035d51e7d6e6baa98dc05c/server.jar  
 -->

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

3. 从[mcversions](https://mcversions.net/download/1.21.1)下载服务端二进制文件。

```shell
mkdir server && cd server
wget https://piston-data.mojang.com/v1/objects/59353fb40c36d304f2035d51e7d6e6baa98dc05c/server.jar
```

---
layout: quote
---

4. 先运行并同意EULA。

```shell
java -jar server.jar # 等一会会自动退出
nano eula.txt #把true改成false
nano server.properties # 把online-mode这一项后面改成false(关闭验证)
```

<!-- 一个用nano另一个用vim -->

5. 查看自己网卡接口地址，再次启动验证。

```shell
ip addr
java -Xms1024M -Xmx2048M -jar server.jar --nogui
```

此时应该能运行了。

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
