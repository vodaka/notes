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

