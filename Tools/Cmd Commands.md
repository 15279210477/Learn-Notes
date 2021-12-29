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
```



