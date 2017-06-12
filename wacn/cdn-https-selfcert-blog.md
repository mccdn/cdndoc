<properties linkid="dev-net-common-tasks-cdn" urlDisplayName="CDN" pageTitle="Overview of Windows Azure CDN in China - Azure feature guide" metaKeywords="Azure CDN, Azure CDN, Azure blobs, Azure caching, Azure add-ons" description="Learn the overview of WIndows Azure CDN, advantages, typical scenarios and key features." metaCanonical="" services="" documentationCenter=".NET" title="" authors="" solutions="" manager="" editor="" />
<tags ms.service=""
ms.date=""
wacn.date="11/24/2015"
/>
    
# Azure China CDN用户上传HTTPS自有证书正式发布

Azure CDN自2015年4月份就开始提供线下人工配置的HTTPS加速服务，2016年5月份开始在CDN管理门户实现step by step的全新开通引导流程，用户可以清楚的了解到整个开通流程每一步骤的状态信息且开通周期缩短到5-7天左右，从而获得一个更好的用户体验。这两种方式都是Azure CDN代为申请证书，属于高级版CDN服务。高级版服务于2017年5月1日起降价，降价幅度高达33%，详情请参考请参考Azure官方网站。

随着苹果宣布2017年开始App Store中所有的APP必须启用HTTPS，百度、谷歌对HTTPS加密的安全网站进行优先排名，用户对HTTPS加密访问的需求也水涨船高。我们收到很多用户请求要求上传自有证书。经过产品组的不懈努力，向所有付费用户开放了自有证书上传的自助化操作和自动化部署。用户可以通过新版CDN管理门户，自助化上传自有证书，并将证书和域名进行绑定，实现自动化证书部署。域名和证书绑定完成后，证书会自动部署，部署时间一般为2-4小时。

## 自助化上传证书步骤如下：

1. 需跳转到新版CDN管理门户，进行HTTPS服务的开启。
2. 在”证书管理“界面，上传PEM格式的证书和私钥。支持泛域名证书上传。
3. 到”域名管理“对域名启用HTTPS服务，并绑定相应证书。

## Azure CDN代为申请证书的HTTPS加速服务和用户自有证书的区别

Azure CDN代为申请证书的HTTPS加速服务和用户自有证书都是自助化配置，性能上差异不大，主要区别在于价格和证书：

1. 价格

- Azure代为申请证书的方式涉及到证书的申请和维护，属于高级版CDN服务；

- 用户上传自有证书的HTTPS加速服务，将于2017年7月1日起，归为标准CDN服务，采用标准CDN服务的价格。高级服务和标准服务价格请参考Azure官方网站。

2. 证书

- Azure代为申请证书的SSL证书类型为SAN多域名证书（SAN/UCC SSL），SANs SSL证书允许您在同一张证书中，添加多个需要保护的"域名"或"服务器"名。证书签发机构： https://www.digicert.com/。
- 用户自有证书支持SNI证书，SNI证书可以多个HTTPS 客户共享同一个IP 地址。SNI证书不支持Windwos XP系统下所有的IE版本，浏览器会提示不受信任。

3. 证书部署时间

- Azure CDN代为申请证书的部署时间是5-7个工作日。
- 用户自有证书的方式，部署时间一般为2-4小时。

4. 选择建议

- 如果用户已经有加速域名对应的证书，可以选择用户自有证书的方式。SNI证书不支持Windwos XP系统下所有的IE版本，浏览器会提示不受信任。
- 如果用户没有证书，又不想自己申请，可以选择Azure CDN代申请证书的方式。

5. 开通步骤

- Azure CDN代为申请证书的HTTPS，需要开工单申请开通，然后到portal上了解到整个开通流程每一步骤的状态信息，请参考Azure CDN HTTPS 加速服务 - AzureCDN待申请证书。
- 用户自有证书的方式，直接跳转到新版CDN管理门户，上传证书，并将域名和证书进行绑定即可。
    
## 后续更新

- 心动不如行动，快去Azure CDN新版管理门户开通HTTPS加速，为您的用户提供安全，快速的用户体验的同时节省成本。更多详情，请参考Azure CDN HTTPS加速服务-用户自有证书和Azure CDN HTTPS 加速服务-AzureCDN待申请证书。
- 产品组也将继续完善HTTPS自有证书的功能，比如支持级联证书，更换证书和删除证书等。
- 如果您对我们的产品有任何意见或者建议，欢迎到我们的产品反馈页面进行反馈。我们的产品团队会针对您的反馈对产品做出改进。
