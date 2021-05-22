
proot是chroot及mount --bind的用户空间实现，一个简单的容器应用。

termux的proot包内还包含了一个名为termux-chroot的bashscript，可在Termux内模拟linux目录结构。


### Atilo

Atilo是一个All-in-one型的Linux安装脚本。

主页：https://github.com/YadominJinta/atilo

```
atilo [命令] [参数]
Atilo 是一个用来帮助你在termux上安装不同的GNU/Linux发行版的bash脚本。

命令:
list             列出可用的和已安装的发行版
remove           移除已安装的发行版
install          安装发行版
help             帮助
作者 @YadominJinta @seashell11234455
本atilo具有超级牛力
```
### proot-distro

```
proot-distro/stable 1.7.0-1 all
  Termux official utility for managing proot'ed Linux distributions
```
官方的工具组

### Rootfs

这个概念很难解释，像`Unix`一样可意会不可言传。就把它当成iso镜像那种系统安装文件吧。

当你想安装一个Linux发行版时，你应该:

1.建立一个空目录

2.将对应你的设备架构的发行版Rootfs下载进去。

3.解压

4.将该目录当作proot容器的根目录，编写启动脚本。

我制作了一份Rootfs索引，应该会对你有帮助。

```shell
{
parrot_armhf=https://raw.githubusercontent.com/EXALAB/AnLinux-Resources/master/Rootfs/Parrot/armhf/parrot-rootfs-armhf.tar.gz
}

{
parrot_arm64=https://raw.githubusercontent.com/EXALAB/AnLinux-Resources/master/Rootfs/Parrot/arm64/parrot-rootfs-arm64.tar.gz
}

{
kali_armhf=https://raw.githubusercontent.com/EXALAB/AnLinux-Resources/master/Rootfs/Kali/armhf/kali-rootfs-armhf.tar.gz
}

{
kali_arm64=https://raw.githubusercontent.com/EXALAB/AnLinux-Resources/master/Rootfs/Kali/arm64/kali-rootfs-arm64.tar.gz
}

{
ubuntu_armhf=https://raw.githubusercontent.com/EXALAB/AnLinux-Resources/blob/master/Rootfs/Ubuntu/armhf/ubuntu-rootfs-armhf.tar.gz
}

{
ubuntu_arm64=https://raw.githubusercontent.com/EXALAB/AnLinux-Resources/master/Rootfs/Ubuntu/arm64/ubuntu-rootfs-arm64.tar.gz
}

{
debian_armhf=https://raw.githubusercontent.com/EXALAB/AnLinux-Resources/master/Rootfs/Debian/armhf/debian-rootfs-armhf.tar.gz
}

{
debian_arm64=https://raw.githubusercontent.com/EXALAB/AnLinux-Resources/master/Rootfs/Debian/arm64/debian-rootfs-arm64.tar.gz
}

{
arch_armhf=https://mirrors.tuna.tsinghua.edu.cn/archlinuxarm/os/ArchLinuxARM-armv7-latest.tar.gz
}

{
arch_arm64=https://mirrors.tuna.tsinghua.edu.cn/archlinuxarm/os/ArchLinuxARM-aarch64-latest.tar.gz
}

{
fedora_armhf=https://mirrors.tuna.tsinghua.edu.cn/fedora/releases/28/Container/armhfp/images/Fedora-Container-Minimal-Base-28-1.1.armhfp.tar.xz
}

{
fedora_arm64=https://mirrors.tuna.tsinghua.edu.cn/fedora/releases/28/Container/aarch64/images/Fedora-Container-Base-28-1.1.aarch64.tar.xz
}

{
gentoo_armhf=http://distfiles.gentoo.org/releases/arm/autobuilds/20161129/stage3-armv7a-20161129.tar.bz2
}

{
alpine_arm64=http://dl-cdn.alpinelinux.org/alpine/v3.8/releases/aarch64/alpine-minirootfs-3.8.1-aarch64.tar.gz
}
```

###  编写Proot启动脚本

`unset LD_PRELOAD`

