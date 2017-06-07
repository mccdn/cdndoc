# Azure CDN HTTPS加速服务-用户自有证书

Azure CDN提供HTTPS安全加速服务，支持用户上传自有证书，也支持Azure CDN代为申请证书的自动化配置，均只开放给付费用户。本文档针对用户自己上传证书自助化配置的情况以及证书说明，Azure CDN代为申请证书的配置操作请参考[Azure CDN HTTPS加速服务-Azure代为申请证书](https://www.azure.cn/documentation/articles/cdn-https-how-to/)。

## 配置说明

- HTTPS加速只发放给付费用户。
- 支持泛域名的HTTPS加速。
- 支持的加速类型为网页加速，下载加速，点播加速和直播加速。
     >**注意** 图片处理加速类型暂不支持HTTPS加速。
- 用户需先创建好所需的HTTP加速，之后点击配置界面的“管理”按钮，跳转到CDN管理界面启用HTTPS服务，并上传PEM格式的证书和秘钥。详见后文的启用HTTPS加速配置向导。
- 开启HTTPS加速后，默认同时支持HTTP和HTTPS方式的请求。如需强制将HTTP请求跳转成HTTPS请求，请联系世纪互联进行配置。我们后续会在管理界面实现自动化选择。
- 回源协议默认跟随用户发起的请求协议，即HTTP请求通过HTTP协议回源，HTTPS请求通过HTTPS协议回源。如需指定只有HTTP回源或者只有HTTPS回源，请联系世纪互联尽心配置。我们后续会在管理界面实现自动化选择。

## 证书说明

- 用户自有证书的HTTPS加速是基于SNI技术实现的，SNI证书可以多个HTTPS 客户共享同一个IP 地址。
    >**注意** SNI技术不支持Windwos XP系统下所有的IE版本，浏览器会提示不受信任。

- 开启HTTPS加速后，需要上传加速域名的证书和私钥，证书和私钥需要匹配，否则会校验出错。

- 支持看证书信息，但不支持证书下载，也不支持秘钥查看，请保管好证书相关信息。

### 证书格式说明

- 证书格式为PEM格式，不支持其他格式的证书，需将其他格式的证书转换成PEM格式，可以通过openssl工具进行转换。
- 证书以[-----BEGIN CERTIFICATE-----,-----END CERTIFICATE-----]开头结尾。
- 秘钥目前只支持RSA PKCS8编码格式，PKCS8编码的私钥是以[-----BEGIN PRIVATE KEY-----,-----END PRIVATE KEY-----]开头结尾。
    >**注意**
    >可以使用openssl工具对秘钥编码格式进行转换，以RSA PKCS#1转PKCS8为例：
    >
    >.\openssl.exe pkcs8 -topk8 -inform PEM -outform PEM -in .\keyfile.key -out converted.key -nocrypt

- 暂不不支持中间证书或证书链，用户只需上传域名对应的具体证书。
### 常见证书格式转换

#### DER格式转换为PEM
- 证书转换
    >openssl x509 -in cert.der -inform DER -out cert.pem -outform PEM
- 私钥转换
    >openssl rsa -in privagekey.der -inform DER -out privatekey.pem -outform PEM
#### PFX格式转换为PEM
- 证书转换
    >openssl pkcs12 -in cert.pfx -nokeys -out cert.pem
- 私钥转换
    >openssl pkcs12 -in cert.pfx -nocerts -out key.pem -nodes

## 价格说明

自有证书的HTTPS加速价格是标准版服务价格，具体计费方式请参见[Azure官方网站](https://www.azure.cn/pricing/details/cdn/)。

## 启用HTTPS加速配置向导

1. 用户可以在[经典管理门户](https://manage.windowsazure.cn)创建任意加速类型（网页加速，点播加速，下载加速和直播加速）的CDN节点，也可以在新版[Azure门户预览](https://portal.azure.cn)中创建CDN节点。本文以新版Azure门户预览为例进行演示。
2. 




