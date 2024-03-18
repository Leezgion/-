# nginx.conf

```nginx

# user指定Nginx进程运行的用户和用户组
user nginx;

# worker_processes指定Nginx服务器的工作进程数  通常设置为CPU核心数。值越大，可以支持的并发处理量就越多。
worker_processes auto;

# error_log定义错误日志文件的路径
# 这个设置可以放入全局块，http块，server块，级别以此为：debug|info|notice|warn|error|crit|alert|emerg 
error_log /var/log/nginx/error.log debug;

# pid指定Nginx主进程的PID文件路径
pid /var/run/nginx.pid;

# events定义Nginx服务器的事件模型 影响Nginx服务器与用户的网络连接。
events {

    # worker_connections指定每个工作进程的最大并发连接数
    worker_connections 1024;

    # 使用epoll I/O模型(Linux环境)
    use epoll;

    # 启用多进程接受连接  设置一个进程是否可以同时接受多个新连接
    multi_accept on;

    #设置网路连接序列化，防止惊群现象发生，默认为on
    accept_mutex on;
}

# http定义HTTP协议相关的配置
http {

    # include包含其他配置文件
    include /etc/nginx/mime.types;
    include /etc/nginx/conf.d/*.conf;

    # default_type定义默认的MIME类型
    default_type application/octet-stream;

    # log_format定义日志格式
    log_format main '$remote_addr - $remote_user [$time_local] "$request" '
                    '$status $body_bytes_sent "$http_referer" '
                    '"$http_user_agent" "$http_x_forwarded_for"';

    # access_log定义访问日志文件的路径和格式
    access_log /var/log/nginx/access.log main;

    # 开启高效传输模式  允许使用sendfile方式传输文件。
    sendfile on;

    # 限制每次调用sendfile传输的数据量。默认为0，即不设上限。
    sendfile_max_chunk 100k;

    # 长连接超时时间 设置连接的保持时间。默认为75s
    keepalive_timeout 65;

    # 定义一个上游服务器组。
    upstream mysvr {
      server 127.0.0.1:7878;
      server 192.168.10.121:3333 backup;  #此服务器为备份服务器。
    }

    # 开启gzip压缩
    gzip on;

    # 定义错误页面的重定向地址。
    error_page 404 https://www.baidu.com;

    # server定义一个虚拟主机服务器块，用于处理特定的域名或IP地址的请求
    server {

        # 设置单个连接上的请求上限次数。
        keepalive_requests 120;

        # listen指定服务器监听的端口和IP地址
        listen 80;

        # server_name指定服务器的域名
        server_name example.com;

        # location定义特定URL路径的配置
        location / {

            # root指定服务器的根目录，用于提供静态文件
            root /usr/share/nginx/html;

            # index定义默认的索引文件
            index index.html;

        }

        # location可以定义多个不同的URL路径配置
        location /api {

            # proxy_pass将请求代理到指定的后端服务器
            proxy_pass http://backend_server;

            # 定义访问控制规则。
            deny 127.0.0.1;
            allow 172.18.5.54;
        }

        #~ : 表示uri包含正则表达式，且区分大小写。
        #~* : 表示uri包含正则表达式，且不区分大小写。
        #= : 表示uri不含正则表达式，要求严格匹配。

        location ~* \.(gif|jpg|jpeg|png|css|js|ico|xml)$ {
            # 静态文件缓存时间
            expires 5d;
        }

    }
     # 可以定义更多的server块来处理不同的域名或IP地址
}
```

## 正向代理(Forward Proxy)和反向代理(Reverse Proxy)

### 正向代理(Forward Proxy)

> 正向代理是位于客户端和原始服务器之间的代理服务器。当客户端向原始服务器发送请求时，请求首先被发送到正向代理服务器，然后由代理服务器转发给原始服务器。对于原始服务器来说，请求来自代理服务器而不是客户端本身，因此原始服务器无法直接识别客户端的身份。
>
> 示例:公司内部网络中的代理服务器可用作正向代理。当员工在内部网络中访问外部网站时，请求首先发送到代理服务器，然后代理服务器代表员工向外部服务器发送请求，并将响应返回给员工。

使用场景

> 1. `访问控制与安全性`:
>    - `访问限制`:正向代理可以用于限制客户端对互联网资源的访问，实现网络访问控制策略。
>    - `安全过滤`:可以通过正向代理实现安全过滤，例如过滤恶意网站、拦截恶意请求等，从而提高网络安全性。
> 2. `内容缓存与优化`:
>    - `内容缓存`:正向代理可以缓存经常访问的资源，减少对原始服务器的请求，提高资源获取速度。
>    - `安全过滤`:可以通过正向代理对传输的内容进行优化，例如压缩、合并、加速等，提高传输效率和用户体验。
> 3. `访问控制与日志记录`:
>    - `访问控制`:通过正向代理可以记录和审计客户端对互联网资源的访问，以便进行访问控制和监控。
>    - `日志记录`:可以在正向代理服务器上记录客户端的访问日志，用于分析和监控客户端的行为。
> 4. `访问资源的跨域需求`:
>    - `访问境外资源`:某些资源可能因为地理位置限制而无法直接访问，可以通过正向代理实现访问境外资源的需求。
>    - `访问内部资源`:在一些特殊的网络环境下，可能需要通过正向代理访问内部资源，例如内部测试服务器、开发环境等。
> 5. `网络访问优化`:
>    - `网络加速`:通过正向代理，可以优化网络访问路径，提高网络访问速度和稳定性，减少网络延迟和丢包率。
>    - `内容过滤`:可以通过正向代理对传输的内容进行过滤和优化，例如去除广告、屏蔽恶意内容等，提高用户体验和网络安全性。

### 反向代理(Reverse Proxy)

> 反向代理是位于原始服务器和客户端之间的代理服务器。客户端向反向代理发送请求，反向代理根据一定规则将请求转发给后端的多个原始服务器之一，并将后端服务器的响应返回给客户端。对于客户端来说，所有的通信都是与反向代理直接进行的，而不知道后端的实际原始服务器。
>
> 示例:负载均衡器是反向代理的一个常见用例。当多个服务器提供相同的服务时，反向代理可以根据负载均衡策略将请求分发到不同的服务器上，以实现负载均衡和高可用性。
