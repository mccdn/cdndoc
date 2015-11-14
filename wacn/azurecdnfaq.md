# Windows Azure CDN 常见问题 (FAQ)

1. 源站日志中如何获取访问者的原始IP
2. 如何绑定CDN边缘节点

## 源站日志中如何获取访问者的原始IP

网站通过CDN加速后，其访问来源绝大部分将会来自于CDN缓存节点。CDN回源时，会在HTTP Header **X-Forwarded-For** 中填入原始IP，源站的Web服务器可以修改日志配置记下该信息。

以 Nginx 为例，其配置文件可以加入如下信息：

    log_format logCDN '$remote_addr forwarded for $http_x_forwarded_for - $remote_user [$time_local]  '
                      '"$request" $status $body_bytes_sent '
                      '"$http_referer" "$http_user_agent"';

    access_log /var/log/nginx/access.log logCDN;

## 如何绑定CDN边缘节点

用户通过自定义域名 _cdn.mydomain.com_ 为自己的源站 _www.mydomain.com_ 在 Azure CDN 平台成功创建 CDN 服务后，获得加速域名 _cdn.mydomain.com.mschcdn.com_。用户可以绑定host进行一些基本排查。 步骤如下：

1. ping cdn.mydomain.com.mschcdn.com 得到边缘节点的IP地址，如：a.b.c.d
2. 修改本地hosts文件，添加纪录 "a.b.c.d www.domain.com"

用户打开浏览器访问 _www.domain.com_，如果打开网站显示成功，则说明CDN没有问题。如果无法访问，而hosts文件中IP地址改为源站的IP地址后可以成功访问，则说明CDN服务器有问题。


>**注意**
>Windows下，hosts文件的路径为 C:\Windows\System32\drivers\etc\hosts。
>Linux、BSD等UNIX类操作系统下该文件路径为 /etc/hosts。修改该文件需要管理员权限。

>**注意**
>对于Windows Azure Blob 和 Cloud Service，直接访问域名会得到404。此时可以通过访问一个有效URI来排查。