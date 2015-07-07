# Windows Azure CDN HTTPS 加速服务

## 响应CDN HTTPS加速请求


1. 21V WATS技术支持团队会将客户的开通请求发送给ChinaCache的负责同事
 
2. ChinaCache将新的加速域名统一添加到MSFT-HTTPS总帐户下
3. ChinaCache按照客户所提供的信息申请相应的SAN证书，安装，进行最终的CDN节点配置
4. ChinaCache帮忙给最终客户创建一个功能帐户(帐户名格式类似于MSFT-HTTPS-XXXX), 然后关联到MSFT-HTTPS，但仅开放属于这个客户的CDN节点。
5. ChinaCache待所有配置生效时，将生成的功能账号信息发给我们，并最终通知用户进行CNAME配置。
