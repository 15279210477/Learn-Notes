# Cmd Commands

--------

## 一、基础命令

```shell
# 查看所有进程端口占用情况
netstat -ano
# 查看某端口占用情况
netstat -ano|findstr “PID”
# 查看进程应用
tasklist|findstr PID
# 强制终止进程及子进程
taskkill /PID 进程号 -F -T
# ip转发
ROUTE -p add 172.17.0.0 mask 255.255.0.0 192.168.102.200
```



