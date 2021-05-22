### $HOME

```
$ echo $HOME
------------------------------
/data/data/com.termux/files/home
-------------------------------
```

### $PREFIX

此处存放Termux的prefix-rootfs,简而言之:

```bash
tree -d -L 1 $PREFIX
-----------------------------------
/data/data/com.termux/files/usr/
├── bin
├── etc
├── include
├── lib
├── libexec
├── share
├── src
├── tmp
└── var

9 directories
-----------------------------------
```
### termux-tools


```
pkg f termux-tools
#尝试一下这个命令，查看有哪些小工具
```
如你所见，这是一个termux小工具集合。

```bash
mkdir -p ~/test
cd test
touch Fish.md
echo '#!'/usr/bin/bash > test.sh

#上面的是准备工作
#不做也没关系

termux-open Fish.md
#用Android应用打开文件
#例如一些Android上的Markdown预览工具

termux-setup-storage
cp Fish.md /sdcard/documents/Fish.md
#让Termux拥有对/sdcard的读写权限

termux-open-url https://git.io
#调用Android浏览器打开git.io

termux-wake-lock
#打开休眠锁
#更好地完成后台任务

termux-fix-shebang test.sh
#将Linux风格的shebang转换为Termux适用的shebang

termux-info
#查看Termux与Android信息

termux-reload-settings
#在更改了字体和配色后
#立即应用更改
```
**chsh**

Linux上的chsh通过修改`/etc/passwd`文件来改变用户的启动Shell，那Termux这个单用户机制的应用呢？

答案是通过修改`~/.termux/shell`文件，这个文件不存在时Termux就去启动bash，存在时则将其作为启动Shell。

chsh不过一bashscript而己，它会从`$PREFIX/bin`中寻找用户需要的shell，并在`~/.termux`目录中建立名为shell的符号链接指向它。

**login**

Termux通过bin目录中名为login的shell脚本初始化整个终端。

### LD_LIBRARY_PATH

Android的运行时动态连接器是`/system/lib/ld-android.so`,显然，它只会查找指定目录中的共享库。所以Termux依赖于`LD_LIBRARY_PATH`这个环境变量。Termux的`ldd`使用`objdump`来分析ELF可执行文件的共享库。

### $HOME/.termux

Termux配置目录

**font**

将字体文件复制到$HOME/.termux内，重命名为font，后缀名不变，重启生效。

**colors**

配色方案同上，复制后重命名为colors.properties。

**termux.properties**

