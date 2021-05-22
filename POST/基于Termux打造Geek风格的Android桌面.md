# Talk

显而易见，TermuxUser们的热情真是越来越高了，Yadominjinta@github说他现在连管理文件都用Termux。amsitlab@github也展示出了他的热情：将Termux当作Android启动器!

项目在这里↓

https://github.com/amsitlab/termuxlauncher

使用java编写，可用Termux编译(太清真了)，Apk会分析系统并生成一个Bashscript,用于启动app。

我写了一个wrapper，帮助fish用户使用termuxlauncher。

```shell
#!/data/data/com.termux/files/usr/bin/bash
. "${EXTERNAL_STORAGE}/termuxlauncher/.apps-launcher"
[ $# -eq 0 ]||launch "$@"
```

将上面那个脚本保存为launch，放到`$PREFIX/bin`，然后

`launch openhub`可启动openhub(假设已安装)，当然了，生成的Bashscript不会即时刷新。对新安装的app可能不支持。还有，Bashscript放在内置储存上，得运行`termux-setup-storage`才能读取脚本。

旧手机装上，就真d是服务器了。开机即是命令行!

