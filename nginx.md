## 介绍

### nginx是什么

Nginx 是一款轻量级的 ==HTTP 服务器==，采用==事件驱动的异步非阻塞==处理方式框架，这让其具有极好的 IO 性能，时常用于服务端的==反向代理==和==负载均衡==

> web服务器：负责处理和响应用户请求，一般也称为http服务器，如 Apache、IIS、Nginx
>
> 应用服务器：存放和运行系统程序的服务器，负责处理程序中的业务逻辑，如 Tomcat、Weblogic、Jboss（现在大多数应用服务器也包含了web服务器的功能）



### nginx和tomcat区别

Nginx 是HTTP Server，Tomcat 则是一个Application Serve，其本质上它是一个Servlet/JSP应用的容器

HTTP Server 关心的是 HTTP 协议层面的传输和访问控制，所以在 Apache/Nginx 上你可以看到代理、负载均衡等功能。客户端通过 HTTP Server 访问服务器上存储的资源（HTML 文件、图片文件等）。通过 CGI 技术，也可以将处理过的内容通过 HTTP Server 分发，但是一个 HTTP Server 始终只是把服务器上的文件如实的通过 HTTP 协议传输给客户端。

应用服务器，顾名思义是一个应用的容器。它首先需要支持开发语言的 Runtime环境（对于 Tomcat 来说，就是 Java），保证应用能够在应用服务器上正常运行。其次，需要支持应用相关的规范，例如类库、安全方面的特性。对于 Tomcat 来说，就是需要提供 JSP/Sevlet 运行需要的标准类库、Interface 等。为了方便，应用服务器往往也会集成 HTTP Server 的功能，但是不如专业的 HTTP Server 那么强大，所以应用服务器往往是运行在 HTTP Server 的背后，执行应用，将动态的内容转化为静态的内容之后，通过 HTTP Server 分发到客户端。



## 功能

### 动静分离

### 反向代理

### 负载均衡

### 正向代理



## docker 搭建nginx

**1）拉取**

```  bash
docker pull nginx
```

**2）创建nginx配置文件**

先创建一个临时容器，然后从容器中复制 default.conf出来，可以自行修改default.conf，自定义配置项。
然后再创建正式使用的 nginx 容器并挂载。
将容器文件拷贝到主机中命令：`docker cp 容器id:/容器文件路径/文件名 /主机文件路径`

``` bash
dockre cp nginx:/etc/nginx/conf.d/default.conf /root/nginx/conf
```



docekr中nginx的默认路径位置

- conf：/etc/nginx/nginx.conf

- html：/usr/share/nginx/html

	​	网站文件存放的地方, 默认只有Nginx欢迎页面, 可以通过改变Nginx配置文件的方式来修改这个位置

- log：/var/log/nginx

	​	access.log：每一个访问请求都会默认记录在这个文件中

	​	error.log：  任何Nginx的错误信息都会记录到这个文件中

> nginx容器/etc/nginx/conf.d文件夹内，自带default.conf配置文件。和redis不一样，redis容器内是不带配置文件的
>
> 直接挂载文件夹的话，如果宿主机内为空文件夹，那么容器内挂载的文件夹里的内容将会被清空
> 如果宿主机内为非空文件夹的话，那么容器内挂载的文件夹里的内容将会被清空，再将宿主机内的文件复制到容器内的文件夹里
>
> 直接挂载文件的话（docker可以直接挂载文件），用vim修改宿主机文件后，只有重启容器后，容器内文件内容才会变化。要想不重启，需要对容器内的文件赋予777权限，`chmod 777 redis.conf`。所以挂载文件夹会方便些。
>
> 挂载后，改变宿主机文件，容器内文件会变化；改变容器内文件，宿主机内文件同样会变化
>
> (docker在进行文件挂载时，并不是仅仅挂载文件名到对应位置，而是将文件对应的inode 进行映射。用vim进行文件的编辑并保存时，系统采用的是备份、替换的策略，文件用vim等工具编辑的过程实质是，备份原来的文件，当新文件编辑完成后，再将新文件替换文原件，这会导致文件的inode变化，所以docker内外的文件并不会同步。而用cat，echo等重定向操作修改文件时，文件的inode保持不变，所以不会发生类似现象。)

**3）创建nginx容器并运行**

