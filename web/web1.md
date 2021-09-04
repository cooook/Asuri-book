# 工具安装篇

该篇只挑选一些常见的基础工具安装，其他的都是同理

建议根据自己的习惯，创建专门的文件夹，做好合理的命名和分类

Chrome和Firefox浏览器安装就不做教程了

此篇只是做一个引导，如果觉得不够清楚或是和你的工具版本、电脑环境等不匹配，可以另搜教程，这里并不对版本等做任何强制要求，能用即可。

## BurpSuite

### Java8配置

win10环境：

以java8版本的为例，思路是一样的

配置java环境，下载`jdk`和`jre`

https://www.oracle.com/java/technologies/javase/javase-jdk8-downloads.html

在`Java SE Development Kit 8u301`处根据电脑选择合适的文件下载即可

如果不确定，可以右键`我的电脑/此电脑`在属性中查看系统信息

下载过程中，需要注意下载路径，可以根据电脑的情况自行更改

![jdk.png](https://i.loli.net/2021/09/04/jruf6E1eHV327JQ.png)

环境配置（以我的电脑为例）：

右键此电脑-属性-高级系统设置-环境变量

![huanjing.png](https://i.loli.net/2021/09/04/kFfZGPQCX8hIer9.png)



以我的配置为例

![huanjing2.png](https://i.loli.net/2021/09/04/Elx5tWUNPoXiGb1.png)

编辑系统变量

![huanjing3.png](https://i.loli.net/2021/09/04/cYoglv29VWA5bCI.png)

编辑用户变量-Path-编辑-新建

![huanjing4.png](https://i.loli.net/2021/09/04/EYaeJXmOop1vKAR.png)

验证是否安装成功：

按下键盘上的`win+r-`>输入cmd

或是在左下`在这里输入你要搜索的内容`处输入cmd，选择`命令提示符`

输入：

```
java
javac
```

![huanjing5.png](https://i.loli.net/2021/09/04/i2zNrmckgnb5vhA.png)

### 安装Burp

下载好`绿色的burp文件`后

可以点击`burp-loader-keygen-2.jar`进行启动

若无法通过点击启动，可以右键打开命令行，键入以下命令可以启动

```
javaw -jar burp-loader-keygen-2.jar
```

### 配置浏览器代理

根据图中找到这个位置，可以看到监听的是127.0.0.1的8080端口，这里之后可以按需配置

![buro.png](https://i.loli.net/2021/09/04/egn5azhP7X3ANFQ.png)

以Firefox为例

![firefox.png](https://i.loli.net/2021/09/04/VieIB4WvYPojr13.png)

![firefox2.png](https://i.loli.net/2021/09/04/IZoKLuMPwqG3SkU.png)

可以使用Proxy Switcher插件进行代理的切换和快速的配置

![proxy.png](https://i.loli.net/2021/09/04/mtcAUCrRJ9Xxdhf.png)

## Hackbar插件

需配合浏览器使用，根据下载的版本而定

如Firefox中：附加组件-点击小齿轮-从文件安装附加组件

## 搭建本地php环境

### 下载phpstudy

![phpstudy.png](https://i.loli.net/2021/09/04/XpQ8MBy7OE5zDcd.png)

一般可以打开一个Apache+mysql，点击左侧网站和数据库可以开始配置相关信息

### 测试php环境

在phpstudy的路径下的WWW文件夹中，创建`test.php`，键入：

```php
<? phpinfo(); ?>
```

![test.png](https://i.loli.net/2021/09/04/I3RXuaYZJ4QlOxg.png)

## 后话

做好这些准备之后，就可以尝试做一些基本的ctf题目了

另外，如果在安装过程中对其中的原理多加学习和了解，效果会事半功倍，并且有助于以后解决更为复杂的工具安装和环境配置问题。