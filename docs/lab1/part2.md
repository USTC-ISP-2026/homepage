---
title: 章节二：初探Linux
weight: 12
---

> 考虑到很多同学在本次实验之前没有使用过Linux系统，因此提供此部分来速成Linux。

## 2.1 Ubuntu GUI的使用
打开虚拟机进入到Ubuntu之后，即可看到Ubuntu的GUI界面。默认左侧是Dock（类似Windows的任务栏），里面有若干内置软件。左下角是菜单（类似Windows的开始菜单）。考虑到部分同学首次接触Ubuntu，因此建议各位依次点击所有的软件、按钮，以进一步了解Ubuntu并熟悉其中的软件。

屏幕最右上角有几个图标，可以调整音量、网络设置、语言、输入法等，还可以关机。在菜单里找到“设置”，里面可以调整系统设置，如分辨率、壁纸等。Dock里有一个文件夹形状的图标，它是文件管理器，可以像windows一样图形化地浏览文件。

> 提示：如果在执行某个操作时报错文件/文件夹不存在，可以在UI界面内手动复制粘贴文件到目标位置（文件所有者为root用户的除外）。


<img src="/exp1-part1.assets/image-20250314204308347.png"  style="zoom: 25%;" />

## 2.2 终端（命令行）的使用

### 2.2.1 打开终端
在桌面/文件管理器的空白处右键即可出现“在终端打开”按钮，点击此处即可呼出终端进而在终端中执行相关的Linux指令。注意：终端也是有工作位置的。简单而言，在哪个目录下打开终端，命令就会在哪个目录下执行。终端当前目录一般称为“**工作目录**”。

> 举例：`ls`指令可以显示工作目录下的文件。在不同的目录下运行此指令的结果显然是不一样的。

终端会显示工作目录

### 2.2.2 目录与路径

Linux与Windows不同，Windows一般会分有多个逻辑磁盘，每个逻辑磁盘各有一棵目录树，但Linux只有一个目录树，磁盘可以作为一棵子树**挂载**到目录树的某个节点。在Linux操作系统中，整个目录树的根节点被称为**根目录**。每个用户拥有一个**主目录**，或称**家目录**，类似windows的`C:\Users\用户名`。从根目录看，除root用户之外，每个用户的家目录为`/home/用户名`。

Linux终端里使用的路径分为**绝对路径**和**相对路径**两种。绝对路径指从根目录算起的路径，相对路径指从工作目录算起的路径。其中，以根目录算起的绝对路径以`/`开头，以家目录算起的绝对路径以`~`开头，相对路径不需要`/`或`～`开头。

> 举例：一个名为ustc的用户在他的家目录下创建了一个名为os的目录，在os目录下面又创建了一个名为lab1的目录，则该lab1目录可以表示为：
> * `/home/ustc/os/lab1`
> * `~/os/lab1`
> * 如果工作目录在家目录：`os/lab1`
> * 如果工作目录在os目录：`lab1`

一些特殊的目录：
* `.`代表该目录自身。例：`cd .`代表原地跳转（`cd`是切换目录的指令）；
* `..`代表该目录的父目录。特别地，根目录的父目录也是自身。
* 在Linux里，文件名以`.`开头的文件是隐藏文件（或目录），如何显示它们请参考2.3.3.
  * `.`和`..`都是隐藏的目录。

> 举例：以下几个路径是等价的：
> * `/home/ustc/os/lab1`
> * `/home/ustc/os/././././././lab1`
> * `/home/ustc/os/../os/lab1`

### 2.2.3 运行指令

在终端中输入可执行文件的路径即可。终端所在路径是程序运行时的路径。需要注意的是，如果运行的二进制文件就在工作目录下，需要在文件名前加上`./`。
> 提示：与windows可执行文件扩展名为.exe不同，Linux中，可执行文件一般没有扩展名。例外：gcc在不指定输出文件名的情况下，编译出的可执行文件会带有.out的扩展名。但你也不需要管这个.out，直接运行也是一样的。


