# 1 安装vmtools后，并设置共享文件夹后，在/mnt/hgfs下不出现共享文件夹的问题
### 从vmware-tools光盘中考出tar.gz文件并解压
### 进入解压后的文件夹
```
# cd vmware-tools-distrib/
```
### 在执行./vmware-install.pl之前，对安装文件进行以下修改
```
# cd lib/modeules/source/
# tar xf vmhgfs.tar
```
### 得到mvhgfs-only目录
```
# cd vmhgfs-only
# vim page.c
```
### 将下面的(3,19,0)修改为(3,10,0)
```
#if LINUX_VERSION_CODE >= KERNEL_VERSION(3, 19, 0) 改成下面的值
#if LINUX_VERSION_CODE >= KERNEL_VERSION(3, 10, 0)
```
### 这是因为centos7的内核版本是3.10.0。我这里使用的是vmware workstation12 ,虚拟机是Debian9,内核为4.9.0,所以我是修改为(4,9,0)
### 修改完成之后，请使用:wq!强制保存只读文件，删除之前的tar包，将vmhgfs-only重新打包
```
# cd ..
# rm vmhgfs.tar
# tar cf vmhgfs.tar vmhgfs-only
# rm -rf vmhgfs-only
```
### 回到 vmware-tools-distrib目录，执行安装
```
# cd ../../..
# ./vmware-install.pl
```


tux@dev:~/vmware-tools-distrib$ ps axu | grep vm
root         36  0.0  0.0      0     0 ?        S<   21:42   0:00 [vmstat]
root       8113  0.0  0.0 125568   676 ?        Ssl  21:47   0:00 /usr/bin/vmhgfs-fuse -o subtype=vmhgfs-fuse,allow_other /mnt/hgfs
tux        8183  0.0  0.0  14528   944 pts/0    S+   21:52   0:00 grep vm
tux@dev:~/vmware-tools-distrib$ ls /mnt/hgfs/
E

# 2 另一种安装vmware-tools的方式(针对于debian类系统)
### 对于一个安装vmware-tools成功后，正常重启后应该启动的关于vmware-tools的进程包括：
```
root        276  0.0  0.0 208620   572 ?        Ssl  11:38   0:00 vmware-vmblock-fuse /run/vmblock-fuse -o rw,subtype=vmware-vmblock,default_permissions,allow_other,dev,suid
root        279  0.3  0.2 133772 10528 ?        Ss   11:38   0:24 /usr/bin/vmtoolsd
root       1888  0.0  0.0 233060   884 ?        Ssl  13:03   0:00 /usr/bin/vmhgfs-fuse -o subtype=vmhgfs-fuse,allow_other /mnt/hgfs
yuxiaoh+   5220  0.5  0.7 266476 28252 tty2     S+   13:49   0:00 /usr/bin/vmtoolsd -n vmusr --blockFd 3
```
### 其中PID 279,1888,5220必须有，1888是跟共享文件夹相关的，5220是与host和client间相互复制粘贴相关的进程。
### 对于debian类系统，设置好apt源，不采用vmware自带的安装程序，采用以下命令安装：
```
# apt-get install open-vm-tools open-vm-tools-desktop
```
### 安装完成后复制粘贴的进程重启后能自动启动，但是与共享文件夹相关的进程不能自动启动，所以自定义添开机加启动命令。添加方法如下：
```
# debian9默认不带/etc/rc.local,而rc.local服务还是有的，通过rc-local.service实现
# cat /lib/systemd/system/rc.local.service
# 默认情况下此服务是关闭状态
# sudo systemctl status rc-local.service
# 手动添加一个/etc/rc.local文件,讲需要开机启动的命令添加到/etc/rc.local文件中的exit 0之前即可
# sudo vim /etc/rc.local
    #!/bin/sh -e
    #
    # rc.local
    #
    # This script is executed at the end of each multiuser runlevel.
    # Make sure that the script will "exit 0" on success or any other
    # value on error.
    #
    # In order to enable or disable this script just change the execution
    # bits.
    #
    # By default this script does nothing.
    /usr/bin/vmhgfs-fuse -o subtype=vmhgfs-fuse,allow_other /mnt/hgfs
    exit 0
    :wq
# sudo chmod +x /etc/rc.local
# sudo systemctl start rc-local.service
```

