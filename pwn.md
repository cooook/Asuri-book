###### 南航Asuri内部资料 绝密

# PWN是嘛玩意我该怎么入门

## 尽量简洁的简介

pwn写作 `屁 达不溜 恩`，读作 `胖`  
是指对一个程序输入特定的字符，使得程序执行了一些意料之外的操作，最终拿到系统的shell的过程  

> “拿到shell? shell是什么? 为什么要拿? 怎么拿?”  

这个同学问的非常好  
具体参考提问的智慧(bushi  
**关于shell**  
shell可以就理解为是一个机子的控制权限，或者操作权限  

比如在windows上输入`Win + R`再输入`cmd`后回车
会打开一个黑框子——控制台窗口

![1](.\pic\1.png)  
这个黑框就可以称为你Windows的shell  

linux同理`Ctrl + Alt +T`  
![2](.\pic\2.png)  
在这个黑框里，你可以输入指令来进行文件夹或文件的访问、重命名、删除，ip访问，软件安装，关机，rm -rf /*等等许多你能想到的能用键鼠实现的操作  


实际上用鼠标双击打开一个文件就是将"访问文件夹-访问文件-打开文件"这一系列指令集成进了`double click`这样一个快捷键里  


有时候这个黑框框能提供比键鼠权限更高的操作  


在做CTF的PWN题时，通常会在你目标机子上放一个名为`flag`或`flag.txt`的文件，当我们拿到shell后就可以访问它，得到解题的flag  

因为C/C++的库里面提供了有关系统操作的函数，控制程序执行以下函数之一都可以拿到shell：  
`system('/bin/sh')`  
`system('sh')`  
`system('$0')`  
`execve('/bin/sh',0,0)`  
应该不止这些(大概)，但再多的我也不知道，差不多够用就是了  

那么怎么强迫程序执行它们呢？这就是我们写PWN题儿时具体要做的事儿  
>那我电脑已经有shell了，为啥还要再拿一个shell?  

PWN要拿的不是自己的shell，而是远程其他主机的shell，具体见后面`题型`那一部分  

## 前置知识

* C语言  
C语言的数据类型，数组，选择语句，循环语句，函数，指针  
最起码给了一个反编译后的代码能看懂是干啥的  
C语言的教程我也不太了解，就贴个[菜鸟教程](https://www.runoob.com/cprogramming/c-tutorial.html)吧  
* 程序在内存中的加载与执行  
1.变量是怎么储存在内存中的  
2.数组的储存形式  
3.字符串的储存形式  
4.函数调用在内存中的实现  
5.函数参数的传递过程  
6.从被调用函数返回"调用者"在内存中的具体过程  
7.符号解析与静态链接、库文件与动态链接   
以上都是要会的  
教程当然也是有的[南大袁春风教授 计算机系统基础](https://www.icourse163.org/learn/NJU-1001625001?tid=1450235471#/learn/announce)  
建议在学会C语言函数之后开始学习，要细致地好好学  
* 汇编  
IA-32和x86-64指令系统大差不差，都要会一点   
pop,push,call,leave,ret这个几个是比较重要的，要很清楚  
汇编指令在上面袁春风教授的课里也会详细介绍  
* Linux基本指令  
PWN解题的过程大部分是在Linux系统上完成的，大部分PWN工具也是针对Linux系统开发的，所以了解一点Linux的指令也很必要  
这里介绍几个，更多的可以自己百度  
`ls`:显示文件夹下所有文件/文件夹  
`cd`:访问文件夹  
`cd ..`:返回上级文件夹  
`cd ...`:返回根目录  
`rm`:删除  
`cp`:复制  
`sudo`：sudo一般不单独作为命令，而是作为其他命令前缀，意为 以管理员身份执行

* 一点点python  
漏洞利用的过程基本都是通过写python脚本来实现的，这个脚本又被称为exploit，简称exp  
python同样还是给[菜鸟教程](https://www.runoob.com/python/python-tutorial.html)，计算机实验课上也会给教程让你们自学python，网址我不记得了  
另外，exp中会大量使用一个第三方库pwntools，这里给个例子你们应该就能懂了  
```
from pwn import *          #调用pwn库
sh=process('./easyrop')    #打开同文件夹下easyrop文件
sh=remote('209.288.0.12',12033)  #连接209.288.0.12:12033
sh.recvuntil('please input:\n')  #等待屏幕输出"please input\n"
payload='a'*20      
payload+=p32(0x8048a80) #payload添上0x8048a80在32位格式下转换成的字符串
sh.sendline(payload)    #向程序输入payload加回车
sh.send('ya mie die')   #向程序输入"ya mie die"不加回车
sh.sendlineafter('your name:','aaaa') #等待屏幕输出"your name"后向程序输入"aaaa"加回车
sex_1='male'
sex_2='female'
sex_3='CTFer'
sh.sendafter('your gender',sex_3)     #等待屏幕输出"your gender"后向程序输入sex_3不加回车
sh.recvuntil('ok,so your name is ')
my_name=u64(sh.recv(6).ljust(8,'\x00')) #将接下来屏幕输出的六个字符接收下来，调整长度后按64位格式从字符串转回数字
sh.interactive()                        #程序互动权转交给控制台
```
`payload+=p32(0x8048a80)`和`my_name=u64(sh.recv(6).ljust(8,'\x00'))`这个两个语句中p和u后面的数字是由程序是32位的还是64位的决定的  
所以一个exp里，要么只有`p32`和`u32`，要么就只有`p64`和`u64`，不会有`32`和`64`混合出现，例子里只是为了覆盖知识点才这么写的  

## 题型  

一般题目会给一个二进制文件的下载链接和一个地址    

二进制文件可以在Linux中直接运行，也可以扔进Windows的反编译器里分析    

连接到那个地址后，服务器会用一个一模一样的程序来响应，这时你屏幕上显示的东西也就会和自己运行那个程序的时候一模一样  

如果你的利用脚本可以通过这个程序拿到本地的shell，那么基本上在连接到目标地址后进行同样的操作就也能拿到对方机子的shell，这个时候输入Linxu命令就会被目标主机执行

## 环境

###### Linux  

Linux有许多花里胡哨的版本，这里咱们就用ubuntu 16.04就行  
切记16.04!!!
首先下载安装[VMware](https://www.jianshu.com/p/a979f489b47b)
然后上[镜像网站](https://releases.ubuntu.com/16.04/)下载ubuntu 16.04镜像  
速度太慢的话还有[其他源](https://blog.csdn.net/kwame211/article/details/83902851)可以下  
这里我就翻中科大的牌儿了  
![3](.\pic\3.png)  
下载好后打开VMware，创建新的虚拟机  
![4](.\pic\4.png)  
选择自定义  
![5](.\pic\5.png) 
直接下一步 
![6](.\pic\6.png)  
选择骚后安装操作系统，下一步
![7](.\pic\7.png)  
选择Linux,下面找到ubuntu64，下一步  
![8](.\pic\8.png)
然后给虚拟机取个名字，这里用句大实话代替，下面位置必须是已经存在的文件夹(建议别放C盘)，下一步  
![9](.\pic\9.png)
直接下一步
![10](.\pic\10.png)
内存看自己配置，16G电脑就给8G，8G电脑就给4G，下一步  
![11](.\pic\11.png)
直接下一步
![12](.\pic\12.png)
下一步
![13](.\pic\13.png)
下一步
![14](.\pic\14.png)
下一步
![15](.\pic\15.png)
选择储存为单个文件，下一步
![16](.\pic\16.png)
下一步
![17](.\pic\17.png)
选择自定义硬件
使用ISO镜像文件，把下载的ubuntu镜像位置填进去
![18](.\pic\18.png)
关闭，完成  
这样就有了一个虚拟机
![19](.\pic\19.png)
点击开启此虚拟机，等出现下面这个画面，选择中文，安装ubuntu
![20](.\pic\20.png)
直接继续
![21](.\pic\21.png)
选择现在安装
![22](.\pic\22.png)
选择继续
![23](.\pic\23.png)  
继续  
![24](.\pic\24.png)  
汉语，继续
![25](.\pic\25.png)
账户密码，继续
![26](.\pic\26.png)  
等它搞完，选择重启  
这样一个活生生的Linux就装好了  
不要升级！  
不要升级！  
不要升级！  
然后咱们把源换一下，不然下载东西很慢  
先进入设置-系统设置，找到软件和更新  
![27](.\pic\27.png)  
打开，找到`下载自`选项  
![28](.\pic\28.png)  
选择中国，或者在`中国`选项下面选一个看着顺眼的源  
![29](.\pic\29.png)
然后授权-关闭-重新载入  
接着要装一些库让你64位的Linux可以运行32位的程序  
`Ctrl + Alt + T`打开控制台  

```bash
sudo apt-get install gcc-multilib
```

安装完成后接着输入  

```bash
sudo apt-get install libc:i386
```

###### Windows  

Windows说实话其实没有太多需要配置的，nc签到题要是嫌麻烦不想开Linux写可以在Windows上装一个netcat，这样可以直接用控制台来nc  
也基本仅限于做nc签到题了，所以意义不太大  
[安装教程](https://www.cnblogs.com/kukudetent/p/11696500.html)  
使用方法，以[CTF秀](https://ctf.show/challenges)为例  
PWN第一题`PWN签到题`，直接先launch了这个instance  
![30](.\pic\30.png)  
看到一行`nc 111.231.70.44 28071`  
这个就是我们要连接的目标地址  
安装好netcat后直接复制进控制台  
![31](.\pic\31.png)  
flag就出来了  
![32](.\pic\32.png)

## 工具

### Wndows部分

IDA  
群文件里可以直接下载  
![33](.\pic\33.png)  
emmm还有三天到期？那你们猜猜这个文档是什么时候写的
IDA是一个功能很强大的反编译软件，使用方法如下  
假设我们已经下好了一个二进制文件  
![34](.\pic\34.png)  
双击直接打开，这时会询问打开方式，~~用我们熟悉的影音先锋打开它~~，直接拖到最后选择`在这台电脑上查找其他应用`
![35](.\pic\35.png)  
找到你IDA的安装目录，会看到有两个可执行文件，带64的是反编译64位文件的，不带64是反编译32位文件的  
至于怎么看这个文件是32位的还是64位的，可以拖进Linux里用`elf`命令查看  
但是两个exe都试一下看看哪个没报错其实也挺方便  
而且题目做多了以后你们就可以自己总结出在IDA打开文件时有哪些小细节能帮助你判断这是32位的还是64位的
![36](.\pic\36.png)  
打开文件时一路确定就行，然后一般都直接进main函数去分析它的主要功能
![37](.\pic\37.png)  
双击进入，可以看到main函数的整体流程和汇编代码，按`空格`可以切换到线性代码，按下`F5`能反汇编出伪代码  
![38](.\pic\38.png)  
多数变量和函数名都是IDA默认命名，可以右键重命名便于自己理解程序  
函数可以双击跟进去分析其功能，然后按`ESC`返回跟进前的函数  
![39](.\pic\39.png)  
双击变量可以查看他们在栈上的排列，就不贴图了  

### Linux部分

***以下工具的安装过程会涉及到安装或更新其他库(包括首先要按 "环境" 那一部分正确配置好你的宝贝Linux)，比如所有工具都需要提前安装并更新好pip，但是安装更新操作只需要执行一次，所以我只在安装pwntools的时候给出了该操作。如果跳过前几个工具的安装，先按我给的操作安装ROPgadget，那么基本上安装不了(但是可以自己根据报错来解决)。所以务必要按照本文给的顺序依次安装，指令不能少也不能错***

###### Python  

Python是干啥的应该不用我介绍吧  
ubuntu默认安装了python 2.7,`Ctrl + Alt + T`调出控制台，输入`python`，测试一下python是不是能正常使用  
![40](.\pic\40.png)

###### pwntools

安装：
在控制台输入

```bash
sudo apt install python-pip
```

这一步是安装pip的 

安装好后再输入

```bash
sudo pip install --upgrade pip
```

更新pip  
正式安装pwntools  

```bash
pip install pwntools
```

测试：  

控制台输入

```python
python
import pwn
```

如果这个时候没报错那么基本上是安装成功了 

如果不放心可以接着测试

输入

```bash
sudo apt install git
```

下载peda插件，编入.gdbinit

```bash
git clone https://github.com/longld/peda.git ~/peda
echo "source ~/peda/peda.py" >> ~/.gdbinit
```

测试：  
控制台输入  

```bash
gdb
```

如果显示了`gdb-peda$`说明安装成功

![42](.\pic\42.png)

###### checksec

安装

```bash
git clone https://github.com/slimm609/checksec.sh.git
cd checksec.sh
sudo ln -s checksec /usr/local/bin/checksec
```

测试：

```
checksec
```

 ![43](.\pic\43.png)

###### ROPgadget

先更新一下apt

```
sudo apt-get update
sudo apt-get upgrade
```

  正式安装  

```
sudo apt-get install python-capstone
git clone https://github.com/JonathanSalwan/ROPgadget.git
cd ROPgadget
sudo python setup.py install
```
![44](.\pic\44.png)
报错是因为我没加参数，如果报的错不一样，那么八成是安装失败了  
checksec也是

###### ropper

```bash
pip install setuptools --upgrade
pip install filebytes
pip install keystone-engine
pip install ropper
```

![45](.\pic\45.png)

话说我为了写这个入门手册，自己又配好了一个环境，要不要干脆就打包发出来呢  
还是算了，~~我去年装虚拟机经历的痛苦，你们一丝都不能少~~  
这可都是为你们好，多熟悉熟悉linux操作的姿势，可养发了  

## 一道例题  

没有什么高深的技术(高深的我也不会呀)，主要是体验pwn解题的过程  

咱们靶场还没建好，题目就都在这里手动给了  

[二进制文件](https://pan.baidu.com/s/1lcCNsK_lTYq0_VaP48x3Sg)  提取码：gjbt  
nc 106.15.120.40 8009 

 (已失效)

直接拖入IDA64打开  

找到main，F5反编译  
![46](.\pic\46.png)  
看到通过scanf可以往cmmand这个变量里输入8个字符  

在程序的最后会调用system执行cmmand  

上面讲过，如果能执行system("/bin/sh")就能拿到shell  

所以我们要想办法让command储存的就是"/bin/sh"这个字符串  

但是程序还会对command进行一些处理，它会遍历command  

然后和36,48,115,104这些进行比较  

这些数字在ASCII码表里各对应着一个字符  

实际上就是在和这些字符比较  

点击36，按`R`可以将整数转换为字符  
![47](.\pic\47.png)  
意思是说  

如果发现cmmand里面有'$'并且下一个是'0'  

或者发现's'并且下一个是'h'  

(其实就是说如果有'$0'或者'sh')  

那么就会进入if里面的操作  

在if里会把'sh'或者'$0'删掉  

比如  

如果我们输入"/bin/sh"  

最后会执行system("/bin/")  

如果输入"sh"  

最后会执行system("")(字符串是空的)  

如果输入"sh123"  

最后会执行system("12123")  

这些都是无效指令，肯定都是拿不到shell的  

那么该怎么让处理过的command的里面还有'sh'呢？

想一想，如果输入的是"shsh"或者"sshh"  

"shsh"的话，首先程序会发现第一个'sh'  

于是会把后两个字符覆盖过来  

但是后两个依然是'sh'，所以前两个字符并不会变  

然后程序会继续向下遍历，找到第二个'sh'  

此时再往后两个字符都是空字符  

第二个'sh'就会被覆盖为空  

commad就变成了'sh'  

"sshh"的话，程序只会发现中间有个'sh'  

于是把后面的'h'和一个空字符覆盖过来  

而这个空字符是作为字符串结束的标志，所以程序会认为commad这个字符串到第一个'h'后就结束了  

commad也会变成'sh'  

这样，最后就执行了system('sh')，也就拿到shell了  
![48](.\pic\48.png)  
可以看到在haoshufu这个程序里出现了控制台标志$，并且可以对输入的指令进行响应  
![49](.\pic\49.png)  
那咱们连上远端，实战一下  

```
Win + R
cmd
```
开启控制台  

```
nc 106.15.120.40 8009
```


![50](.\pic\50.png)

```
shsh
```
![51](.\pic\51.png)

```
ls   查看目录
```

![52](.\pic\52.png)

```
cat flag   打印flag文件内容
```

![53](.\pic\53.png)
flag就是`flag{biubiubiubiubiubiubiubiu}`

##### ** 其他解法

有大师傅给了不一样的思路

对于比赛中的pwn题来说，其实我们最终需要的只是flag文件里的字符串，拿到shell可以很方便的读出这个字符串，然而就算不拿shell我们还是有办法将文件泄露出来，比如orw，可以自行百度

对于这题，给了我们控制system函数参数的权利，其实也就相当于给了shell

我们可以直接执行system("cat flag")来读取flag

但是scanf读取到空格就会结束，所以还需要绕过一下

payload：

```
cat<flag
cat$IFS*
od<flag
nl<flag
pg<flag
dd<flag
……
```

原理就不解释了，可以自己查有关这些指令和符号的介绍

最上面两个不易成功，自己想想为什么