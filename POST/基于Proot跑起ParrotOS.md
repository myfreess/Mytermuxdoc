### 在Termux上跑起parrot os!

Termux自带的包不少，但有些包例如pandoc在termux上编译失败，这时就要用到proot linux了!

**fetch-rootfs**

首先获取parrot os的rootfsurl。

```
source <(curl -fsSL https://raw.githubusercontent.com/myfreess/Termux-writer/master/Rootfs.list)
```
下载tar包

```shell
mkdir -p ~/Termuxparrot&&cd ~/Termuxparrot
[ $(dpkg --print-architecture) = arm ]&&curl -O $parrot_armhf
[ $(uname -m) = aarch64 ]&&curl -O $parrot_arm64
```

Extract出rootfs(其实就是解压tar包)，返回上层目录，编写启动脚本。

proot启动脚本编写指南：[Proot](https://github.com/myfreess/Mytermuxdoc/wiki/Proot)

**edit-source**

更换Tuna源。

```
cat > /etc/apt/sources.list <<<"deb http://mirrors.tuna.tsinghua.edu.cn/parrot/ parrot main contrib non-free"
```

不过，我的爪机容量只有8G，所以我把Parrot安装到外置sd卡上了。就放在外置的`Android/data/com.termux`目录下，一切正常。除了symbol link创建不了还有没有执行权限。好吧，其实我完全失败了。