Termux在0.66版之后支持用配置文件控制特殊键了，见issue[#987](https://github.com/termux/termux-app/issues/987)

```bash
mkdir -p ~/.termux
cat > ~/.termux/termux.properties <<EOF
extra-keys = [['ESC','/','-','HOME','UP','END','PGUP'],['TAB','CTRL','ALT','LEFT','DOWN','RIGHT','PGDN']]
EOF
```

### termux-api

在Termux内调用Android api。

### resolv.conf

是的，你可以添加自定义的dns server。

### termux-am

Android 8.0专属am实现。

### keyboard

在Termux中从左向右滑动左边屏幕，出现白色侧栏，长按下方keyboard唤出特殊键。将出现的特殊键一栏向左滑，开启中文输入(严格来说是以词为单位的输入)。

也可以按音量上键+q.

### termux-elf-cleaner

Android可能在加载Termux上编译出的一些ELF文件时出错，尽管对使用没有影响，Fornwall还是写了个小工具修复问题。

### termux-exec

利用LD_PRELOAD钩子让Termux自适应Linux shebang

### More

长按屏幕,点击More。

```
#Copyright sqlsec.com
#Edit:myfreess

├── More:更多
   ├── Select URL: 选择url
   └── Share transcipt:传输当前会话的所有输出(通过Android api) 
   └── Reset: 重置
   └── Kill process: 关闭当前Shell会话及Shell子进程
   └── Style: 打开style插件
   └── Help: 打开帮助文档(即Termux wiki)
```


### termux-auth

让Termux上的openssh和dropbear支持密码登录的库

### termux-keyring

现在Termux把某些领域特定的包单独建repo存放，这些repo的GPG key都通过stable仓库的termux-keyring包管理。

### termux-create-package

```
termux-create-package/stable 0.10 all
  Utility to create Termux packages
```

### Hooks

你可以在Android应用中调用Termux内的编辑器

```shell
mkdir ~/bin
cd ~/bin
ln -s $PREFIX/bin/micro termux-file-editor
#vim用户与Emacs用户可如法炮制
#放脚本也行，记得写shebang
#$@的值是文件路径
```

文件打开时在Termux的home目录内生成downloads目录，编辑完成后文件将保存在这个目录。

建议：

```bash
cd ~
ln -s /sdcard/documents downloads
```
建立文件~/bin/termux-url-opener，然后可以在Android中调用Termux打开链接。

可以在此处链接you-get或lynx，自已写一个处理url的脚本也可以。($@的值为url)


### Build

对于使用GNU autotools作为编译管理系统的项目，在源码树根目录执行以下命令:

```shell
find . -name 'config.sub' -exec chmod u+w '{}' \; -exec cp -f "${PREFIX}/share/libtool/build-aux/config.sub" '{}' \;
find . -name 'config.guess' -exec chmod u+w '{}' \; -exec cp -f "${PREFIX}/share/libtool/build-aux/config.guess" '{}' \;
```

然后自行完成编译。

### Failsafe

打开会话时看到`[Process completed - press Enter]`?那么你的Termux环境出问题了。

别太担忧，Termux有危急情况下的修复机制。在任意Termux会话中左滑，长按侧栏下方的NEW SESSION，选择FAILSAFE，然后Termux将启动`/system/bin/sh`。

接着你需要检查自己的dotfile和启动文件，如:

+ $HOME/.bashrc

+ $HOME/.zshrc

+ $PREFIX/etc/profile

+ $PREFIX/bin/login

有时chsh会被错误地使用，那么

```shell
rm $HOME/.termux/shell
```
如果确定自己的dotfile没有问题，而安装的软件包出现了问题，执行下面的命令。

```bash
rm -rf /data/data/com.termux/files/usr
```

如果因为滥用tsu引发了文件权限问题，应该

```shell
export user=$(whoami)
su
#selinux可能导致一些问题
#关闭请执行
#setenforce 0
chmod -hR ${user} ${PREFIX}
```

### termux-services

这个包中有一组控制各类服务的脚本。

目前只有很少的package获得了支持，包括:

+ mpd
+ tor
+ transmission
+ sshd
+ ftpd
+ telnetd
+ emacsd

安装:

```shell
pkg in termux-services
```

然后重启Termux，保证termux-daemon运行。

插个题外话，在pull/3490下面评论区，Termux开发者之一Grimler91称“我相信我们的用户会自己去读文档，自己去搞清楚语法和选项。Termux不是Debian也无意模仿Debian”，结果还是真香了………

启用service

注：每次打开Termux，该service都会激活

```shell
sv-enable <service>
```

只启动一次

注：重启后就没了

```shell
sv up <service>
```
过一会儿关闭services

注：也就关闭这一次

```shell
sv down <service>
```
禁用一个service

```shell
sv-disable <service>
```
若 `$PREFIX/var/service/<service>/down` 文件存在, 则该<service>被禁用。

你也可以自写管理脚本

```shell
mkdir -p $PREFIX/var/service/<PKG>/log
ln -sf $PREFIX/share/termux-services/svlogger $PREFIX/var/service/<PKG>/log/run
```

把你的脚本放到`$PREFIX/var/service/<PKG>/run` ，确保shebang正确，有执行权限。

然后用`sv up <PKG>`尝试开启服务.

Log文件在`$PREFIX/var/log/sv/<PKG>/`放着，当前的log名为current，以前的会标好日期。

可与Termux:Boot配合。

### Pthread

Termux的Ptread库(POSIX标准的线程库)一直有点问题。如果你有需要的话

```
libandroid-spawn/stable 0.2-3 aarch64
  Shared library for the posix_spawn system function

libandroid-spawn-static/stable 0.2-3 aarch64
  Static libraries for libandroid-spawn
```