> 例：有一个可执行文件，其路径为`~/os/lab1/testprog`。它在运行时会读取一个相对路径为`a.txt`的文件。
> * 在家目录下运行：需要执行`os/lab1/testprog`（或`~/os/lab1/testprog`等绝对路径），程序读取的`a.txt`在家目录下；
> * 在`~/os/lab1`下运行：需要执行`./testprog`(当然你用绝对路径也无所谓)，程序读取的`a.txt`在`~/os/lab1`目录下。

相关问题：为什么在运行`sudo`、`man`等命令时，只需要输入指令名而不需要输入这些指令对应的二进制文件所在的路径？

一种情况是，这是因为这些指令所在的路径（一般是`/usr/bin`）被加入到了该用户的**环境变量**中。当终端读取到一个不带路径的命令之后，系统只会在环境变量中搜索，从而方便用户使用。当然，默认被加入环境变量里的路径一般只有一些系统路径，除非自行设置，家目录下面没有目录默认在环境变量中。如感兴趣，修改环境变量的方法可自行搜索了解。

另一种情况是，部分指令是Shell内建指令（如`cd`），它们的意义直接由Shell解释，没有对应的二进制文件。此情况可能会在下一实验中详细阐述。

### 2.2.4 指令及其参数

无论是Linux还是Windows，一条完整的命令都由命令及其参数构成。你们可以通过`man 指令名`来自行了解指令语法。一般来说，指令语法里带有'[ ]'的是可选参数，其他是必选参数。不同的参数的顺序一般是可以互换的。下面以`gcc`（一种编译器）为例，介绍实验文档描述指令的方式，以及如何按需构造一条指令。

本次要用到的`gcc`指令的一部分语法是：`gcc [-static] [-o outfile] infile` 。下面是各参数介绍：

| 参数 | 含义 |
| ---------- | ---------- |
| -static    | 静态编译选项（此处参数仅为示例，参数详细含义请自行上网搜索）。 |
| -o outfile | 指定输出的可执行文件的文件名为outfile。如果不指定，会输出为a.out。|
| infile     | 要编译的gcc文件名。注意绝对路径/相对路径的问题。|

* 如果我们编译test.c，不指定输出文件名，命令就只是`gcc test.c`；（这种情况下，gcc会自动命名输出文件为a.out） 
* 如果我们编译test.c，输出二进制文件名为test，命令就是`gcc -o test test.c`； 
  - 一般来说参数的顺序是无所谓的。所以使用`gcc test.c -o test`也一样能编译。
* 如果我们编译test.c，输出二进制文件名为test，且要使用静态编译，那么构造出的编译指令就是`gcc -static -o test test.c`。 

### 2.2.5 终端使用小技巧
* 按键盘的`↑↓`键可以切换到之前输入过的指令；
* 按键盘的`Tab`键可以自动补全。如果按一下Tab之后没反应，说明候选项太多。再按一下Tab可以显示所有候选项。
* 在shell里，`Ctrl+C`是终止不是复制。复制的快捷键是`Ctrl+Insert`或`Ctrl+Shift+C`，粘贴的快捷键是`Shift+Insert`或`Ctrl+Shift+V`。

## 2.3 Linux常用指令

> 注：一些指令的使用方法详见提供的链接。本部分涉及测验考察，测验方式见文档3.2。

### 2.3.1 man

英文缩写：manual

如果你不知道一条命令的含义，使用`man xxx`可以显示该命令的使用手册。

举例：`man ps` 可以显示`ps`指令的使用方法。按q退出手册。


### 2.3.2 sudo

