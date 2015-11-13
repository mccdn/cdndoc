# 日志下载
单击左侧的导航窗格中 “日志下载”，可以对指定的域名设置CDN原始日志下载相关参数。

##日志下载视图
日志下载需要用户首先提供一个 Azure Storage Account 用以存放CDN日志，点击“下载设置”进行设置。

![][1]

##下载设置视图

在“下载设置”视图中，用户可以设置存储账号，以及需要下载日志的域名。设置完成后，系统会把搜集到的日志自动存放到指定的存储账号中。
用户可以删除存储账号以取消日志下载。

![][2]

##日志格式详解
日志以 blob 的形式存放在名为 "cdn-access-logs"的容器中。每个blob是一个GZip压缩后的CSV文件。其中每一栏的含义如下：

- c-ip： 客户端IP地址
- timestamp： 访问时间
- cs-method： HTTP请求动作，如GET/HEAD等。
- cs-uri-stem： 请求的URI 
- http-ver： HTTP协议版本
- sc-status： HTTP状态码 
- sc-bytes： 服务器向客户端传送的字节数
- c-referer：客户端Referer URI
- c-user-agent：客户端User Agent标识
- rs-duration(ms)：完成请求花费的时间（单位毫秒）。
- hit-miss：CDN缓存命中、丢失标识。
- s-ip：生成日志的CDN边缘节点IP地址。

>**注意**
>如果CDN日志中未包括栏目内容，则相应记录标记为“-”，比如“c-referer”记录。此外，取决于边缘节点的日志配置，“rs-duration”、“hit-miss”、“s-ip”等记录也有可能为空。

>**注意**
>网站通过CDN加速后，其访问记录多数来源于CDN边缘节点。如果用户需要知道客户端原始IP地址，请参考FAQ：
>[源站日志中如何获取访问者的原始IP](https://github.com/mccdn/cdndoc/blob/master/wacn/azurecdnfaq.md)。

 [1]: images/log-download-1.png
 [2]: images/log-download-2.png