```shell
unset LD_PRELOAD
#为当前shell消除LD_PRELOAD这个变量
```


~~如果你安装了`termux-exec`，则这一步是必须的，如果不进行这一步则proot必报错。~~  
*此问题已在[#2163](https://github.com/termux/termux-packages/pull/2163)中修复*

`-r`

指定proot容器的根目录，参数为目录名。

例子:我使用Alpine这个轻量级发行版，根目录为

```shell
~/TermuxAlpine
```

所以我的proot启动脚本中有这样一行

```shell
-r /data/data/com.termux/files/home/TermuxAlpine/
```

`-b`

将真实系统中的目录映射到容器中。

```shell
#将Android内核产生的数个特殊目录映射到容器中
-b /dev/ -b /sys/ -b /proc/
#另一种方法
-b /dev:/dev
```
其余目录可自行选择，常有人将Termux的Home目录映射到容器中，以便于在Termux与容器间共享dot文件。

`--link2symlink`

用符号链接替换硬链接。

`-0`

使当前用户显示为“root”并伪造其权限。

编译必备。

注:然而termux访问不了的系统资源在proot容器中一样访问不了。就是说，不要想着开80端口这种事了，不可能的。子进程上了当，内核可是一清二楚。

`-w`

设定初始工作目录。

```
-w /home
```

`-k`

虚拟内核版本号。

```
-k 100
```

`-i`

用`-i uid:gid`来指定自己启动一个proot容器时的登录用户。

`-S`

等效于`-0 -r`。

[$]启动shell

```shell
/usr/bin/env -i
#使用env这个程序来设定环境变量与启动shell
HOME=/home
#设定Home目录
TERM="xterm-256color"
#设定终端样式
LANG=zh_CN.UTF-8
#语言与文本编码
PATH=/bin:/usr/bin:/sbin:/usr/sbin
#指定可执行文件所在目录
/bin/busybox sh --login
#启动登录shell，此处为busybox sh。
#如果你用bash
/bin/bash -l
#zsh用户
/usr/bin/zsh -l
```

最终集成到脚本中，是这样

```shell
#unset LD_PRELOAD
exec proot --link2symlink \
-0 -r /data/data/com.termux/files/home/TermuxAlpine/ \
-b /dev/ -b /sys/ -b /proc/ -b /sdcard -w /home /usr/bin/env -i HOME=/home TERM="xterm-256color" LANG=zh_CN.UTF-8 PATH=/bin:/usr/bin:/sbin:/usr/sbin /bin/busybox sh --login
#实际的脚本中最好使用exec而非直接启动proot
#原因与shell工作机制有关
```
问题1:当proot启动脚本中的shell与/etc/passwd相冲突时，听谁的?

proot是老大，听他的。

问2：proot会启动linux发行版的init吗？

不会。

问3：systemd在Android上可用吗？

由于Android的内核魔改过，所以不行。

问4：为什么ps无法正常使用？

高版本Android开了进程隔离，Termux的ps魔改过才能用。



**启动之后**

别急着高兴，还有些问题没有被解决。

+ `/etc/resolv.conf`
 
```shell
#若此文件为空，则
rm /etc/resolv.conf
echo "nameserver 8.8.8.8" >> /etc/resolv.conf
```

**AUR**

Arch社区的AUR在Android上暂时无法使用，因为Android不支持IPC，编译时的帮助工具fakeroot无法运行。~当然刷内核问题可解。~

但是，Termux的issues区有人提出，fakeroot可以无需IPC运行，目前fakeroot包已添加到官方仓库，所以，这事怎么说呢……

最后我找到了这个：[arch-fakeroot-tcp](https://github.com/yuk7/arch-fakeroot-tcp)


此仓库的简介：_ArchLinux fakeroot-tcp package。_

### Trick

如果启动失败，把这些东西加到proot脚本里，放在proot命令上面就行。

```shell
export PROOT_NO_SECCOMP=1
```

### See also

https://github.com/Cool-Pan/oh-my-TermuxArch/blob/master/docs/README-CN.md


## 感谢

YadominJinta@github.com

Hax4us@github.com