```bash
docker run -d \
--name nginx \
-p 80:80 \
-v /root/nginx/conf:/etc/nginx/conf.d \
-v /root/nginx/html:/usr/share/nginx/html \
-v /root/nginx/log:/var/log/nginx \
nginx

#/usr/share/nginx/html为nginx，网站文件存放的地方, 默认只有Nginx欢迎页面。挂载后即可替换为自己的网站页面
```



## nginx配置文件

### 配置文件结构

- 全局块：配置影响nginx全局的指令。一般有运行nginx服务器的用户组，nginx进程pid存放路径，日志存放路径，配置文件引入，允许生成worker process数等。
- events块：配置影响nginx服务器或与用户的网络连接。有每个进程的最大连接数，选取哪种事件驱动模型处理连接请求，是否允许同时接受多个网路连接，开启多个网络连接序列化等。
- http块：用于配置Nginx的业务功能，可以嵌套多个server，配置代理，缓存，日志定义等绝大多数功能和第三方模块的配置。如文件引入，mime-type定义，日志自定义，是否使用sendfile传输文件，连接超时时间，单连接请求数等。
- server块：配置虚拟主机的相关参数，一个http中可以有多个server。必须位于http内部，用于配置Nginx的一个主机；其中listen指定监听的端口号；server_name指定主机名；location用于指定主机上的资源位置。
- location块：配置请求的路由，以及各种页面的处理情况。

``` nginx
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
}
```



### 配置文件详解

