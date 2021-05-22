# OpenSSH

首先，更新一下

```shell
pkg i openssh -y
```

然后设置密码

```shell
passwd
```

注意，为了安全，你什么都不会看到。

然后启动服务

```shell
sshd
```

风平浪静，Unix哲学：没事就是好事。只要没报错就没问题。~出错照样当空气~

用户名用`whoami`查看，端口8022。

可以先在本机做个测试

```shell
ssh $(whoami)@127.0.0.1 -p 8022
```


