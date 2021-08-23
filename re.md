# Reverse

## 什么是逆向？

软件代码逆向主要指对软件的结构，流程，算法，代码等进行逆向拆解和分析。

## 我们为什么要学习逆向？

受限于某些订阅制软件高昂的价格（如 Adobe Creative Cloud 中国摄影计划的[一年订阅价格是￥888](https://www.adobe.com/cn/creativecloud/buying-plans.html)，而逆向工程者常用的 IDA Pro 全插件的[买断价则高达$10000](https://hex-rays.com/cgi-bin/quote.cgi/products)），使用破解软件成了很多人的首选。有些时候我们不需要破解程序的全部功能，而是只需要修改一部分内容（例如游戏的[修改器](https://www.cheatengine.org/)）。通过某些方式修改程序功能达到我们需要的目的这一过程也是逆向。

## 如何开始逆向？

学习逆向最好了解编程：编程的过程是将人能够理解的高级语言交给编译器/解释器，输出被机器所理解的二进制程序；而逆向，顾名思义就是反向操作：假设我们有一个二进制程序，但是没有源码，从二进制反推原程序逻辑的过程就是逆向。

在逆向之前，我们需要知道在计算机的眼里程序是长什么样子的。

{% hint style="info" %}

**推荐使用 WSL2**

接下来的大部分操作都在 Linux 上进行。

如果大家对安装一个虚拟机感到恐慌的话（首先，你要安装 VMWare/VirtualBox；接下来你还要安装一个操作系统，真是让人感到头大！），不如试一试 Windows10 上的 WSL2，它非常适合初学者入门。WSL2 让 Windows 成为了“最好用的 Linux 发行版”之一。安装方式可以参考[官方网站](https://docs.microsoft.com/en-us/windows/wsl/install-win10)。如果大家感觉阅读英文较为吃力的话，也可以自行在往上搜索中文教程。安装完基础设施之后，我们就可以像安装手机应用程序那样安装各种 Linux 发行版，如 [Ubuntu 20.04 LTS](https://www.microsoft.com/store/productId/9N6SVWS3RX71)！

在配置好 WSL2 之后，推荐大家安装 [Windows Terminal](https://www.microsoft.com/store/productId/9N0DX20HK701) 和 [VSCode](https://code.visualstudio.com/)，它可以为我们的编程带来很多方便。

如果你已经按照我上面的介绍安装了 Windows Terminal 和 VSCode，那么就可以开始 Linux 之旅了！你首先需要了解一些 [Linux 的基础操作](https://linuxtools-rst.readthedocs.io/zh_CN/latest/index.html)。注意，在 WSL2 中，C 盘被挂载在 `/mnt/c` 下，D 盘也与之类似。

- 在 Terminal 中输入 `code .`（注意最后一个点）可以在当前目录下打开 VSCode。

- 在文件资源管理器中输入 `\\wsl$` 即可像访问 Windows 中文件那样访问 WSL2 下的文件。

- 请学会使用 [apt 的用法](https://www.linuxidc.com/Linux/2019-08/160197.htm)，[切换源为国内源](http://mirrors.ustc.edu.cn/help/ubuntu.html)（如果你对网络有自信的话，这一步可以省略），将系统更新之后安装一些常用工具：

  ```bash
  $ sudo apt install build-essential \
    man gdb git
  ```


{% endhint %}

以最简单的“Hello, world!”为例。相信大家已经学会用 VC (Visual Studio) 或者 gcc/clang 编译一个最简单的程序了。将以下代码保存进 `main.cpp` 中：

```c
#include <stdio.h>
int main()
{
    printf("Hello, world!\n");
    return 0;
}
```

我们使用命令编译：

``` bash
# 在 Visual Studio 中，我们可以使用图形化的选项代替这一过程，
# 但这并不利于逆向的学习。
# 本文及之后的内容都会在 Linux 上运行。

# gcc
$ gcc -o main main.cpp

# clang
$ clang -o main main.cpp
```

如果你按照我上面的介绍用 VSCode 进行了全部操作，它看起来应该是这个样子的：

![](https://pan.qrzbing.cn/img/re/VSCode.png?raw)

这样我们就得到了一个可执行的 `main` 文件。在这个过程中，编译器将我们可以理解的程序代码转换成为了机器可以理解的二进制文件。如果我们用文本文档打开这个可执行文件，只会得到一串乱码。不过计算机中的所有内容都是由人定义的，虽然在我们看来它是一个[黑箱](https://en.wikipedia.org/wiki/Black_box)，但是只要我们了解了人们对程序的定义，那么所有的迷雾都会烟消云散。在打开文件之前，我们需要知道一些更方便的操作。

{% hint style="info" %}

**快速进入到当前环境下的 WSL2 路径**

在上图中的左上角，对目录或者文件点击右键，单击“Reveal in Explorer”即可快速用文件资源管理器打开当前路径。

<img src="https://pan.qrzbing.cn/img/re/reveal_in_explorer.png?raw" style="zoom:30%;" />

{% endhint %}

{% hint style="info" %}

**更好地使用 IDA Pro**

在下载 IDA Pro 并解压之后，请让它的路径中不要包含任何中文字符。在分析程序时，不要让被分析的路径包含空格。

为了方便使用 IDA 打开程序，我们可以将 IDA 添加进右键菜单。将以下文件保存进 `ida.reg`：

``` reg
Windows Registry Editor Version 5.00

[HKEY_CLASSES_ROOT\*\shell\IDAx86]
@="Open with IDA x86"
"Icon"="<INSTALL_PATH>\\ida.exe"

[HKEY_CLASSES_ROOT\*\shell\IDAx86\command]
@="<INSTALL_PATH>\\ida.exe %1"

[HKEY_CLASSES_ROOT\*\shell\IDAx64]
@="Open with IDA x64"
"Icon"="<INSTALL_PATH>\\ida64.exe"

[HKEY_CLASSES_ROOT\*\shell\IDAx64\command]
@="<INSTALL_PATH>\\ida64.exe %1"
```

请将 `<INSTALL_PATH>` 替换为你安装的 IDA Pro 的路径。双击添加进注册表后，右键需要分析的文件，将会显示 “Open with IDA x86” 和 “Open with IDA x64”。

{% endhint %}

{% hint style="info" %}

**可执行程序的格式**

在 Linux 中，可执行文件的格式被称为 ELF（可执行与可链接格式，Executable and Linkable Format），而 Windows 下的可执行文件格式则被称为 PE（可移植可执行，Portable Executable）。除此之外，可执行文件也有位数的区别，一般情况下，我们编译的程序是 64 位的，不过也与我们使用的编译参数有关。如果你发现无法用 ida64 打开，不妨试试直接使用 ida 打开。有关可执行程序的更多内容，未来会有更详细的讲解。

{% endhint %}

我们用 IDA Pro 打开这个可执行文件。

<img src="https://pan.qrzbing.cn/img/re/IDA_1.png?raw" style="zoom:33%;" />

我们用默认设置进入即可。在进行一连串的分析之后，我们进入了 IDA Pro 的主页面：

![](https://pan.qrzbing.cn/img/re/IDA_2.png?raw)

看上去很复杂，但是**不要恐慌**！在这个最大的代码块中单击一下 F5，你就会惊喜地发现我们熟悉的代码又回来了：

![](https://pan.qrzbing.cn/img/re/IDA_3.png?raw)

这就是 IDA Pro 最好用也是最强大的功能之一——[反编译](https://www.zhihu.com/question/49843549)，它将机器代码转换成我们可以理解的伪 C 语言，为什么说它是“伪 C 语言”呢？因为在它最终得到的代码中有很多参数仍然是不确定的，需要人工分析。

回到刚才的代码块，你可以按 Tab 键恢复，或者在代码块中按空格键得到顺序的指令。注意：Tab 键得到的代码块和空格得到的代码序列是可以相互转换的。顺序指令如下所示。注意，左边的数字代表指令所存放的相对位置。

![](https://pan.qrzbing.cn/img/re/IDA_4.png?raw)

代码块代表一个[基本块](https://en.wikipedia.org/wiki/Basic_block)，我们先不必了解它的定义。在这里，基本块包含如下指令：

```assembly
push    rbp
mov     rbp, rsp
lea     rdi, s          ; "Hello, world!"
call    _puts
mov     eax, 0
pop     rbp
retn
```

上面的这几条指令属于[汇编语言](https://www.ruanyifeng.com/blog/2018/01/assembly-language-primer.html)，在最开始，我们不必了解它的具体内容。这些汇编语言有什么作用呢？在介绍它们之前，请让我为大家介绍计算机的本质。

### 计算机的本质：不停计算的机器

计算机的结构很简单：

![](https://pan.qrzbing.cn/img/re/NFA.png?raw)

人们通过键盘、鼠标、麦克风等方式输入，计算机捕获这些输入并根据代码执行相应的操作，最终通过显示器、音箱等方式输出，如此反复。

计算机捕获输入并输出的过程一样适用于我们写的程序，不过我们的程序中没有输入，只有输出。可执行文件执行的过程，就是让计算机获取指令并输出的过程。

在计算机中有一个专门指向当前指令的程序计数器（Program Counter, PC），它存放了下一条指令所在的地址。每次计算机执行指令都需要从 PC 中取出指令，执行指令，更新 PC。那么稍微抽象一下，用伪代码表示计算机的工作方式就是

```
while (true) {
	通过 PC 获取指令
	执行指令
	更新 PC
}
```

所有的操作都可以总结为这三种操作中的一种。回到上面的代码。

``` assembly
.text:000000000000063A                 push    rbp
.text:000000000000063B                 mov     rbp, rsp
.text:000000000000063E                 lea     rdi, s          ; "Hello, world!"
.text:0000000000000645                 call    _puts
.text:000000000000064A                 mov     eax, 0
.text:000000000000064F                 pop     rbp
.text:0000000000000650                 retn
```

最开始的 `push rbp` 和 `mov rbp, rsp` 和最后的 `pop rbp` 以及 `ret` 我们可以不考虑，核心代码是中间的那三行。

首先，`lea rdi, s` 将字符串 s 的地址放入寄存器 rdi 中，在这个过程中，计算机会：

- 通过 PC 获取指令：这条指令位于内存中的某一个地址 0x63A，计算机会加载该地址然后解析这个地址上的命令；
- 执行指令：人们定义 lea 汇编指令为将一个内存地址赋值给一个操作数，这里将 s 的地址赋值给 rdi；
- 更新 PC：PC 的值更新为 0x645。

如果我们双击 `s`，会跳转到 s 所存放的位置：

```assembly
.rodata:00000000000006E4 ; const char s[]
.rodata:00000000000006E4 s               db 'Hello, world!',0    ; DATA XREF: main+4↑o
.rodata:00000000000006E4 _rodata         ends
```

可以看到，`s` 被保存在了 .rodata 节中。在未来我会介绍可执行文件中一些基础的格式。这里我们只需要知道数据都会被存放在 data 段就可以了。

按 Esc 返回到 .text 节，接下来会 `call _puts`，根据人们对 `call` 指令的定义，它会调用 put 函数——但是我们的代码中明明写的是 `printf` 啊？这是因为编译器对代码进行了[优化](https://www.zhihu.com/question/31941203)，它可以让同样的代码运行得更加迅速。

这个函数的参数则保存在 rdi 中，返回值保存在 eax 中，是根据 [x64 调用约定](https://docs.microsoft.com/zh-cn/cpp/build/x64-calling-convention?view=msvc-160)规范的。这样，我们就理解了这个程序的逻辑：它将一个字符串 `Hello, world!` 放如 `put` 的参数中，然后输出。这样我们就完成了一个程序的逆向，是不是很简单呢？😁

接下来，同学们可以将逆向和学习 C 的过程结合，每学一个 C 的知识点就可以实验编程，然后用 IDA Pro 逆向，看看高级语言在可执行文件中的表现形式，希望大家玩的开心~
