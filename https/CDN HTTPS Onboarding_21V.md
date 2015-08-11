# Windows Azure CDN HTTPS 加速服务


## 开通流程
目前CDN HTTPS加速服务仅对Azure付费用户（包括EA和WebDirect）开放。如果客户需要开通请联系[Windows Azure Support](http://www.windowsazure.cn/support/contact/?fb=001)进行开通配置。

### 需要客户提供如下的信息：

1. Windows Azure 订阅ID
2. 源站域名或IP地址，并在源站下放置如下的路径的探测文件
	
	源站地址/ccotp/[chinacachehealthtest.txt](./img/chinacachehealthtest.txt)
3. 所要使用的自定义加速域名
4. 所使用的自定义加速域名相对应的ICP备案号
5. CDN节点（edge server）和源站（origin）所需要使用的访问协议（https:443, http:80）
	
	![][1]
6. 对带宽使用量的预估 
	
	![][2]


7. 其他需要向客户了解的信息
	
	* 客户源站位于国内还是海外？
	* 客户要加速的内容是？静态页面，动态还是视频？
    * 目前提供https服务范围是中国大陆，如果客户从国外访问的话会出现https证书报错，针对这种情况，我们可以配置成“当国外节点访问时，自动用国内CDN节点覆盖海外业务”，客户是否同意？
	* 源站域名 or 加速域名作为回源host header？
	* 缓存策略客户有无特殊要求？默认是遵循源站

### WATS将上述的开通信息发送给ChinaCache和微软CDN团队

1. ChinaCache联系人：Vincent Zhang <jian.zhang@chinacache.com>, Lynn Qi <lin.qi@chinacache.com> 
2. 微软CDN团队：<mccdncn@microsoft.com>


### SSL证书申请和配置
ChinaCache的同事在收到配置请求后，会代为申请SSL证书（详细的证书类型请见附注）。
> **注意**

> 此证书申请配置时间大约需要***五个工作日***。同时在申请过程中，需要客户帮忙设置CNAME记录用以验证域名所有权，例如客户所使用的自定义加速域名是“www.abc.cn”，那么需要客户做一个类似下面的CNAME映射到证书签发机构：

> 7D059FDC69119019DD0679835B96ABA9.www.abc.cn IN CNAME df8b850ca8a8a92fd9d767b574118b4f9b0c4922.comodoca.com 

>**此记录需要一直保留，删除的话会影响到HTTPS的访问**


### 关于缓存规则
CDN系统有默认的缓存规则。如果客户需要进行修改，请按下图所描述的方式来提交修改请求给ChinaCache：
	![][3]






### 配置完成生效
全部配置完成后，ChinaCache会回复客户需要最后做的CNAME记录名称。同时会返回一个专门为HTTPS服务生成的用户名和密码。客户可以用来登录<https://portal.chinacache.com>来查看基本的流量带宽信息，以及提交手动缓存刷新请求。




## 附录

### 关于所使用的SSL证书
SSL证书类型为SAN多域名证书（SAN/UCC SSL）: 

SAN 证书 – Subject Alternative Name certificates, 又称为 UCC 证书– Unified Communication Certificates。SANs SSL证书允许您在同一张证书中，添加多个需要保护的"域名"或"服务器"名。这种功能为客户提供了非常大的使用弹性，它使您可以创建一张易于使用和安装，却又比通配符SSL 证书更安全，完全适合您服务器安全需求的SSL证书。

证书签发机构： <https://www.comodo.com>
	
该SSL证书由Windows Azure CDN代为申请，安装和维护。

### 关于计费信息
CDN HTTPS加速节点的计费信息，会和其他CDN加速节点一样可以通过相应的[Azure帐户门户](https://account.windowsazure.cn)查看，并被汇总到相应的Windows Azure订阅下面。


### 关于CDN HTTPS的价格
目前Azure CDN HTTPS加速服务包含在高级版当中，具体的计费方式请参见[Windows Azure官方网站](http://www.windowsazure.cn/home/features/caching/?fb=001#price)。

<!--Image references-->
[1]: ./img/001.png
[2]: ./img/002.png
[3]: ./img/003.png
[4]: ./img/004.png