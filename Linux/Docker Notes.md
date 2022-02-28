# Docker Notes

## 辅助命令

**docker version**：docker版本查看

**docker info**：docker信息描述

**docker --help**：docker帮助

## 镜像命令

**docker images**：列出本地主机上的镜像
options：

- -a：列出本地所有镜像

- -q：只显示镜像ID

- --digests：显示镜像的摘要信息

- --no-trunc：显示完整的镜像信息 

**docker search [OPTIONS] [镜像名]：** 查询镜像

**docker pull [镜像名]：** 拉取镜像

**docker rmi [镜像名]：** 删除镜像

**docker build [OPTIONS] PATH | URL | -：**构建镜像

**docker run [OPTIONS] IMAGE [COMMAND] [ARG...]:** 新建并启动容器

options:

- --name：容器指定一个名称

- -d：后台运行容器，并返回容器ID，也即启动守护式容器

- -i：以交互模式运行容器，通常与-t同时使用

**docker ps [OPTIONS]：** 列出所有当前正在运行的容器

options：

- -a：列出当前所有正在运行的容器和历史运行过的

- -l：显示最近创建的容器

- -n：显示最近n个创建的容器

- -q：静默模式，只显示容器编号

- --no-trunc：不截断输出

**exit：** 容器停止并退出（ctrl+P+Q：容器不停止退出）

**docker start  [容器IDor容器名]：** 启动容器

**docker restart [容器IDor容器名]：** 重启容器

**docker stop [容器IDor容器名]：** 停止容器

**docker kill [容器IDor容器名]：** 强制停止容器

**docker rm [容器ID]：** 删除已停止的容器

一次性删除多个容器：

- docker rm -f $ (docker ps -a -q)

- docker ps -a -q | xargs docker rm

**docker loogs [OPTIONS] [容器ID]：** 查看容器日志

options：

- -t：加入时间戳

- -f：跟随最新的日志打印

- --tail [数字]：显示最后多少条

**docker top [容器ID]：** 查看容器内运行的进程

**docker inspect [容器ID]：** 查看容器内部细节

**docker exec -it [容器ID] [bashShell]：** 进入正在运行的容器并以命令行交互(/bin/bash)

**docker attach [容器ID]：** 重新进入容器

上述两个区别：

- attach：直接进入容器启动命令的终端，不会启动新的进程

- exec：在容器中打开新的终端，并且可以启动新的进程

**docker cp [容器ID]: [容器内路径] [目的主机路径]：** 从容器内拷贝文件带主机上

![20201003133051.png](..\Images\20201003133051.png)