**TL;DR：`sudo` = “以管理员模式运行”**。[A joke](https://mp.weixin.qq.com/s/JEIiLl-VXAkiahKZk8ECfw)

Linux采用**用户组**的概念实现访问控制，其中，只有root用户组才具备管理系统的权限。在`sudo`出现之前，一般用户管理linux系统的方式是，先用`su`指令切换到root用户，然后在root用户下进行操作。但是使用`su`的缺点之一在于必须要先告知root用户的密码，且这种控制方式不够精细。

为了方便操作，并更加精确地控制权限，`sudo`用来将root用户的部分权限让渡给普通用户。普通用户只需要输入自己的密码，确认“我是我自己”，就能执行自己拥有的那部分管理员权限。特别地，在Ubuntu下，普通用户默认可以通过`sudo`取得root用户的所有权限。若想精确地控制每个用户能用`sudo`干什么，可以参阅`visudo`。

如果在输入某个指令后，系统提示权限不够(Permission Denied)，那么在指令前加上`sudo`一般都能解决问题。
> 注意1：root用户具备很高的权限。一些需要管理员权限才能执行的指令（如，删除整个磁盘上的内容）会破坏系统。所以在使用`sudo`时，请务必确认输入的指令没问题。
> 
> 注意2：为了防止旁窥者获知密码长度，linux下输入密码不会在屏幕上给出诸如***的回显。输完密码敲回车就行了。

常见使用方法：`sudo command`

例：以普通用户运行`apt install vim`，会被告知没有权限，因为只有root用户（管理员）才有资格安装软件包。正确的用法是`sudo apt install vim`。

### 2.3.3 ls

英文全拼：list files

显示特定目录中的文件列表。常见的一部分语法是：`ls [-a] [name]` 。参数含义详见[Linux ls命令](https://www.runoob.com/linux/linux-comm-ls.html)。

### 2.3.4 cd

英文全拼：change directory

切换工作目录。常见的一部分语法是：`cd [name]` 。参数含义详见[Linux cd命令](https://www.runoob.com/linux/linux-comm-cd.html)。特别地，常见使用`cd -`来返回上次到达的目录。

### 2.3.5 pwd

英文全拼：print work directory

输出工作目录的绝对路径。常见的使用方法是：`pwd` 。

### 2.3.6 rm

英文全拼：remove

删除一个文件或目录。常见的一部分语法是：`rm [-rf] name` 。参数含义详见[Linux rm命令](https://www.runoob.com/linux/linux-comm-rm.html)。

### 2.3.7 mv

英文全拼：move

移动一个文件或目录，或重命名文件。常见的一部分语法是：`mv source dest` 。参数含义详见[Linux mv命令](https://www.runoob.com/linux/linux-comm-mv.html)。

### 2.3.8 cp

英文全拼：copy

复制一个文件或目录。常见的一部分语法是：`cp [-r] source dest` 。参数含义详见[Linux cp命令](https://www.runoob.com/linux/linux-comm-cp.html)。

### 2.3.9 mkdir

英文全拼：make directory

创建目录。常见的一部分语法是：`mkdir [-p] dirName` 。

参数含义详见[Linux mkdir命令](https://www.runoob.com/linux/linux-comm-mkdir.html)。

### 2.3.10 cat

英文全拼：concatenate

一般用于将文件内容输出到屏幕。常见的使用方法是：`cat fileName` 。

### 2.3.11 kill

用于将指定的信息发送给程序，通常使用此指令来结束进程。强制结束进程的信号编号是9，所以强制结束进程的使用方法是：`kill -9 [进程编号]`。

其他参数的使用方法可以参考[Linux kill命令](https://www.runoob.com/linux/linux-comm-kill.html)。

### 2.3.12 ps

英文全拼：process status

用于显示进程状态（任务管理器）。常见的使用方法：

- `ps`: 显示**当前用户**在当前终端控制下的进程。考虑到用户通常想看不止当前用户和当前终端下的进程，所以不加参数的用法并不常用。
- `ps aux`: 展示所有用户所有进程的详细信息。注意，a前面带一个横线是严格意义上不正确的使用方法。
- `ps -ef`: 也是展示所有用户所有进程的详细信息。就输出结果而言和`ps aux`无甚差别。

### 2.3.13 wget

wget是一个在命令行下下载文件的工具。常见的使用方法是：`wget [-O FILE] URL`。

例如，我们要下载 [https://git-scm.com/images/logo@2x.png](https://git-scm.com/images/logo@2x.png) 到本地。

1. 直接下载：`wget https://git-scm.com/images/logo@2x.png`，文件名是logo@2x.png。
2. 直接下载并重命名：`wget -O git.png https://git-scm.com/images/logo@2x.png`，文件名是git.png。注：-O中的O表示字母O而不是数字0。

注意，如果发现有同名文件，1所示方法会在文件名后面加上.1的后缀进行区分，而2所示方法会直接覆盖。

### 2.3.14 tar

用于压缩、解压压缩包。常见使用方法：

* 把某名为source的文件或目录压缩成名为out.tar.gz的gzip格式压缩文件：`tar zcvf out.tar.gz source`
* 解压缩某名为abc.tar.gz的gzip格式压缩文件：`tar zxvf abc.tar.gz`

其他使用方法详见[Linux tar命令](https://www.runoob.com/linux/linux-comm-tar.html)

### 2.3.15 包管理器（apt等）
在Linux下，如何安装软件包？每个Linux发行版都会自带一个**包管理器**，类似一个“软件管家”，专门下载免费软件。

不同Linux发行版附带的包管理器是不一样的。如，Debian用apt（Ubuntu是基于Debian的，所以也用apt），ArchLinux用Pacman，CentOS用yum，等等。

apt的常见用法：

- 安装xx包，yy包和zz包：`sudo apt install xx yy zz`
- 删除xx包，yy包和zz包：`sudo apt remove xx yy zz`
- 更新已安装的软件包：`sudo apt upgrade`
- 从软件源处检查系统中的软件包是否有更新：`sudo apt update`

其他使用方法参见[Linux apt命令](https://www.runoob.com/linux/linux-comm-apt.html)。

> 其他提示：
>
> - 如果报错`“无法获得锁 /var/lib/dpkg/lock.....”`，这是因为系统在同一时刻只能运行一个apt，请耐心等另一边安装/更新完。Ubuntu的软件包管理器也是一个apt。若你确信没有别的apt在运行，可能是因为没安装完包就关掉了终端或apt。请重启Linux或参考[此链接](https://blog.csdn.net/yaoduren/article/details/8561145)。
> - 如果报错`“下列软件包有未满足的依赖关系......”`或`没有可用的软件包.....，但是它被其他软件包引用了....`，可能是因为刚换了源，没等包刷新完就关闭窗口，请手动`sudo apt-get update`。

### 2.3.16 文字编辑器（vim、gedit等）

Linux系统中，常见的使用命令行的文字编辑器是vim。系统一般并**不**自带vim，需要使用包管理器安装。由于vim的使用方法与我们习惯的的GUI编辑方式有很大差异，所以在这里不详细介绍它的用法。若想了解请参考https://www.runoob.com/linux/linux-vim.html。

gedit是Ubuntu使用的Gnome桌面环境**自带**的一款文本编辑器，其使用方法与Windows的记事本(Notepad)大同小异。在这里也不多介绍。在终端里输入`gedit`回车即可启动gedit。编辑特定的文件的使用方法是`gedit 文件名`，若输入的文件名不存在，将自动创建新文件。在Ubuntu的图形化界面中直接双击文本文件也可以编辑文件。**但需要注意的是，在编辑一些需要root权限的文件时，直接双击文件不能编辑，只能在终端里`sudo gedit 文件名`。**

> 在命令行下启动gedit会在终端里报warning，忽略即可。[参考链接](https://askubuntu.com/questions/798935/set-document-metadata-failed-when-i-run-sudo-gedit)

如果想使用一些更高级的编辑器，可以考虑安装vscode。安装方法请自行到网上搜索。

比起在虚拟机内安装vscode，更推荐在主机上安装vscode并用ssh连接虚拟机，可以参考链接：[https://blog.csdn.net/m0_73500130/article/details/139669691](https://blog.csdn.net/m0_73500130/article/details/139669691)

如果想安装高级IDE的话（不推荐这么做），只能用CLion等。Visual Studio不支持Linux。

> 在后续的实验中，如果某步骤写着`vim xxx`，说明这是让你编辑某文件。编辑文件的方式不仅局限于vim，用gedit等也可。

### 2.3.17 编译指令（gcc、g++、make等）

我们在编译代码时，需要使用编译器。`gcc`和`g++`是常见的编译命令。其中:

- `gcc`会把`.c`文件当作C语言进行编译，把`.cpp`文件当作C++语言编译。
- `g++`会把`.c`和`.cpp`文件都当作C++语言编译。

考虑到复杂工程需要编译的文件数量会很多，此时每次都手输编译命令较为繁琐，为此GNU提供了一个make工具，可以按照一个编写好的Makefile文件来完成编译任务。我们将在实验一的第二部分给予重点介绍。

> 注意：
>
> 1. 这里的“C语言”是C90标准的C语言，**不能使用STL、类、引用、for内定义变量等C++特性**。
> 2. Linux内核是使用上面所述的C语言编写的，而不是C++。
> 3. 在编写代码时，请注意代码文件的扩展名命名，和编译指令的选择。
> 4. `gcc`和`g++`默认是不安装的。如果你想使用，请先使用包管理器安装`build-essential`包，里面包括`gcc`、`g++`等常见编译器，和make工具等。


简单地使用`gcc`/`g++`编译的语法是：`gcc [-o outfile] infile` 。下面是指令的各个参数介绍。如需了解更详尽的使用方法，可参考gcc官方手册：http://www.gnu.org/software/gcc/。

| 参数 | 含义 |
| ---------- | ---------- |
| -o outfile | 指定输出的可执行文件的文件名为outfile。如果不指定，会输出为a.out。 |
| infile     | 要编译的gcc文件名。注意绝对路径/相对路径的问题。|

使用`make`的方法是：在工程目录下直接运行`make`即可。一些Makefile会提供多种编译选项，如删除编译好的二进制文件(`make clean`)、编译不同的文件等。这时候需要根据Makefile来确定不同的编译选项。

### 2.3.18 gdb调试指令

gdb是一款命令行下常用的调试工具，可以用来打断点、显示变量的内存地址，以及内存地址中的数据等。使用方法是`gdb 可执行文件名`，即可在gdb下调试某二进制文件。

gdb需要使用包管理器来安装。（参考`2.3.15`）

一般在使用gcc等编译器编译程序的时候，编译器不会把调试信息放进可执行文件里，进而导致gdb知道某段内存里有内容，但并不知道这些内容是变量a还是变量b。或者，gdb知道运行了若干机器指令，但不知道这些机器指令对应哪些C语言代码。所以，在使用gdb时需要在编译时加入`-g`选项，如：`gcc -g -o test test.c`来将调试信息加入可执行文件。而Linux内核采取了另一种方式：它把符号表独立成了另一个文件，在调试的时候载入符号表文件就可以达到相同的效果。

* gdb里的常用命令

  ``` shell
  r/run                 				# 开始执行程序
  b/break <location>    				# 在location处添加断点，location可以是代码行数或函数名
  b/break <location> if <condition>   # 在location处添加断点，仅当condition条件满足才中断运行
  c/continue                          # 继续执行到下一个断点或程序结束
  n/next                				# 运行下一行代码，如果遇到函数调用直接跳到调用结束
  s/step                				# 运行下一行代码，如果遇到函数调用则进入函数内部逐行执行
  ni/nexti              				# 类似next，运行下一行汇编代码（一行c代码可能对应多行汇编代码）
  si/stepi              				# 类似step，运行下一行汇编代码
  l/list                  				# 显示当前行代码
  p/print <expression>   				# 查看表达式expression的值
  q                     				# 退出gdb
  ```

## 2.4 参考资料
* Linux命令大全：https://www.runoob.com/linux/linux-command-manual.html
* Shell教程：https://www.runoob.com/linux/linux-shell.html

<div STYLE="page-break-after: always;"></div>