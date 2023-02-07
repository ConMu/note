**参考资料**

https://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html

https://www.ruanyifeng.com/blog/2011/12/ssh_port_forwarding.html

### ssh登录

以用户名user，登录远程主机host

```bash
$ ssh user@host
```

本地用户名与远程用户名一致，登录时可以省略用户名

```bash
$ ssh host
```

SSH的默认端口是22，也就是说，你的登录请求会送进远程主机的22端口。使用p参数，可以修改这个端口。

```bash
$ ssh -p 2222 user@host
```

