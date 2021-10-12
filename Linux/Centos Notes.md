# Centos Notes

-----

## 一、设置网络

/etc/sysconfig/network-script/{网卡名}

```txt
网络地址转换/DHCP
TYPE=Ethernet
PROXY_METHOD=none
BROWSER_ONLY=no
BOOTPROTO=dhcp
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILUTE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=enp0s3
UUID=XXX
DEVICE=enp0s3
ONBOOT=yes
设置静态ip
BOOTPROTO=static
IPADDR=192.168.1.160
NETMASK=255.255.255.0
GATEWAY=192.168.1.1
DNS1=119.29.29.29
DNS2=8.8.8.8
```

## 二、常用命令

```shell
# 查看磁盘使用情况
df -h
# 查看当前目录使用大小
du -h --max-depth=1
# 停止防火墙
systemctl stop firewalld.service
# 禁止防火墙开机启动
systemctl disable firewalld.service

# java environment
export JAVA_HOME=/usr/java/jdk1.8.0_144
export CLASSPATH=.:${JAVA_HOME}/jre/lib/rt.jar:${JAVA_HOME}/lib/dt.jar:${JAVA_HOME}/lib/tools.jar
export PATH=$PATH:${JAVA_HOME}/bin
# 使环境变量生效
source /etc/profile

# 查看指定端口占用情况
lsof -i:8080
```

