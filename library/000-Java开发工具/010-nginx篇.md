# 1       课程目标

掌握nginx+tomcat反向代理的使用方法。

掌握nginx作为负载均衡器的使用方法。

掌握nginx实现web缓存方法。

 

# 2       nginx介绍

## 2.1     什么是nginx

​         Nginx是一款高性能的http 服务器/反向代理服务器及电子邮件（IMAP/POP3）代理服务器。由俄罗斯的程序设计师Igor Sysoev所开发，官方测试nginx能够支支撑5万并发链接，并且cpu、内存等资源消耗却非常低，运行非常稳定。

## 2.2     Nginx的应用场景

1、http服务器。Nginx是一个http服务可以独立提供http服务。可以做网页静态服务器。

2、虚拟主机。可以实现在一台服务器虚拟出多个网站。例如个人网站使用的虚拟主机。

3、反向代理，负载均衡。当网站的访问量达到一定程度后，单台服务器不能满足用户的请求时，需要用多台服务器集群可以使用nginx做反向代理。并且多台服务器可以平均分担负载，不会因为某台服务器负载高宕机而某台服务器闲置的情况。

 

# 3       nginx安装

## 3.1     下载

 

进入http://nginx.org/en/download.html 下载nginx1.8.0版本（当前最新稳定版本）。

tar.gz为Linux下的安装包

.zip 为Windows下的安装

