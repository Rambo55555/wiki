有两台服务器A和B，A能连到B，但是B不能连到A，该如何让B能够访问到A上的服务呢？

首先在服务器A上运行命令：
```
ssh -p 50026 -N -R 60022:localhost:9100 rambo@ipofB
```

其中，50026是连B需要的端口，9100是A上的服务端口，ipofB是B的IP，60022表示在B上新开一个端口来绑定A的9100端口

通过这种方式，在B上就能够通过 http://localhost:60022来访问A上的服务啦

上述命令可以新建一个tmux会话来一直运行

```
tmux new -s service_for_B
ssh -p 50026 -N -R 60022:localhost:9100 rambo@ipofB
```