``` nginx
#定义Nginx运行的用户和用户组
user www www;
#
#nginx进程数,建议设置为等于CPU总核心数.
worker_processes 8;
#
#全局错误日志定义类型,[ debug | info | notice | warn | error | crit ]
error_log /var/log/nginx/error.log info;
#
#进程文件
pid /var/run/nginx.pid;
#
#一个nginx进程打开的最多文件描述符数目,理论值应该是最多打开文件数（系统的值ulimit -n）与nginx进程数相除,但是nginx分配请求并不均匀,所以建议与ulimit -n的值保持一致.
worker_rlimit_nofile 65535;
#
#工作模式与连接数上限
events
{
    #参考事件模型,use [ kqueue | rtsig | epoll | /dev/poll | select | poll ]; epoll模型是Linux 2.6以上版本内核中的高性能网络I/O模型,如果跑在FreeBSD上面,就用kqueue模型.
    use epoll;
    #单个进程最大连接数（最大连接数=连接数*进程数）
    worker_connections 1024;    #最大连接数，默认为512
}
#
#设定http服务器
http
{
    include mime.types; #文件扩展名与文件类型映射表
    default_type application/octet-stream; #默认文件类型
    #charset utf-8; #默认编码
    server_names_hash_bucket_size 128; #服务器名字的hash表大小
    client_header_buffer_size 32k; #上传文件大小限制
    large_client_header_buffers 4 64k; #设定请求缓
    client_max_body_size 8m; #设定请求缓
    keepalive_timeout 65;  #连接超时时间，默认为75s，可以在http，server，location块。
    # 开启目录列表访问,合适下载服务器,默认关闭.
    autoindex on; # 显示目录
    autoindex_exact_size on; # 显示文件大小 默认为on,显示出文件的确切大小,单位是bytes 改为off后,显示出文件的大概大小,单位是kB或者MB或者GB
    autoindex_localtime on; # 显示文件时间 默认为off,显示的文件时间为GMT时间 改为on后,显示的文件时间为文件的服务器时间
    
    sendfile on; # 开启高效文件传输模式,sendfile指令指定nginx是否调用sendfile函数来输出文件,对于普通应用设为 on,如果用来进行下载等应用磁盘IO重负载应用,可设置为off,以平衡磁盘与网络I/O处理速度,降低系统的负载.注意：如果图片显示不正常把这个改成off.
    tcp_nopush on; # 防止网络阻塞
    tcp_nodelay on; # 防止网络阻塞
    
    
    # FastCGI相关参数是为了改善网站的性能：减少资源占用,提高访问速度.下面参数看字面意思都能理解.
    fastcgi_connect_timeout 300; ## 链接
    fastcgi_send_timeout 300;  ##读取 是指nginx进程向fastcgi进程发送request的整个过程的超时时间
    fastcgi_read_timeout 300;  ##发请求 是指fastcgi进程向nginx进程发送response的整个过程的超时时间
    fastcgi_buffer_size 64k;
    fastcgi_buffers 4 64k;
    fastcgi_busy_buffers_size 128k;
    fastcgi_temp_file_write_size 128k;
    
    # gzip模块设置
    gzip on; #开启gzip压缩输出
    gzip_min_length 1k; #允许压缩的页面的最小字节数,页面字节数从header偷得content-length中获取.默认是0,不管页面多大都进行压缩.建议设置成大于1k的字节数,小于1k可能会越压越大
    gzip_buffers 4 16k; #表示申请4个单位为16k的内存作为压缩结果流缓存,默认值是申请与原始数据大小相同的内存空间来存储gzip压缩结果
    gzip_http_version 1.1; #压缩版本（默认1.1,目前大部分浏览器已经支持gzip解压.前端如果是squid2.5请使用1.0）
    gzip_comp_level 2; #压缩等级.1压缩比最小,处理速度快.9压缩比最大,比较消耗cpu资源,处理速度最慢,但是因为压缩比最大,所以包最小,传输速度快
    gzip_types text/plain application/x-javascript text/css application/xml;
    #压缩类型,默认就已经包含text/html,所以下面就不用再写了,写上去也不会有问题,但是会有一个warn.
    gzip_vary on;#选项可以让前端的缓存服务器缓存经过gzip压缩的页面.例如:用squid缓存经过nginx压缩的数据
    
    #开启限制IP连接数的时候需要使用
    #limit_zone crawler $binary_remote_addr 10m;
    
    ##upstream的负载均衡,四种调度算法(下例主讲)##
    
    #虚拟主机的配置
    server
    {
        # 监听端口
        listen 80;
        # 域名可以有多个,用空格隔开
        server_name 127.0.0.1;
        # HTTP 自动跳转 HTTPS
        rewrite ^(.*) https://www.baidu.com;
        deny 127.0.0.1;  #拒绝的ip
        allow 172.18.5.54; #允许的ip 
    }
    upstream myserver {   
      server 127.0.0.1:8080;
      server 192.168.24.189:8080 backup;  #热备
    }
    server
    {
        # 监听端口 HTTPS
        listen 443 ssl;
        server_name https://www.baidu.com;
        root /data/www/;
        # 配置域名证书
        ssl_certificate      C:\WebServer\Certs\certificate.crt;
        ssl_certificate_key  C:\WebServer\Certs\private.key;
        ssl_session_cache    shared:SSL:1m;
        ssl_session_timeout  5m;
        ssl_protocols SSLv2 SSLv3 TLSv1;
        ssl_ciphers ALL:!ADH:!EXPORT56:RC4+RSA:+HIGH:+MEDIUM:+LOW:+SSLv2:+EXP;
        ssl_prefer_server_ciphers  on;
    
        index index.html index.htm index.php;
        
        location ~ .*\.(php|php5)?$
        {
            fastcgi_pass 127.0.0.1:9000;
            fastcgi_index index.php;
            include fastcgi.conf;
        }
        
        # 配置地址拦截转发，解决跨域验证问题
        location /oauth/{
            proxy_pass https://localhost:13580/oauth/;
            proxy_set_header HOST $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        }
        
        # 图片缓存时间设置
        location ~ .*\.(gif|jpg|jpeg|png|bmp|swf)$ {
            expires 10d;
        }
        
        # JS和CSS缓存时间设置
        location ~ .*\.(js|css)?$ {
            expires 1h;
        }

        # 日志格式设定
        log_format access '$server_name $remote_addr -$remote_user [$time_local] "$request"'
                  '$status $uptream_status $body_bytes_sent "$http_referer"'
                  '"$http_user_agent" "$http_x_forwarded_for" '
                  '$ssl_protocol $ssl_cipher $upstream_addr $request_time $upstream_response_time';
       # 定义本虚拟主机的访问日志
        access_log /var/log/nginx/access.log access;
        
        # 设定查看Nginx状态的地址.StubStatus模块能够获取Nginx自上次启动以来的工作状态，此模块非核心模块，需要在Nginx编译安装时手工指定才能使用
        location /NginxStatus {
            stub_status on;
            access_log on;
            auth_basic "NginxStatus";
            auth_basic_user_file conf/htpasswd;
            #htpasswd文件的内容可以用apache提供的htpasswd工具来产生.
        }
    }
}
```

