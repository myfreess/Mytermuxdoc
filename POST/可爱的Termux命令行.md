### 彩虹

```
yes "$(seq 231 -1 16)" | while read i; do printf "\x1b[48;5;${i}m\n"; sleep .02; done
```

### 世界地图

```
telnet mapsciie.me
```

### 终端二维码

```
echo "https://github.com/myfreess" | curl -F-=\<- qrenco.de
#终端下的二维码
```

### 天气预报

```
curl wttr.in?lang=zh
```
