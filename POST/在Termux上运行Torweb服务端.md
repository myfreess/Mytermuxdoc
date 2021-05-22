### Orweb service

Tor出现也有些年头了。国内常常将使用Tor映射的web服务称为暗网。谣言满天飞，真理在Wiki。从Termuxwiki看了Tor使用简单教程之后，决定动手。



首先

```bash
pkg in tor lighttpd -y
```

编辑Torrc

```bash
cat > $PREFIX/etc/tor/torrc <<EOF
#Enable TOR SOCKS proxy
SOCKSPort 127.0.0.1:9050
#Hidden Service: Http
HiddenServiceDir /data/data/com.termux/files/home/.tor/hiddenservice
HiddenServicePort 80 127.0.0.1:8080
EOF
```
编写lighttpd配置文件

```bash
termux-setup-storage&&rm -rf ~/storage
cat > ~/lighttpd.conf <<EOF
server.port             = 8080
server.document-root    = "/sdcard/Orweb"
server.upload-dirs      = ( "/data/data/com.termux/files/usr/tmp" )
index-file.names        = ( "index.html" )
mimetype.assign         = (
                                ".html" => "text/html",
                                ".txt" => "text/plain",
                                ".css" => "text/css",
                                ".js" => "application/x-javascript",
                                ".jpg" => "image/jpeg",
                                ".jpeg" => "image/jpeg",
                                ".gif" => "image/gif",
                                ".png" => "image/png",
                                "" => "application/octet-stream"
                       )
EOF
```


启动`Orweb-daemon`

```bash
mkdir -p /sdcard/Orweb
lighttpd -f ~/lighttpd.conf& > /dev/null
tor&
```

等待tor连接到主服务器并完成初始化(bootstrap)，然后新开一个termux会话，执行:

```
echo "Onion域名:$(cat ~/.tor/hiddenservice/hostname)"
```
Shadowsocks自己挂上.

然后该想想这个网站该放点什么了.用[Mnote](https://www.coolapk.com/apk/com.hjq.md)写一份Markdown文档，转换为html，命名为`index.html`放到`/sdcard/Orweb`目录.


Hugo,Hexo,jekyll可以提供更漂亮的静态blog，不过不想折腾，毕竟截完图就自删了，没必要花太长时间。


我最爱看网友编的Darkweb故事了，虽然那些视频图片造的都挺烂的:D，但它们给我带来了很多欢乐。

**Warning:**不要用宝贵的tor流量看视频，很多人等着救命用！

Seealso:https://thehiddenwiki.org

