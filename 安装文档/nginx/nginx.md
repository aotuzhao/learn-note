介绍

1.反向代理

![image-20191024140435056](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20191024140435056.png)

2.负载均衡

![image-20191024140657963](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20191024140657963.png)

3.动静分离

![image-20191024140718116](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20191024140718116.png)

安装

```shell
 #安装pcre
 #http://downloads.sourceforge.net/project/pcre/pcre/8.35/pcre-8.35.tar.gz
 cd /usr/local/src/
 wget http://downloads.sourceforge.net/project/pcre/pcre/8.35/pcre-8.35.tar.gz
 tar zxvf pcre-8.35.tar.gz
 cd pcre-8.35
 ./configure
 make && make install
 
 #安装gcc等
 yum -y install make zlib zlib-devel  gcc-c++ libtoll openssl openssl-devel
 
 #安装nginx
 tar zxvf nginx-1.6.2.tar.gz
 cd nginx-1.6.2
 ./configure
 make && make install
 
 /usr/local/nginx/conf/nginx.conf
```

通过Docker安装Nginx

```shell

```





常见命令

```shell
#查看版本
./nginx -v
#启动
./nginx
#关闭
./nginx -s stop
#重新加载配置文件
./nginx -s reload
```

配置文件

```yaml

#user  nobody;
worker_processes  1;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       mime.types;
    default_type  application/octet-stream;

    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    #gzip  on;

    server {
        listen       80;
        server_name  localhost;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            root   html;
            index  index.html index.htm;
        }

        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

        # proxy the PHP scripts to Apache listening on 127.0.0.1:80
        #
        #location ~ \.php$ {
        #    proxy_pass   http://127.0.0.1;
        #}

        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        #
        #location ~ \.php$ {
        #    root           html;
        #    fastcgi_pass   127.0.0.1:9000;
        #    fastcgi_index  index.php;
        #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
        #    include        fastcgi_params;
        #}

        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        #location ~ /\.ht {
        #    deny  all;
        #}
    }


    # another virtual host using mix of IP-, name-, and port-based configuration
    #
    #server {
    #    listen       8000;
    #    listen       somename:8080;
    #    server_name  somename  alias  another.alias;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}


    # HTTPS server
    #
    #server {
    #    listen       443 ssl;
    #    server_name  localhost;

    #    ssl_certificate      cert.pem;
    #    ssl_certificate_key  cert.key;

    #    ssl_session_cache    shared:SSL:1m;
    #    ssl_session_timeout  5m;

    #    ssl_ciphers  HIGH:!aNULL:!MD5;
    #    ssl_prefer_server_ciphers  on;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}

}

```

<img src="C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20191024144439631.png" alt="image-20191024144439631" style="zoom:50%;" />

1.全局块

从配置文件开始到events块之间的内容，主要设置一些影响nginx服务器整体运行的配置指令，主要包括配置运行Nginx服务器的用户（组）、允许生成的work Process数，进程PID存放路径、日志存放路径和类型以及配置文件的引入等。

```shell
#user  nobody;
worker_processes  1;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;
```

`worker_processes`：这是Nginx服务器并发处理服务的关键配置，worker_processes值越大，可以支持的并发处理量越大，但是会受到硬件、软件等设备的制约。

2.events块

```shell
events {
    worker_connections  1024;
}

```

events块涉及的指令主要影响Nginx服务器与用户的网络连接，常用设置包括是否开启对多 worker_processes下的网络连接进行序列化，是否允许同时接收多个网络连接，选取哪种事件驱动模型来处理连接请求，每个worker_processes可以同时支持的最大连接数等。

上面配置中描述每个worker_processes最大连接数为1024，这部分配置对Nginx的性能影响较大，在实际中应该灵活配置。

3.http块

可以嵌套多个server，配置代理，缓存，日志定义等绝大多数功能和第三方模块的配置。如文件引入，mime-type定义，日志自定义，是否使用sendfile传输文件，连接超时时间，单连接请求数等 

​	server块：配置虚拟主机的相关参数，一个http中可以有多个server。

​                    location块：配置请求的路由，以及各种页面的处理情况。

配置示例

```shell
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
        server_name  127.0.0.1;   #监听地址       
        location  ~*^.+$ {       #请求的url过滤，正则匹配，~为区分大小写，~*为不区分大小写。
           #root path;  #根目录
           #index vv.txt;  #设置默认页
           proxy_pass  http://mysvr;  #请求转向mysvr 定义的服务器列表
           deny 127.0.0.1;  #拒绝的ip
           allow 172.18.5.54; #允许的ip           
        } 
    }
}
```

注意：

> 1、几个常见配置项：
>
> - 1.$remote_addr 与 $http_x_forwarded_for 用以记录客户端的ip地址；
> - 2.$remote_user ：用来记录客户端用户名称；
> - 3.$time_local ： 用来记录访问时间与时区；
> - 4.$request ： 用来记录请求的url与http协议；
> - 5.$status ： 用来记录请求状态；成功是200；
> - 6.$body_bytes_s ent ：记录发送给客户端文件主体内容大小；
> - 7.$http_referer ：用来记录从那个页面链接访问过来的；
> - 8.$http_user_agent ：记录客户端浏览器的相关信息；
>
> 2、惊群现象：一个网路连接到来，多个睡眠的进程被同事叫醒，但只有一个进程能获得链接，这样会影响系统性能。
>
> 3、每个指令必须有分号结束。







Demo

1.反向代理

![image-20191024154253834](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20191024154253834.png)

根据访问路径跳转到不同服务器

![image-20191024171749601](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20191024171749601.png)

路径通配符：

 https://www.cnblogs.com/sign-ptk/p/6723048.html 

