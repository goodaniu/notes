# Arch Linux使用笔记

最近手又痒了，想玩下Linux开发环境。由于之前已经用过Ubuntu、Fedora之类的商业发行版，总感觉有各种各样的问题，所以这次想尝试下别的。Arch Linux听说不错，而且自由度比较高，所以就折腾下Arch Linux吧。

## 关于Arch Linux

### 安装

#### 修改软件源

Arch Linux默认会带许多软件源的配置，而且都是启用状态的，但是我们不需要那么多软件源，只需要保留最快的一个就可以了。所以，我们先禁用所有的软件源：

```sh
sed -i "s/^\b/#/g" /etc/pacman.d/mirrorlist
```
通过sed命令禁用所有的软件源后，我们再手动开启自己想要的软件源。

## Antergos

Antergos是基于Arch Linux的发行版，它沿用Arch Linux原有的软件源和配置，可以说相当于Arch的GUI LiveCD。我也是用Antergos的镜像安装Arch的。

### 更换LightDM

Antergos使用LightDM作为它的登录管理器，但是如果你使用Gnome Shell桌面的话，会发现你锁不了屏，所以我们要删除LightDM，重新安装GDM。

```sh
sudo systemctl disable lightdm # 取消LightDM的默认启动
sudo pacman -Rcns lightdm      # 删除LightDM
sudo pacman -Rcns xscreensaver light-lock # 删除LightDM锁屏相关软件包

sudo pacman -S gdm			   # 安装GDM
sudo systemctl -f enable gdm   # 默认启动GDM
sudo reboot                    # 重启
```

## Arch相关命令行指令

### Pacman

Pacman是Arch Linux的软件包管理工具，相对于Debian的aptitude或者RedHat的yum。
Pacman的命令有：

```sh
pacman -Syy # 同步远程软件源
pacman -S python # 安装软件,加-f参数强行安装，忽略文件冲突
pacman -Ss python # 搜索软件
pacman -Syu # 升级系统
```

### Yaourt

Yaourt是Arch Linux第三方软件包管理工具，在Pacman的基础上集成了AUR，也就是Arch Linux的第三方软件源的管理。
Yaourt的命令大部分与Pacman的不一样，主要命令有：

```sh
yaourt -Syua # 检查升级包括AUR软件包在内的所有系统软件
yaourt 软件包名称 # 搜索并安装软件包
```
### SystemCtl

SystemCtl是Systemd的CLI前端。

#### 启用与关闭

## 相关软件配置

### JDK

和其他Linux发行版一样，因为版权的关系，Arch Linux也是使用OpenJDK作为默认JDK。但是OpenJDK在Linux上有各种各样的问题，比如中文字符乱码等等，其中最让人不可接收的是OpenJDK的字体渲染问题，字体好丑……

在网上搜到的解决方案中，有两种是比较不错的，一个是Infinity Patch，一种是[TuxJDK](https://code.google.com/p/tuxjdk/)，两个其实都差不多。我选择第二种，在需要Java调用的软件中，设置独立的TuxJDK即可解决字体渲染问题（其实并不完美）。

### Intellij Idea

JetBrains的软件基本都是全平台通用的，没有什么特别的地方。唯一麻烦的是因为JetBrains的IDE都是用Java写的，所以也会遇到字体渲染问题。所以在运行的时候要指定下之前提到的TuxJDK：

```sh
#!/bin/sh

IDEA_HOME=/opt/idea
export JAVA_HOME=/usr/lib/jvm/jdk-8u5-tuxjdk-b08/
export _JAVA_OPTIONS="-Dawt.useSystemAAFontSettings=lcd \
                      -Dsun.java2d.xrender=true"
export GNOME_DESKTOP_SESSION_ID=this-is-deprecated
exec $IDEA_HOME/bin/idea.sh "$@"
```
另外，如果使用了TuxJDK的话，有可能软件界面显示会乱码，我试了很多种方法都不行，有可能只有自己手动编译JDK才会解决这个问题吧。所以为了不让软件界面显示中文，我们需要在idea的vmoptions上设置软件语言为英文：
```sh
-Duser.language=en
```

