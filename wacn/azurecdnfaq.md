# Windows Azure CDN 常见问题 (FAQ)

1. 源站日志中如何获取访问者的原始IP

## 源站日志中如何获取访问者的原始IP

网站通过CDN加速后，其访问来源绝大部分将会来自于CDN缓存节点。CDN回源时，会在HTTP Header **X-Forwarded-For** 中填入原始IP，源站的Web服务器可以修改日志配置记下该信息。

以 Nginx 为例，其配置文件可以加入如下信息：

    log_format logCDN '$remote_addr forwarded for $http_x_forwarded_for - $remote_user [$time_local]  '
                      '"$request" $status $body_bytes_sent '
                      '"$http_referer" "$http_user_agent"';

    access_log /var/log/nginx/access.log logCDN;