用于匹配URL

 https://blog.csdn.net/zorro_jin/article/details/85003947 

```shel
location [ = | 空格 | ~ | ~* | ^~ ] uri { 
    ... 
}
```

- 精准匹配： =
- 字符串匹配： 空格
- 优于正则匹配： ^~
- 正则匹配： ~ | ~* | !~ | !~*
- 通用匹配： /

  其中，" ~ "表示-正则匹配且区分大小写；" ~ *"表示-正则匹配且不区分大小写；加上" ! "代表-不匹配





2.负载均衡

 https://blog.csdn.net/xyang81/article/details/51702900 

 https://blog.csdn.net/qq_35119422/article/details/81505732 

```shell
1、轮询（默认）
每个请求按时间顺序逐一分配到不同的后端服务器，如果后端服务器down掉，能自动剔除。 
upstream backserver { 
server 192.168.0.14; 
server 192.168.0.15; 
} 

2、指定权重
指定轮询几率，weight和访问比率成正比，用于后端服务器性能不均的情况。 
upstream backserver { 
server 192.168.0.14 weight=8; 
server 192.168.0.15 weight=10; 
} 

3、IP绑定 ip_hash
每个请求按访问ip的hash结果分配，这样每个访客固定访问一个后端服务器，可以解决session的问题。 
upstream backserver { 
ip_hash; 
server 192.168.0.14:88; 
server 192.168.0.15:80; 
} 

4、fair（第三方）
按后端服务器的响应时间来分配请求，响应时间短的优先分配。 
upstream backserver { 
server server1; 
server server2; 
fair; 
} 

5、url_hash（第三方）
按访问url的hash结果来分配请求，使每个url定向到同一个后端服务器，后端服务器为缓存时比较有效。 
upstream backserver { 
server squid1:3128; 
server squid2:3128; 
hash $request_uri; 
hash_method crc32; 
} 
--------------------
proxy_pass http://backserver/; 
upstream backserver{ 
ip_hash; 
server 127.0.0.1:9090 down; (down 表示当前的server暂时不参与负载) 
server 127.0.0.1:8080 weight=2; (weight 默认为1.weight越大，负载的权重就越大) 
server 127.0.0.1:6060; 
server 127.0.0.1:7070 backup; (其它所有的非backup机器down或者忙的时候，请求backup机器) 
} 

max_fails ：允许请求失败的次数默认为1.当超过最大次数时，返回proxy_next_upstream 模块定义的错误 
fail_timeout:max_fails次失败后，暂停的时间
————————————————
```



```
Nginx 的 upstream目前支持的分配算法： 
1)、轮询 ——1：1 轮流处理请求（默认）

      每个请求按时间顺序逐一分配到不同的应用服务器，如果应用服务器down掉，自动剔除，剩下的继续轮询。 
2)、权重 ——you can you up
      通过配置权重，指定轮询几率，权重和访问比率成正比，用于应用服务器性能不均的情况。 
3)、ip_哈希算法
      每个请求按访问ip的hash结果分配，这样每个访客固定访问一个应用服务器，可以解决session共享的问题。

```

```
upstream myServer {    
  
    server 192.168.72.49:9090 down;   
    server 192.168.72.49:8080 weight=2;   
    server 192.168.72.49:6060;   
    server 192.168.72.49:7070 backup;   
} 
upstream tomcats {
    server 192.168.0.100:8080 weight=2 max_fails=3 fail_timeout=15;
    server 192.168.0.101:8080 weight=3;
    server 192.168.0.102:8080 weight=1;
}

1）down

    表示单前的server暂时不参与负载

2）Weight

    默认为1.weight越大，负载的权重就越大。

3）max_fails

    允许请求失败的次数默认为1.当超过最大次数时，返回proxy_next_upstream 模块定义的错误

4）fail_timeout

    max_fails 次失败后，暂停接收请求的时间。

5）Backup

    其它所有的非backup机器down或者忙的时候，请求backup机器。所以这台机器压力会最轻。
    
max_conns
限制分配给某台Server处理的最大连接数量，超过这个数量，将不会分配新的连接给它。默认为0，表示不限制。注意：1.5.9之后的版本才有这个配置
```



3.动静分离

同理，通过location路径的配置实现不同资源访问到不同服务器。



Nginx高可用

 [https://cnblogs.com/woniusky/p/11050499.html](https://www.cnblogs.com/woniusky/p/11050499.html) 

KeepAlived

 主要提供**loadbalancing**（负载均衡）和 **high-availability**（高可用）功能，负载均衡实现需要依赖Linux的虚拟服务内核模块（ipvs），而高可用是通过VRRP协议实现多台机器之间的故障转移服务。 

<img src="C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20191025101731658.png" alt="image-20191025101731658" style="zoom:50%;" />

上图是Keepalived的功能体系结构，大致分两层：用户空间（user space）和内核空间（kernel space）。 
　　**内核空间**：主要包括IPVS（IP虚拟服务器，用于实现网络服务的负载均衡）和NETLINK（提供高级路由及其他相关的网络功能）两个部份。 
　　**用户空间**：

- WatchDog：负载监控checkers和VRRP进程的状况
- VRRP Stack：负载负载均衡器之间的失败切换FailOver，如果只用一个负载均稀器，则VRRP不是必须的。
- Checkers：负责真实服务器的健康检查healthchecking，是keepalived最主要的功能。换言之，可以没有VRRP Stack，但健康检查healthchecking是一定要有的。
- IPVS wrapper：用户发送设定的规则到内核ipvs代码
- Netlink Reflector：用来设定vrrp的vip地址等。

　　Keepalived的所有功能是配置keepalived.conf文件来实现的。

![image-20191025103028508](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20191025103028508.png)