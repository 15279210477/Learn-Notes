# Nginx Notes

-----

## 一、常用命令

> **cd /usr/local/nginx/sbin**：进入到目录

**./nginx**：启动

**./nginx -s stop**：停止

**./nginx -s quit**：安全退出

**./nginx -s reload**： 重新加载配置文件

**ps aux|grep nginx**：查看nginx进程

---

## 二、作用

- **反向代理**：反向代理（ReverseProxy）方式是指==以代理服务器来接受请求==，然后==将请求转发给内部网络上的服务器==，并将从服务器上得到的结果返回请求连接的客户端，简单来说就是==真实的服务器不能直接被外部网络访问，想要访问必须通过代理==。
- **动静分离**：运用Nginx的反向代理功能分发请求：==所有动态资源的请求交给应用服务器==，而==静态资源的请求（例如图片、视频、CSS、JavaScript文件等）则直接由Nginx返回到浏览器==，这样能大大==减轻应用服务器的压力==。
- **负载均衡**：负载均衡也是 Nginx常用的一个功能，当==一台服务器的单位时间内的访问量越大时，服务器压力就越大==，大到超过自身承受能力时，服务器就会崩溃。为了避免服务器崩溃，让用户有更好的体验，我们==通过负载均衡的方式来分担服务器压力==。我们可以建立很多很多服务器，组成一个服务器集群，当用户访问网站时，先访问一个中间服务器，在让这个中间服务器在服务器集群中选择一个压力较小的服务器，然后将该访问请求引入该服务器。如此以来，用户的每次访问，都会保证服务器集群中的每个服务器压力趋于平衡，分担了服务器压力，避免了服务器崩溃的情况。负载均衡配置==一般都需要同时配置反向代理，通过反向代理跳转到负载均衡==。

**nginx的负载均衡策略可以划分为两大类**

- 内置策略
  - 包含==加权轮询==和ip hash，在默认情况下这两种策略会编译进nginx内核，只需在nginx配置中指明参数即可
- 扩展策略
  - 扩展策略有很多，如fair、通用hash、consistent hash等，默认不编译进nginx内核

```xml
upstream backserver { 
ip_hash; 	//每个请求按访问ip的hash结果分配，这样每个访客固定访问一个后端服务器，可以解决session的问题。
server 192.168.0.14 weight=8; 
server 192.168.0.15 weight=8;
} 
```

------

## 三、配置文件

#### 3.1、文件结构

```nginx
...              #全局块

events {         #events块
   ...
}

http      #http块
{
    ...   #http全局块
    server        #server块
    { 
        ...       #server全局块
        location [PATTERN]   #location块
        {
            ...
        }
        location [PATTERN] 
        {
            ...
        }
    }
    server
    {
      ...
    }
    ...     #http全局块
}
```

- 1、**全局块**：配置影响nginx全局的指令。一般有运行nginx服务器的用户组，nginx进程pid存放路径，日志存放路径，配置文件引入，允许生成worker process数等。
- 2、**events块**：配置影响nginx服务器或与用户的网络连接。有每个进程的最大连接数，选取哪种事件驱动模型处理连接请求，是否允许同时接受多个网路连接，开启多个网络连接序列化等。
- 3、**http块**：可以嵌套多个server，配置代理，缓存，日志定义等绝大多数功能和第三方模块的配置。如文件引入，mime-type定义，日志自定义，是否使用sendfile传输文件，连接超时时间，单连接请求数等。
- 4、**server块**：配置虚拟主机的相关参数，一个http中可以有多个server。
- 5、**location块**：配置请求的路由，以及各种页面的处理情况。

#### 3.2、基本配置

```nginx
########### 每个指令必须有分号结束。#################
#user administrator administrators;  #配置用户或者组，默认为nobody nobody。
#worker_processes 2;  #允许生成的进程数，默认为1
#pid /nginx/pid/nginx.pid;   #指定nginx进程运行文件存放地址
error_log log/error.log debug;  #制定日志路径，级别。这个设置可以放入全局块，http块，server块，级别以此为：debug|info|notice|warn|error|crit|alert|emerg
events {
    accept_mutex on;   #设置网路连接序列化，防止惊群现象发生，默认为on
    multi_accept on;  #设置一个进程是否同时接受多个网络连接，默认为off
    #use epoll;      #事件驱动模型，select|poll|kqueue|epoll|resig|/dev/poll|eventport
    worker_connections  1024;    #最大连接数，默认为512
}
http {
    include       mime.types;   #文件扩展名与文件类型映射表
    default_type  application/octet-stream; #默认文件类型，默认为text/plain
    #access_log off; #取消服务日志    
    log_format myFormat '$remote_addr–$remote_user [$time_local] $request $status $body_bytes_sent $http_referer $http_user_agent $http_x_forwarded_for'; #自定义格式
    access_log log/access.log myFormat;  #combined为日志格式的默认值
    sendfile on;   #允许sendfile方式传输文件，默认为off，可以在http块，server块，location块。
    sendfile_max_chunk 100k;  #每个进程每次调用传输数量不能大于设定的值，默认为0，即不设上限。
    keepalive_timeout 65;  #连接超时时间，默认为75s，可以在http，server，location块。

    upstream mysvr {
      server 127.0.0.1:7878;
      server 192.168.10.121:3333 backup;  #热备
    }
    error_page 404 https://www.baidu.com; #错误页
    
    server {
        keepalive_requests 120; #单连接请求上限次数。
        listen       4545;   #监听端口
        server_name  127.0.0.1;   #监听地址或者域名       
        location  ~*^.+$ {       #请求的url过滤，正则匹配，~为区分大小写，~*为不区分大小写。
           #root path;  #网页根目录
           #index vv.txt;  #设置默认页
           proxy_pass  http://mysvr;  #请求转向mysvr 定义的服务器列表
           deny 127.0.0.1;  #拒绝的ip
           allow 172.18.5.54; #允许的ip    
           try_files $uri $uri/ /index.html?$query_string;
        } 
    }
}
```

#### 3.3、配置详解

**try_files**：

```bash
try_files：当用户请求 http://localhost/example 时，这里的 $uri 就是 /example。
try_files 会到硬盘里尝试找这个文件。如果存在名为 $root/example（其中 $root 是项目代码安装目录）的文件，就直接把这个文件的内容发送给用户。
如果目录中没有叫 example 的文件。然后就看 $uri/，增加了一个 /，也就是看有没有名为 /$root/example/ 的目录。 如果也找不到，会对最后一个参数进行一个内部重定向。且只有最后一个参数可以引起一个内部重定向（最后一个参数是请求URI且必须存在，否则将会出现内部500错误），try_files 的最后一个选项 /index.php，发起一个内部 “子请求”，也就是相当于 nginx 发起一个 HTTP 请求到 http://localhost/index.php。
```
