# Azure China CDN图片处理功能公共预览版正式开放

随着移动互联网的发展，随时拍照分享生活的点滴已经成为一种生活姿态；信息爆炸和碎片化时代，图片无疑提高了信息接收的效率。但是图片质量的提高、图片数量的几何级增加以及高并发的访问，导致图片加载速度越来越慢，也给企业带来巨大的流量成本；移动终端多样化，对图片尺寸要求也不近相同；同时，版权保护催生了对图片加水印的需求。

Azure China CDN团队以用户需求为导向，迅速迭代开发出“图片处理”功能，现开放公共预览版本给用户，让用户随时随地访问处理过的图片版本。也欢迎广大用户积极反馈，Azure China CDN产品组会努力改善、优化产品，为大家提供极致图片体验。

通过Azure China CDN的图片处理功能，可以实现缩放、裁剪、旋转、锐化、模糊、管道、水印亮度对比、图片格式转换、获取图片信息等功能，灵活适配各种终端大小、不同页面的图片展示样式和水印防盗等需求。

图片处理功能作为Azure China CDN的增值服务免费提供给所有用户，用户需要创建一个“图片处理”加速类型的CDN加速节点。目前该服务没有图形化管理界面，CDN节点创建好之后，所有的图片处理请求都是通过Restful API的形式提供给用户，直接引用CDN加速域名加上控制图片尺寸、裁剪模式以及质量等图片处理参数，即可访问经过处理的图片版本。

## 使用Azure China CDN图片处理功能效果图

下面这个小动画简单演示了对经过CDN加速的图片进行缩放和加水印的过程，可以看到在CDN加速域名后加上相应的参数即可得到期待处理的效果。

   ![1]

心动不如行动，快去[Azure门户预览](https://portal.azure.cn/)体验Azure中国CDN的图片处理功能吧！更多详情，请参考[Azure CDN 图片服务 RESTful API 文档](https://www.azure.cn/documentation/articles/cdn-image-processing/).


<!--Image references-->
[1]: ./img/image-processing2.gif