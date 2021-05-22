# Command line usage

首先装好Linuxdeploy，配置发行版。

然后在Termux里执行

```shell
su
```

不是**tsu!!!!**

然后执行一条命令

```shell
/data/data/ru.meefik.linuxdeploy/bin/linuxdeploy config
centos          centos     armhfp     7          bootstrap extra/ssh init
fedora          fedora     armhfp     24         bootstrap desktop extra/ssh gr
```

然后你可以直接启动fedora。SSH连不上也没关系了，反正有Termux。

```shell
/data/data/ru.meefik.linuxdeploy/bin/linuxdeploy -p fedora start -m
Mounting partitions:
/ ... done
/proc ... done
/sys ... done
/dev ... done
/dev/pts ... done
/dev/shm ... done
:: Mounting partitions:
/ ... skip
:: Configuring core/mnt ...
:: Configuring core/net ...
:: Starting extra/ssh ... done
:: Starting init/run-parts:
rc.local ... done
```

依此类推，任一发行版都可以这么干。
