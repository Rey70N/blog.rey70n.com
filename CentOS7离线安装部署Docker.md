首先去官方镜像下载所需RPM包：

官方镜像：<https://download.docker.com/linux/centos/7/x86_64/stable/Packages/>

所需RPM包：

containerd.io-1.2.5-3.1.el7.x86_64.rpm

docker-ce-18.09.4-3.el7.x86_64.rpm

docker-ce-cli-18.09.4-3.el7.x86_64.rpm



然后通过scp命令上传至服务器：

```
scp 本地路径 用户名@IP地址:服务器路径
```



然后使用yum命令进行安装：

```
yum install *.rpm
```

出现依赖错误：

```
错误：软件包：3:docker-ce-18.09.4-3.el7.x86_64 (/docker-ce-18.09.4-3.el7.x86_64)
          需要：container-selinux >= 2.9
 您可以尝试添加 --skip-broken 选项来解决该问题
 您可以尝试执行：rpm -Va --nofiles --nodigest
```



依赖错误很明确，我们去下载container-selinux：

下载地址：<http://mirror.centos.org/centos/7/extras/x86_64/Packages/>

进去下载地址后直接搜container-selinux，下载最新版就行。

然后安装