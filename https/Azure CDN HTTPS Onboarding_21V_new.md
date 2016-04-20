# Microsoft Azure CDN HTTPS 加速服务


## 开通流程
目前CDN HTTPS加速服务仅对Azure付费用户（包括EA Paid和WebDirect PIA）开放。如果客户需要开通请联系[Microsoft Azure Support](https://www.azure.cn/support/contact/)进行开通配置。

### 需要客户提供如下的信息：

1. Azure 订阅ID


### WATS将上述的开通信息发送给微软CDN团队

微软CDN团队：<mccdncn@microsoft.com>

微软CDN团队收到开通请求并确认之后，会对用户提供的Azure订阅开通HTTPS加速服务。之后，用户可以自行登陆到Azure管理门户完成自助式的创建操作。


### SSL证书申请和配置
收到配置请求后，Azure CDN会代为申请SSL证书（详细的证书类型请见附注）。
> **注意**

> 此证书申请配置时间大约需要***五个工作日***。同时在申请过程中，需要客户帮忙配合证书签发机构确认域名所有权。具体确认方式，请参见另外的帮助文档。







### 配置完成生效
全部配置完成后，用户可以像创建其他加速类型服务一样，做最后的CNAME记录设置。之后，通过统一的Azure CDN管理门户来完成其他相应的管理配置任务。



## 附录

### 关于所使用的SSL证书
SSL证书类型为SAN多域名证书（SAN/UCC SSL）: 

SAN 证书 – Subject Alternative Name certificates, 又称为 UCC 证书– Unified Communication Certificates。SANs SSL证书允许您在同一张证书中，添加多个需要保护的"域名"或"服务器"名。这种功能为客户提供了非常大的使用弹性，它使您可以创建一张易于使用和安装，却又比通配符SSL 证书更安全，完全适合您服务器安全需求的SSL证书。

证书签发机构： <https://www.digicert.com/>
	
该SSL证书由Azure CDN代为申请，安装和维护。

### 关于计费信息
CDN HTTPS加速节点的计费信息，会和其他CDN加速节点一样可以通过相应的[Azure帐户门户](https://account.windowsazure.cn)查看，并被汇总到相应的Azure订阅下面。


### 关于CDN HTTPS的价格
目前Azure CDN HTTPS加速服务包含在高级版当中，具体的计费方式请参见[Microsoft Azure官方网站](https://www.azure.cn/home/features/cdn/#price)。