![img](file:///C:\Users\ADMINI~1\AppData\Local\Temp\msohtmlclip1\01\clip_image006.gif)

 

![img](file:///C:\Users\ADMINI~1\AppData\Local\Temp\msohtmlclip1\01\clip_image008.gif)

 

## 3.2     安装

### 3.2.1  nginx安装环境

​         nginx是C语言开发，建议在linux上运行，本教程使用Centos6.5作为安装环境。



* n  gcc

​         安装nginx需要先将官网下载的源码进行编译，编译依赖gcc环境，如果没有gcc环境，需要安装gcc：yum install gcc-c++ 



* n  PCRE

​         PCRE(Perl Compatible Regular Expressions)是一个Perl库，包括 perl 兼容的正则表达式库。nginx的http模块使用pcre来解析正则表达式，所以需要在linux上安装pcre库。

**yum install -y pcre pcre-devel**

注：pcre-devel是使用pcre开发的一个二次开发库。nginx也需要此库。



* n  zlib

​         zlib库提供了很多种压缩和解压缩的方式，nginx使用zlib对http包的内容进行gzip，所以需要在linux上安装zlib库。

**yum install -y zlib zlib-devel**

 

* n  openssl

​         OpenSSL 是一个强大的安全套接字层密码库，囊括主要的密码算法、常用的密钥和证书封装管理功能及SSL协议，并提供丰富的应用程序供测试或其它目的使用。

​         nginx不仅支持http协议，还支持https（即在ssl协议上传输http），所以需要在linux安装openssl库。

**yum install -y openssl openssl-devel**

 

### 3.2.2  编译安装

将nginx-1.8.0.tar.gz拷贝至linux服务器。

 

解压：

tar -zxvf nginx-1.8.0.tar.gz

cd nginx-1.8.0

 

1、  configure

./configure --help查询详细参数（参考本教程附录部分：nginx编译参数）

 

参数设置如下：

./configure \

--prefix=/usr/local/nginx \

--pid-path=/var/run/nginx/nginx.pid \

--lock-path=/var/lock/nginx.lock \

--error-log-path=/var/log/nginx/error.log \

--http-log-path=/var/log/nginx/access.log \

--with-http_gzip_static_module \

--http-client-body-temp-path=/var/temp/nginx/client \

--http-proxy-temp-path=/var/temp/nginx/proxy \

--http-fastcgi-temp-path=/var/temp/nginx/fastcgi \

--http-uwsgi-temp-path=/var/temp/nginx/uwsgi \

--http-scgi-temp-path=/var/temp/nginx/scgi

 

**注意：上边将临时文件目录指定为/var/temp/nginx**，需要在/var下创建temp及nginx目录

 

 

2、  编译安装

make

make  install

 

安装成功查看安装目录 ：

```

```



![img](file:///C:\Users\ADMINI~1\AppData\Local\Temp\msohtmlclip1\01\clip_image010.jpg)

 

 

## 3.3     启动nginx

cd /usr/local/nginx/sbin/

./nginx 

 

查询nginx进程：

![img](file:///C:\Users\ADMINI~1\AppData\Local\Temp\msohtmlclip1\01\clip_image012.jpg)

 

15098是nginx主进程的进程id，15099是nginx工作进程的进程id

 

**注意：执行./nginx启动nginx，这里可以-c指定加载的nginx配置文件，如下:**

**./nginx -c /usr/local/nginx/conf/nginx.conf**

**如果不指定-c，nginx在启动时默认加载conf/nginx.conf文件，此文件的地址也可以在编译安装nginx时指定./configure的参数（--conf-path=指向配置文件（nginx.conf））**

 

## 3.4     停止nginx

方式1，快速停止：

cd /usr/local/nginx/sbin

./nginx -s stop

此方式相当于先查出nginx进程id再使用kill命令强制杀掉进程。

 

方式2，完整停止(建议使用)：

cd /usr/local/nginx/sbin

./nginx -s quit

此方式停止步骤是待nginx进程处理任务完毕进行停止。

 

 

## 3.5     重启nginx

方式1，先停止再启动（建议使用）：

对nginx进行重启相当于先停止nginx再启动nginx，即先执行停止命令再执行启动命令。

如下：

./nginx -s quit

./nginx

 

方式2，重新加载配置文件：

当nginx的配置文件nginx.conf修改后，要想让配置生效需要重启nginx，使用-s reload不用先停止nginx再启动nginx即可将配置信息在nginx中生效，如下：

./nginx -s reload

 

## 3.6     安装测试

nginx安装成功，启动nginx，即可访问虚拟机上的nginx：

![img](file:///C:\Users\ADMINI~1\AppData\Local\Temp\msohtmlclip1\01\clip_image014.jpg)

 

到这说明nginx基本上安装成功。

 

 

# 4       配置虚拟主机

## 4.1     什么是虚拟主机

​         虚拟主机是一种特殊的软硬件技术，它可以将网络上的每一台计算机分成多个虚拟主机，每个虚拟主机可以独立对外提供www服务，这样就可以实现一台主机对外提供多个web服务，每个虚拟主机之间是独立的，互不影响的。

 

如下图：

 

![img](file:///C:\Users\ADMINI~1\AppData\Local\Temp\msohtmlclip1\01\clip_image015.gif)

 

通过nginx可以实现虚拟主机的配置，nginx支持三种类型的虚拟主机配置，1、基于ip的虚拟主机， 2、基于域名的虚拟主机 3、基于端口的虚拟主机

 

 

## 4.2     nginx配置文件的结构

从第一个虚拟主机的例子可以看出nginx的配置文件结构如下：

 

......

events {

​    .......

}

http{

   .......

   server{

​         .......

​         }

   server{

​         .......

​         }

 

}

每个server就是一个虚拟主机。

 

## 4.3     基于域名的虚拟主机配置

### 4.3.1  需求

两个域名指向同一台nginx服务器，用户访问不同的域名显示不同的网页内容。

两个域名是aaa.test.com和bbb.test.com

nginx服务器使用虚拟机192.168.101.3 

 

### 4.3.2  准备环境

创建192.168.101.3虚拟机，保证本地电脑和虚拟网络通畅。

在192.168.101.3上安装nginx。

 

通过host文件指定aaa.test.com和bbb.test.com对应192.168.101.3虚拟机：

修改window的hosts文件：（C:\Windows\System32\drivers\etc）

![img](file:///C:\Users\ADMINI~1\AppData\Local\Temp\msohtmlclip1\01\clip_image017.jpg)

 

### 4.3.3  html目录创建

 

在192.168.101.3上创建/usr/local/aaa_html，此目录为aaa.test.com域名访问的目录

在192.168.101.3上创建/usr/local/bbb_html，此目录为bbb.test.com域名访问的目录

 

目录中的内容使用nginx自带的html文件，将/usr/local/nginx/html中的内容拷贝分别拷贝到上边两个目录中，并且将aaa_html目录中的index.html内容改为：“Welcome to aaa nginx!”

将bbb_html目录中的index.html内容改为“Welcome to bbb nginx!”

 

 

### 4.3.4  配置虚拟主机

修改/usr/local/nginx/conf/nginx.conf文件，添加两个虚拟主机，如下：

\#配置虚拟主机aaa.test.com 

server {

​                   #监听的ip和端口，配置本机ip和端口

​        listen 192.168.101.3:80;          

​                   #虚拟主机名称是aaa.test.com，请求域名aaa.test.com的url将由此server配置解析

​        server_name aaa.test.com;    

​                   #所有的请求都以/开始，所有的请求都可以匹配此location

​        location / {

​                   #使用root指令指定虚拟主机目录即网页存放目录

​                   #比如访问http://ip/test.html将找到/usr/local/aaa_html/test.html

​                   #比如访问http://ip/item/test.html将找到/usr/local/aaa_html/item/test.html

​                root /usr/local/aaa_html;        

​                                #指定欢迎页面，按从左到右顺序查找

​                index index.html index.htm;    

​        }

​    }

 

\#配置虚拟主机bbb.test.com

​    server {

​        listen 192.168.101.3:80;

​        server_name bbb.test.com;

​        location / {

​                root /usr/local/bbb_html;

​                index index.html index.htm;

​        }

​    }

 

### 4.3.5  测试

访问aaa.test.com、bbb.test.com

 

 

## 4.4     基于端口的虚拟主机

### 4.4.1  需求

nginx对外提供80和8080两个端口监听服务。

请求80端口则请求html80目录下的html

请求8080端口则请求html8080目录下的html

 

### 4.4.2  准备环境

创建192.168.101.3虚拟机，保证本地电脑和虚拟网络通畅。

在192.168.101.3上安装nginx。

 

### 4.4.3  html目录创建

将原来nginx的html目录拷贝两个目录 “html80”和“html8080”，为了方便测试需要修改每个目录下的index.html内容使之个性化。

参考基于ip或域名的虚拟主机章节。

 

### 4.4.4  配置虚拟主机

修改/usr/local/nginx/conf/nginx.conf文件，添加两个虚拟主机，如下：

 

 

\#user  nobody;

worker_processes  1;

 

events {

​    worker_connections  1024;

}

 

http {

​    include       mime.types;

​    default_type  application/octet-stream;

 

​    sendfile        on;

​    

​    keepalive_timeout  65;

​    #配置虚拟主机

​    server {

​         #监听的ip和端口，配置80

​        listen       80;

​         #虚拟主机名称这里配置ip地址

​        server_name  192.168.101.3;

​         #所有的请求都以/开始，所有的请求都可以匹配此location

​        location / {

​             #使用root指令指定虚拟主机目录即网页存放目录

​             #比如访问http://ip/test.html将找到/usr/local/html3/test.html

​             #比如访问http://ip/item/test.html将找到/usr/local/html3/item/test.html

 

​            root   /usr/local/nginx/html80;

​             #指定欢迎页面，按从左到右顺序查找

​            index  index.html index.htm;

​        }

 

​    }

​    #配置虚拟主机

​    server {

​        listen       8080;

​        server_name  192.168.101.3;

 

​        location / {

​            root   /usr/local/nginx/html8080;

​            index  index.html index.htm;

​        }

 

​    }

 

}

 

### 4.4.5  测试

启动 nginx，查看端口监听状态：

![img](file:///C:\Users\ADMINI~1\AppData\Local\Temp\msohtmlclip1\01\clip_image019.jpg)

 

访问http://192.168.101.3

![img](file:///C:\Users\ADMINI~1\AppData\Local\Temp\msohtmlclip1\01\clip_image021.jpg)

 

访问http://192.168.101.3:8080

![img](file:///C:\Users\ADMINI~1\AppData\Local\Temp\msohtmlclip1\01\clip_image023.jpg)

 

 

# 5       nginx反向代理

## 5.1     什么是反向代理

​         通常的代理服务器，只用于代理内部网络对Internet的连接请求，客户机必须指定代理服务器,并将本来要直接发送到Web服务器上的http请求发送到代理服务器中由代理服务器向Internet上的web服务器发起请求，最终达到客户机上网的目的。

​         而反向代理（Reverse Proxy）方式是指以代理服务器来接受internet上的连接请求，然后将请求转发给内部网络上的服务器，并将从服务器上得到的结果返回给internet上请求连接的客户端，此时代理服务器对外就表现为一个反向代理服务器。

 

如下图：

![img](file:///C:\Users\ADMINI~1\AppData\Local\Temp\msohtmlclip1\01\clip_image025.jpg)

 

 

## 5.2     nginx+tomcat反向代理

### 5.2.1  需求

​         两个tomcat服务通过nginx反向代理，本例子使用三台虚拟机进行测试，

​         nginx服务器：192.168.101.3

​         tomcat1服务器：192.168.101.5

​         tomcat2服务器：192.168.101.6

如下图：

 

![img](file:///C:\Users\ADMINI~1\AppData\Local\Temp\msohtmlclip1\01\clip_image026.gif)

 

 

### 5.2.2  启动tomcat

​         tomcat使用apache-tomcat-7.0.57版本，在192.168.101.5和192.168.101.6虚拟机上启动tomcat。

 

### 5.2.3  nginx反向代理配置

根据上边的需求在nginx.conf文件中配置反向代理，如下：

 

\#配置一个代理即tomcat1服务器

upstream tomcat_server1 {

​            server 192.168.101.5:8080;

​        }

\#配置一个代理即tomcat2服务器

​    upstream tomcat_server2 {

​            server 192.168.101.6:8080;

​        }

 

\#配置一个虚拟主机

​    server {

​        listen 80;

​        server_name aaa.test.com;

​        location / {

​                                     #域名aaa.test.com的请求全部转发到tomcat_server1即tomcat1服务上

​                proxy_pass http://tomcat_server1;

​                                     #欢迎页面，按照从左到右的顺序查找页面

​                index index.jsp index.html index.htm;

​        }

 

​    }

 

​    server {

​        listen 80;

​        server_name bbb.test.com;

 

​        location / {

​                                      #域名bbb.test.com的请求全部转发到tomcat_server2即tomcat2服务上

​                  proxy_pass http://tomcat_server2;

​                  index index.jsp index.html index.htm;

​        }

​    }

 

### 5.2.4  测试

分别修改两个tomcat下的webapps/ROOT/index.jsp的内容，使用tomcat1和tomcat2两个服务首页显示不同的内容，如下：

tomcat1下的index.jsp修改后：

![img](file:///C:\Users\ADMINI~1\AppData\Local\Temp\msohtmlclip1\01\clip_image028.jpg)

 

tomcat2下的index.jsp修改后：

![img](file:///C:\Users\ADMINI~1\AppData\Local\Temp\msohtmlclip1\01\clip_image030.jpg)

 

 

分别访问aaa.test.com、bbb.test.com测试反向代理。

 

请求访问aaa.test.com通过nginx代理访问tomcat1，请求访问bbb.test.com通过nginx代理访问tomcat2。

 

 

# 6       负载均衡

## 6.1     什么是负载均衡

​         负载均衡 建立在现有网络结构之上，它提供了一种廉价有效透明的方法扩展网络设备和服务器的带宽、增加吞吐量、加强网络数据处理能力、提高网络的灵活性和可用性。

​         负载均衡，英文名称为Load Balance，其意思就是分摊到多个操作单元上进行执行，例如Web服务器、FTP服务器、企业关键应用服务器和其它关键任务服务器等，从而共同完成工作任务。

## 6.2     nginx实现负载均衡

### 6.2.1  需求

​         nginx作为负载均衡服务器，用户请求先到达nginx，再由nginx根据负载配置将请求转发至 tomcat服务器。

​         nginx负载均衡服务器：192.168.101.3

​         tomcat1服务器：192.168.101.5

​         tomcat2服务器：192.168.101.6

 

![img](file:///C:\Users\ADMINI~1\AppData\Local\Temp\msohtmlclip1\01\clip_image031.gif)

 

### 6.2.2  配置

根据上边的需求在nginx.conf文件中配置负载均衡，如下：

 

upstream tomcat_server_pool{

​        server 192.168.101.5:8080 weight=10;

​        server 192.168.101.6:8080 weight=10;

​        }

 

​    server {

​        listen 80;

​        server_name aaa.test.com;

​        location / {

​                 proxy_pass http://tomcat_server_pool;

​                 index index.jsp index.html index.htm;

​        }

​    }

 

### 6.2.3  测试

请求aaa.test.com，通过nginx负载均衡，将请求转发到tomcat服务器。

通过观察tomcat的访问日志或tomcat访问页面即可知道当前请求由哪个tomcat服务器受理。

 

 

# 7       附录

 

## 7.1     nginx编译参数

--prefix= 指向安装目录

--sbin-path 指向（执行）程序文件（nginx）

--conf-path= 指向配置文件（nginx.conf）

--error-log-path= 指向错误日志目录

--pid-path= 指向pid文件（nginx.pid）

--lock-path= 指向lock文件（nginx.lock）（安装文件锁定，防止安装文件被别人利用，或自己误操作。）

--user= 指定程序运行时的非特权用户

--group= 指定程序运行时的非特权用户组

--builddir= 指向编译目录

--with-rtsig_module 启用rtsig模块支持（实时信号）

--with-select_module 启用select模块支持（一种轮询模式,不推荐在高载环境下使用）禁用：--without-select_module

--with-poll_module 启用poll模块支持（功能与select相同，与select特性相同，为一种轮询模式,不推荐在高载环境下使用）

--with-file-aio 启用file aio支持（一种APL文件传输格式）

--with-ipv6 启用ipv6支持

--with-http_ssl_module 启用ngx_http_ssl_module支持（使支持https请求，需已安装openssl）

--with-http_realip_module 启用ngx_http_realip_module支持（这个模块允许从请求标头更改客户端的IP地址值，默认为关）

--with-http_addition_module 启用ngx_http_addition_module支持（作为一个输出过滤器，支持不完全缓冲，分部分响应请求）

--with-http_xslt_module 启用ngx_http_xslt_module支持（过滤转换XML请求）

--with-http_image_filter_module 启用ngx_http_image_filter_module支持（传输JPEG/GIF/PNG 图片的一个过滤器）（默认为不启用。gd库要用到）

--with-http_geoip_module 启用ngx_http_geoip_module支持（该模块创建基于与MaxMind GeoIP二进制文件相配的客户端IP地址的ngx_http_geoip_module变量）

--with-http_sub_module 启用ngx_http_sub_module支持（允许用一些其他文本替换nginx响应中的一些文本）

--with-http_dav_module 启用ngx_http_dav_module支持（增加PUT,DELETE,MKCOL：创建集合,COPY和MOVE方法）默认情况下为关闭，需编译开启

--with-http_flv_module 启用ngx_http_flv_module支持（提供寻求内存使用基于时间的偏移量文件）

--with-http_gzip_static_module 启用ngx_http_gzip_static_module支持（在线实时压缩输出数据流）

--with-http_random_index_module 启用ngx_http_random_index_module支持（从目录中随机挑选一个目录索引）

--with-http_secure_link_module 启用ngx_http_secure_link_module支持（计算和检查要求所需的安全链接网址）

--with-http_degradation_module  启用ngx_http_degradation_module支持（允许在内存不足的情况下返回204或444码）

--with-http_stub_status_module 启用ngx_http_stub_status_module支持（获取nginx自上次启动以来的工作状态）

--without-http_charset_module 禁用ngx_http_charset_module支持（重新编码web页面，但只能是一个方向--服务器端到客户端，并且只有一个字节的编码可以被重新编码）

--without-http_gzip_module 禁用ngx_http_gzip_module支持（该模块同-with-http_gzip_static_module功能一样）

--without-http_ssi_module 禁用ngx_http_ssi_module支持（该模块提供了一个在输入端处理处理服务器包含文件（SSI）的过滤器，目前支持SSI命令的列表是不完整的）

--without-http_userid_module 禁用ngx_http_userid_module支持（该模块用来处理用来确定客户端后续请求的cookies）

--without-http_access_module 禁用ngx_http_access_module支持（该模块提供了一个简单的基于主机的访问控制。允许/拒绝基于ip地址）

--without-http_auth_basic_module禁用ngx_http_auth_basic_module（该模块是可以使用用户名和密码基于http基本认证方法来保护你的站点或其部分内容）

--without-http_autoindex_module 禁用disable ngx_http_autoindex_module支持（该模块用于自动生成目录列表，只在ngx_http_index_module模块未找到索引文件时发出请求。）

--without-http_geo_module 禁用ngx_http_geo_module支持（创建一些变量，其值依赖于客户端的IP地址）

--without-http_map_module 禁用ngx_http_map_module支持（使用任意的键/值对设置配置变量）

--without-http_split_clients_module 禁用ngx_http_split_clients_module支持（该模块用来基于某些条件划分用户。条件如：ip地址、报头、cookies等等）

--without-http_referer_module 禁用disable ngx_http_referer_module支持（该模块用来过滤请求，拒绝报头中Referer值不正确的请求）

--without-http_rewrite_module 禁用ngx_http_rewrite_module支持（该模块允许使用正则表达式改变URI，并且根据变量来转向以及选择配置。如果在server级别设置该选项，那么他们将在 location之前生效。如果在location还有更进一步的重写规则，location部分的规则依然会被执行。如果这个URI重写是因为location部分的规则造成的，那么 location部分会再次被执行作为新的URI。 这个循环会执行10次，然后Nginx会返回一个500错误。）

--without-http_proxy_module 禁用ngx_http_proxy_module支持（有关代理服务器）

--without-http_fastcgi_module 禁用ngx_http_fastcgi_module支持（该模块允许Nginx 与FastCGI 进程交互，并通过传递参数来控制FastCGI 进程工作。 ）FastCGI一个常驻型的公共网关接口。

--without-http_uwsgi_module 禁用ngx_http_uwsgi_module支持（该模块用来医用uwsgi协议，uWSGI服务器相关）

--without-http_scgi_module 禁用ngx_http_scgi_module支持（该模块用来启用SCGI协议支持，SCGI协议是CGI协议的替代。它是一种应用程序与HTTP服务接口标准。它有些像FastCGI但他的设计更容易实现。）

--without-http_memcached_module 禁用ngx_http_memcached_module支持（该模块用来提供简单的缓存，以提高系统效率）

-without-http_limit_zone_module 禁用ngx_http_limit_zone_module支持（该模块可以针对条件，进行会话的并发连接数控制）

--without-http_limit_req_module 禁用ngx_http_limit_req_module支持（该模块允许你对于一个地址进行请求数量的限制用一个给定的session或一个特定的事件）

--without-http_empty_gif_module 禁用ngx_http_empty_gif_module支持（该模块在内存中常驻了一个1*1的透明GIF图像，可以被非常快速的调用）

--without-http_browser_module 禁用ngx_http_browser_module支持（该模块用来创建依赖于请求报头的值。如果浏览器为modern ，则$modern_browser等于modern_browser_value指令分配的值；如果浏览器为old，则$ancient_browser等于 ancient_browser_value指令分配的值；如果浏览器为 MSIE中的任意版本，则 $msie等于1）

--without-http_upstream_ip_hash_module 禁用ngx_http_upstream_ip_hash_module支持（该模块用于简单的负载均衡）

--with-http_perl_module 启用ngx_http_perl_module支持（该模块使nginx可以直接使用perl或通过ssi调用perl）

--with-perl_modules_path= 设定perl模块路径

--with-perl= 设定perl库文件路径

--http-log-path= 设定access log路径

--http-client-body-temp-path= 设定http客户端请求临时文件路径

--http-proxy-temp-path= 设定http代理临时文件路径

--http-fastcgi-temp-path= 设定http fastcgi临时文件路径

--http-uwsgi-temp-path= 设定http uwsgi临时文件路径

--http-scgi-temp-path= 设定http scgi临时文件路径

-without-http 禁用http server功能

--without-http-cache 禁用http cache功能

--with-mail 启用POP3/IMAP4/SMTP代理模块支持

--with-mail_ssl_module 启用ngx_mail_ssl_module支持

--without-mail_pop3_module 禁用pop3协议（POP3即邮局协议的第3个版本,它是规定个人计算机如何连接到互联网上的邮件服务器进行收发邮件的协议。是因特网电子邮件的第一个离线协议标准,POP3协议允许用户从服务器上把邮件存储到本地主机上,同时根据客户端的操作删除或保存在邮件服务器上的邮件。POP3协议是TCP/IP协议族中的一员，主要用于支持使用客户端远程管理在服务器上的电子邮件）

--without-mail_imap_module 禁用imap协议（一种邮件获取协议。它的主要作用是邮件客户端可以通过这种协议从邮件服务器上获取邮件的信息，下载邮件等。IMAP协议运行在TCP/IP协议之上，使用的端口是143。它与POP3协议的主要区别是用户可以不用把所有的邮件全部下载，可以通过客户端直接对服务器上的邮件进行操作。）

--without-mail_smtp_module 禁用smtp协议（SMTP即简单邮件传输协议,它是一组用于由源地址到目的地址传送邮件的规则，由它来控制信件的中转方式。SMTP协议属于TCP/IP协议族，它帮助每台计算机在发送或中转信件时找到下一个目的地。）

--with-google_perftools_module 启用ngx_google_perftools_module支持（调试用，剖析程序性能瓶颈）

--with-cpp_test_module 启用ngx_cpp_test_module支持

--add-module= 启用外部模块支持

--with-cc= 指向C编译器路径

--with-cpp= 指向C预处理路径

--with-cc-opt= 设置C编译器参数（PCRE库，需要指定–with-cc-opt=”-I /usr/local/include”，如果使用select()函数则需要同时增加文件描述符数量，可以通过–with-cc- opt=”-D FD_SETSIZE=2048”指定。）

--with-ld-opt= 设置连接文件参数。（PCRE库，需要指定–with-ld-opt=”-L /usr/local/lib”。）

--with-cpu-opt= 指定编译的CPU，可用的值为: pentium, pentiumpro, pentium3, pentium4, athlon, opteron, amd64, sparc32, sparc64, ppc64

--without-pcre 禁用pcre库

--with-pcre 启用pcre库

--with-pcre= 指向pcre库文件目录

--with-pcre-opt= 在编译时为pcre库设置附加参数

--with-md5= 指向md5库文件目录（消息摘要算法第五版，用以提供消息的完整性保护）

--with-md5-opt= 在编译时为md5库设置附加参数

--with-md5-asm 使用md5汇编源

--with-sha1= 指向sha1库目录（数字签名算法，主要用于数字签名）

--with-sha1-opt= 在编译时为sha1库设置附加参数

--with-sha1-asm 使用sha1汇编源

--with-zlib= 指向zlib库目录

--with-zlib-opt= 在编译时为zlib设置附加参数

--with-zlib-asm= 为指定的CPU使用zlib汇编源进行优化，CPU类型为pentium, pentiumpro

--with-libatomic 为原子内存的更新操作的实现提供一个架构

--with-libatomic= 指向libatomic_ops安装目录

--with-openssl= 指向openssl安装目录

--with-openssl-opt 在编译时为openssl设置附加参数

--with-debug 启用debug日志

 

 

 

 

 