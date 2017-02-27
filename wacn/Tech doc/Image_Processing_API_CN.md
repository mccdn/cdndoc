Azure 图片服务 RESTful API 文档
==

对应API版本`v1`




简介
--

Azure图片服务是由`Azure存储服务`提供的一个可靠、安全且经济的图片处理服务。

通过`Azure图片服务`，用户可以以最低的存储空间和经济的开销，在任何时间、从任何位置和设备获取存储在`Microsoft Azure`上的图片或对其进行过一些处理后的版本。

_注意：`Azure图片服务`中的图片被以一个`blob`被保存在`Microsoft Azure`上的一个与您图片`Bucket`同名的存储账号下_





### 名词解释

* **Bucket**:`Bucket`是一些图片的容器，`Azure图片服务`中的图片通过`Bucket`和`Container`被更容易地管理，`Bucket`与`Azure存储账号`一一对应；
* **Bucket Key**:`Bucket Key`是一段用于身份认证的密钥，可用于上传图片和访问权限为`Private`的Bucket中的图片；
* **Container**:`Container`是存在于`Bucket`中的图片的容器，一个`Bucket`中可以包含多个`Container`；
* **Style**:`Style`是一组图片处理参数的别名，通过使用预先定义的`Style`，图片处理的过程可以被更轻松地调用和修改；
* **处理字符串**:`处理字符串`是用户请求图片时在URL中指定的一段包含了控制所需图片被返回前需要经过的处理流程的字符串；
* **API Key**:`API Key`是一段用于使用包括人脸识别在内图片分析服务的认证字符串；




### 基本信息和限制

#### 访问域名

`Azure图片服务`的区域设定和`Azure`数据中心的区域设定相同.

| 数据中心名称 | 访问域名 | 对应别名 |
|:----|:----|:----|
| 中国北部（北京） | img-north.azure.cn | chinanorth |
| 中国东部（上海） | img-east.azure.cn | chinaeast |


#### 限制

####### 1. 支持格式转换的文件格式包括 jpg, png, bmp, webp, gif
####### 2. 图片源文件的大小不可大于20MB。
####### 3. 处理后的图片尺寸不得大于 4096 * 4096像素, 且任意边边长不得大于 4096*4像素


#### 需求

##### Azure 存储账号

为了使用`Azure图片服务`，你首先需要一个Azure的存储账号。关于创建存储账号，请参阅[此处文档](https://www.azure.cn/zh-cn/documentation/articles/storage-dotnet-how-to-use-blobs/) “创建 Azure 存储帐户”章节。

##### Bearer Token

为了管理`bucket`，你需要利用Azure Active Directory (Azure AD)获取一个`bearer token`, 关于创建`bearer token`, 请参阅[此处文档](https://www.azure.cn/documentation/articles/active-directory-protocols-oauth-code/)

### 安全性

**如果您需要较高的安全保障，请:**

* 将您`Bucket`的`权限设定`(`Privacy`) 设置为`私有`(`Private`)，以保证只有拥有共享访问签名(SAS，详情请参阅`上传图片`中的`生成授权Token`一节)才可以访问其中的图片；




查看Bucket
--

**Host**: `www.img-auto.azure.cn`

**路径**:`/v1/ImageBuckets?$filter=(Subscription eq xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx)`

_其中，`xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`表示您的Subscription ID_

**参数**: 无

**方法**: GET

**请求头**:

| 名称 | 取值 | 描述 |
|:--- | :--- | :------------------------------------------------------- |
| Accept | application/json, text/plain, */* | 设置接受的响应体为JSON或文本 |
| Authorization | _取值不定_ | 拥有所对应 Resource Group 访问权限的 Bearer token | 

**请求体**:  无

**响应体**: 

描述Bucket的 JSON 格式对象组成的数组, 其中每个对象至少包含以下字段:

| 名称 | 描述 |
| :------| :--------------------------------------------------------|
| Id | 对应`Bucket`的 ID |
| Name | 对应`Bucket`的名称 |
| Status | 当前操作的状态, 0 为操作已创建，1为操作中，2为操作完成，3为操作失败 |
| PrivacyLevel | 对应`Bucket`访问权限设置, 公开为 0， 私有为 1 |
| Location | 对应`Bucket`被部署到的区域的别名 |
| ImageTypes | 对应`Bucket`拥有的图片预定义类型,JSON格式数组，当前API其值为空。 |
| Subscription | 对应`Bucket`所用 Subscription 的 ID |
| Timestamp | 当前操作的时间戳 |

**响应体示例**:

	[
		{
			"ImageBucketBatchConfig":null,
			"ImageBucketCDNConfig":null,
			"ImageBucketUsage":null,
			"ImageTypes":[],
			"Id":5,
			"CDNStatus":0,
			"Location":"chinaeast",
			"Name":"mybucket",
			"PrivacyLevel":0,
			"ResourceGroup":"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
			"Status":2,
			"Subscription":"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
			"Timestamp":"2016-08-24T03:33:14.7353182+00:00"
		}
	]




创建 Bucket 
--

通过创建`Bucket`的操作，将使得对应存储账号上的`Azure图片服务`被启用.

**Host**: `www.img-auto.azure.cn`

**路径**: `/v1/ImageBuckets`

**参数**: 无

**方法**: POST

**请求头**:

| 名称 | 取值 | 描述 |
|:--- | :--- | :------------------------------------------------------- |
| Content-Type | application/json;charset=UTF-8 | 设置请求体类型为UTF-8编码的JSON |
| Accept | application/json, text/plain, */* | 设置接受的响应体为JSON或文本 |
| Authorization | _取值不定_ | 拥有所对应 Resource Group 访问权限的 Bearer token | 

**请求体**: 

包含至少以下字段的JSON对象:

| 名称 | 描述 |
| :------| :--------------------------------------------------------|
| Name | 新`Bucket`的名称 |
| PrivacyLevel | 对应`Bucket`访问权限设置, 公开为 0， 私有为 1 |
| Subscription | 所用 Subscription 的 ID |
| ResourceGroup | 使用的Resource Group的名称 |
| Location | 对应`Bucket`被部署到的区域的别名 |

**请求体示例**  

	{
		"Name":"myimagebucket",
		"PrivacyLevel":1,
		"ResourceGroup":"MyResourceGroup",
		"Location":"chinaeast",
		"Subscription":"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
	}


**响应体**: 

包含至少以下字段的JSON对象:

| 名称 | 描述 |
| :------| :--------------------------------------------------------|
| ParameterContent | 描述新`Bucket`的JSON对象, 关于其字段的介绍请参阅`查看Bucket` |
| Status | 当前操作的状态, 0 为操作已创建，1为操作中，2为操作完成，3为操作失败 |
| Timestamp | 当前操作的时间戳 |

**响应体示例**:

	{
		"ParameterContent":{
				"Id":19,
				"CDNStatus":0,
				"Location":"chinaeast",
				"Name":"myimagebucket",
				"PrivacyLevel":1,
				"ResourceGroup":"MyResourceGroup",
				"Status":1,
				"Subscription":"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
				"Timestamp":"2016-08-25T07:33:42.9204402+00:00",
				"ImageBucketCDNConfig":null,
				"ImageBucketUsage":null,
				"ImageTypes":[],
				"ImageBucketBatchConfig":null
		},
		"Status":0,
		"Subscription":"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
		"OperationType":0,
		"AccessToken":"something",
		"PartitionKey":"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
		"RowKey":"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
		"Timestamp":"2016-08-25T07:33:43.076703+00:00",
		"ETag":null
	}




删除 Bucket
--

通过`删除 Bucket`，用户关闭了对应存储账号上的`Azure图片服务`。 若需要删除存储账号，请参阅[此处](https://msdn.microsoft.com/en-us/library/azure/hh264517.aspx).

当`Bucket`被删除，其中的图片文件仍然将以`Blob`形式保存在对应的存储账号中。 

**Host**: `www.img-auto.azure.cn`

**路径**: `/v1/ImageBuckets({ID})`

_其中， {ID} 表示需要删除的`Bucket`的ID_

**参数**: 无

**方法**: DELETE

**请求头**:

| 名称 | 取值 | 描述 |
|:--- | :--- | :------------------------------------------------------- |
| Accept | application/json, text/plain, */* | 设置接受的响应体为JSON或文本 |
| Authorization | _取值不定_ | 拥有所对应 Resource Group 访问权限的 Bearer token | 

**请求体**:  无


**响应体**: 

包含至少以下字段的JSON对象:

| 名称 | 描述 |
| :------| :--------------------------------------------------------|
| ParameterContent | 描述被删除`Bucket`的JSON对象, 关于其字段的介绍请参阅`获取 Bucket` |
| Status | 当前操作的状态, 0 为操作已创建，1为操作中，2为操作完成，3为操作失败 |
| Timestamp | 当前操作的时间戳 |

**响应体示例**:

	{
		"ParameterContent":{
				"Id":19,
				"CDNStatus":0,
				"Location":"chinaeast",
				"Name":"myimagebucket",
				"PrivacyLevel":1,
				"ResourceGroup":"MyResourceGroup",
				"Status":1,
				"Subscription":"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
				"Timestamp":"2016-08-25T07:33:42.9204402+00:00",
				"ImageBucketCDNConfig":null,
				"ImageBucketUsage":null,
				"ImageTypes":[],
				"ImageBucketBatchConfig":null
		},
		"Status":0,
		"Subscription":"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
		"OperationType":0,
		"AccessToken":"something",
		"PartitionKey":"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
		"RowKey":"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
		"Timestamp":"2016-08-25T07:33:43.076703+00:00",
		"ETag":null
	}




为 Bucket 启用 CDN 服务
--

当前版本API暂不支持此功能。

__如需启用，请联系我们并提供域名。__




查看 Style
--

**Host**: `www.img-auto.azure.cn`

**路径**: `/v1/ImageTypes?$filter=(Subscription eq xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx and (ImageBucketId eq {ID}))`

_其中`xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`表示您你的 subscription ID，`{ID}`表示需要查询的`Bucket`的 ID_

**参数**: 无

**方法**: GET

**请求头**:

| 名称 | 取值 | 描述 |
|:--- | :--- | :------------------------------------------------------- |
| Content-Type | application/json;charset=UTF-8 | 设置请求体类型为UTF-8编码的JSON |
| Accept | application/json, text/plain, */* | 设置接受的响应体为JSON或文本 |
| Authorization | _取值不定_ | 拥有所对应 Resource Group 访问权限的 Bearer token | 

**请求体**:  无

**响应体**: 

描述请求的`Style`的JSON格式对象的数组，其中每个对象至少包含以下字段:

| 名称 | 描述 |
| :------| :--------------------------------------------------------|
| Name | 对应`Style`的名称 |
| Id | 对应`Style`的ID |
| Value | 对应`Style`所代表的`处理字符串` |
| BucketName | 对应`Style`所属`Bucket`的名称 |
| ImageBucketId | 对应`Style`所属`Bucket`的ID |
| ImageBucket | 描述对应`Style`所属`Bucket`的JSON对象, 关于其字段的介绍请参阅`获取 Bucket`(对于`获取 Bucket`操作，此字段为null) |
| Subscription | 使用的 Subscription 的 ID |
| Status | 当前操作的状态, 0 为操作已创建，1为操作中，2为操作完成，3为操作失败 |
| Timestamp | 对应`Bucket`创建的时间戳 |

**响应体示例**:

	[
		{
			"ImageBucket":null,
			"Id":1,
			"BucketName":"mybucket",
			"ImageBucketId":5,
			"Name":"teststyle",
			"Status":0,
			"Subscription":"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
			"Value":"400w_400h_50sh.png",
			"Timestamp":"2016-08-26T09:14:15.6240711+00:00"
		}
	]




创建 Style
--

**Host**: `www.img-auto.azure.cn`

**路径**: `/v1/ImageTypes/`

**参数**: 无

**方法**: POST

**请求头**:

| 名称 | 取值 | 描述 |
|:--- | :--- | :------------------------------------------------------- |
| Content-Type | application/json;charset=UTF-8 | 设置请求体类型为UTF-8编码的JSON |
| Accept | application/json, text/plain, */* | 设置接受的响应体为JSON或文本 |
| Authorization | _取值不定_ | 拥有所对应 Resource Group 访问权限的 Bearer token | 

**请求体**:  

包含至少以下字段的JSON对象:

| 名称 | 描述 |
| :------| :--------------------------------------------------------|
| Name | 新`Style`的名称 |
| ImageBucketId | 新`Style`所属`Bucket`的ID |
| Subscription | 所用 Subscription 的 ID |
| Value | 对应`Style`所代表的`处理字符串` |

**请求体示例**  

	{
		"Name":"teststyle",
		"ImageBucketId":5,
		"Subscription":"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
		"Value":"400w_400h_50sh.png"
	}

**响应体**: 

包含至少以下字段的JSON对象:

| 名称 | 描述 |
| :------| :--------------------------------------------------------|
| ParameterContent | 描述被新建`Style`的JSON对象, 关于其字段的介绍请参阅`查看Style` |
| Status | 当前操作的状态, 0 为操作已创建，1为操作中，2为操作完成，3为操作失败 |
| Timestamp | 当前操作的时间戳 |

**响应体示例**:

	{
		"ParameterContent":"
		{  
			"Id": 1,  
			"BucketName": "mybucket",  
			"ImageBucketId": 5,  
			"Name": "teststyle",  
			"Status": 0,  
			"Subscription": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",  
			"Value": "400w_400h_50sh.png",  
			"Timestamp": "2016-08-26T09:14:15.6240711+00:00",  
			"ImageBucket": 
			{    
				"ImageTypes": [],    
				"Id": 5,    
				"CDNStatus": 0,    
				"Location": "chinaeast",    
				"Name": "mybucket",    
				"PrivacyLevel": 0,    
				"ResourceGroup": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",    
				"Status": 2,    
				"Subscription": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",    
				"Timestamp": "2016-08-24T03:33:14.7353182+00:00"  
			}
		}",
		"Status":0,
		"Subscription":"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
		"OperationType":1,
		"AccessToken":null,
		"PartitionKey":"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
		"RowKey":"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
		"Timestamp":"2016-08-26T09:14:16.4366177+00:00",
		"ETag":null
	}




删除 Style
--

**Site**: `www.img-auto.azure.cn`

**路径**: `/v1/ImageTypes({ID})`

_其中`{ID}`为需要删除的`Style`的ID_

**参数**: 无

**方法**: DELETE

**请求头**:

| 名称 | 取值 | 描述 |
|:--- | :--- | :------------------------------------------------------- |
| Content-Type | application/json;charset=UTF-8 | 设置请求体类型为UTF-8编码的JSON |
| Accept | application/json, text/plain, */* | 设置接受的响应体为JSON或文本 |
| Authorization | _取值不定_ | 拥有所对应 Resource Group 访问权限的 Bearer token | 

**请求体**:  无

**响应体**: 

包含至少以下字段的JSON对象:

| 名称 | 描述 |
| :------| :--------------------------------------------------------|
| ParameterContent | 描述被删除`Style`的JSON对象, 关于其字段的介绍请参阅`获取 Style` |
| Status | 当前操作的状态, 0 为操作已创建，1为操作中，2为操作完成，3为操作失败 |
| Timestamp | 当前操作的时间戳 |

**响应体示例**:

	{
		"ParameterContent":"
		{  
			"Id": 1,  
			"BucketName": "mybucket",  
			"ImageBucketId": 5,  
			"Name": "teststyle",  
			"Status": 0,  
			"Subscription": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",  
			"Value": "400w_400h_50sh.png",  
			"Timestamp": "2016-08-26T09:14:15.6240711+00:00",  
			"ImageBucket": 
			{    
				"ImageTypes": [],    
				"Id": 5,    
				"CDNStatus": 0,    
				"Location": "chinaeast",    
				"Name": "mybucket",    
				"PrivacyLevel": 0,    
				"ResourceGroup": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",    
				"Status": 2,    
				"Subscription": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",    
				"Timestamp": "2016-08-24T03:33:14.7353182+00:00"  
			}
		}",
		"Status":0,
		"Subscription":"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
		"OperationType":1,
		"AccessToken":null,
		"PartitionKey":"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
		"RowKey":"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
		"Timestamp":"2016-08-26T09:14:16.4366177+00:00",
		"ETag":null
	}




上传图片
--

当前有三种向`Azure图片服务`上传图片的方法， 包括`Azure 存储服务 API`，`File-Upload`和`MultiPart/Form-Data`

### 生成授权Token

不管选择通过何种方式上传图片，用户都需要一个[共享访问签名(Shared Access Signature, SAS)](https://azure.cn/documentation/articles/storage-dotnet-shared-access-signature-part-1/) token.


通过`Microsoft Azure`提供的`Azure 存储服务 API`, 用户可以离线生成`SAS`token.

详情请参阅 [此处文档](https://azure.cn/documentation/articles/storage-dotnet-shared-access-signature-part-2/).

*其他语言或平台所对应的文档参见[MSDN](https://msdn.microsoft.com/library),`Microsoft Azure`章节


### 使用 Azure 存储服务 API 上传图片 (推荐)

一旦获取到了`SAS`token, 你就可以通过`Azure 存储服务 API`将图片作为`Block Blob`直接上传到与所需操作的`Bucket`同名且互相对应的存储账号中.

我们强烈建议通过这种途径上传图片。 更多的指导请参阅 [此处](https://msdn.microsoft.com/en-us/library/azure/mt420159.aspx)

### 通过 File-Upload 上传图片

**Host**:`{`Bucket`名称}.{区域访问域名}`

**路径**: `/v1/images`

**参数**: 无

**方法**: POST

**请求头**:

| 名称 | 取值 | 描述 |
|:--- | :--- | :------------------------------------------------------- |
| Content-Type | application/json;charset=UTF-8 | 设置请求体类型为UTF-8编码的JSON |
| Accept | application/json, text/plain, */* | 设置接受的响应体为JSON或文本 |
| Name | _取值不定_ |  图片在`Container`中的文件名，不需要和源文件同名 |
| BucketName | _取值不定_ | 所需操作的`Bucket`的名称 |
| Authorization | _取值不定_ | 为对应图片获取的`SAS`token | 

**请求体**:  二进制的图片文件内容

**响应体**: 

包含至少以下字段的JSON对象:

| 名称 | 描述 |
| :------| :--------------------------------------------------------|
| Name | 新图片在`Container`中的文件名 |
| Id | 新图片的ID |
| BucketName | 新图片所属的`Bucket`的名称 |
| BucketId | 新图片所属的`Bucket`的ID |
| Timestamp | 当前操作的时间戳 |

**响应体示例**:

	{
		"Id":8652,
		"BucketId":3,
		"BucketName":"mybucket1",
		"ContainerName":null,
		"Name":"TEST_e6548b9b-58a2-4ec1-8d4d-88608c6e9d93_toupload.png",
		"Timestamp":"2016-07-01T04:28:06.9819784Z"
	}


### 通过 MultiPart/Form-Data 上传图片.

**Host**:`{`Bucket`名称}.{区域访问域名}`

**路径**:  `/v1/images`

**参数**: 无

**方法**: POST

**请求头**:

| 名称 | 取值 | 描述 |
|:--- | :---- | :------------------------------------------------------------------- |
| Content-Type | application/json;charset=UTF-8 | 设置请求体类型为UTF-8编码的JSON |
| Accept | application/json, text/plain, */* | 设置接受的响应体为JSON或文本 |
| Authorization | _取值不定_ | 为对应图片获取的`SAS`token | 

**请求体**:  

包含内容为所上传图片二进制内容的文件字段和以下文本字段的 MultiPart/Form-Data :

| Name | Type | Explanation |
|:--- | :---- | :------------------------------------------------------------------------- |
| Name | `text/plain` | 新图片在`Container`中的文件名 |
| BucketName | `text/plain` | 需要上传当前图片的`Bucket`名称 |
| ContainerName*(可选)* | `text/plain` | 需要上传当前图片的`Container`名称, 若此字段为空或不存在，则默认`Container``$root`将被采用 |


**响应体**:  

包含至少以下字段的JSON对象:

| 名称 | 描述 |
| :------| :----------------------------------------------------|
| BucketName | 为对应图片获取的`SAS`token |
| ImageName | 新图片在`Container`中的文件名 |
| Id | 新图片的ID |
| BucketId | 新图片所属的`Bucket`的ID |
| Timestamp | 当前操作的时间戳 |

**响应体示例**:

	{
		"Id":8652,
		"BucketId":3,
		"BucketName":"mybucket1",
		"ContainerName":null,
		"Name":"TEST_e6548b9b-58a2-4ec1-8d4d-88608c6e9d93_toupload.png",
		"Timestamp":"2016-07-01T04:28:06.9819784Z"
	}



删除图片
--

要删除图片，请使用`Azure 存储服务 API`的对应接口直接删除存储账号下图片对应的`Blob`，具体操作请参阅 [此处](https://msdn.microsoft.com/library/dd135733.aspx).


下载图片
--

**Host**:`{`Bucket`名称}.{区域访问域名}`

**路径**:  `/v1/images/{图片名}`

**参数**: 无

**方法**: GET

**请求头**: 无

**请求体**:  无

**响应体**:  所请求图片的二进制内容；
				



处理图片
--

**Host**:`{`Bucket`名称}.{区域访问域名}`

**路径**:  `/v1/images/{图片名}{分隔符}{`处理字符串`或`Style`名}`

**参数**: 无

**方法**: GET

**请求头**: 无

**请求体**:  无

**响应体**:  所请求图片的二进制内容

### 图片处理语法

#### 处理字符串

`处理字符串`由两部分组成，即处理参数和格式转化参数：

* 处理参数部分是一个由形如`{值}{参数关键字}`的单一参数由字符`_`相接而成的字符串；
* 格式转换部分则为一段形如`.{对应格式后缀名}`的字符串

__注意__ 两个部分都是可选的，但当两者都存在时，前者应处在后者之前。


#### 分隔符


| 分隔符名称 | 分隔符 | 定义 |
|:----|:----|
| 处理字符串 分隔符 | @ | 分割图片名与`处理字符串` |
| Style 分隔符 | @! | 分割图片名与预定义的`Style`名 |


###`处理字符串`的详细定义

`处理字符串`是若干个形如`{值}{参数关键字}`参数的组合，每个参数之间由`_`相互连接；

若需要进行格式转换则`Process String`必须以`.{目标格式扩展名}`结尾


#### 支持的图片操作（参数）


| 操作名 | 语法 | 备注 |
|:----------|:-------------------------------------------------------------|:-------------------------------------|
| 缩放和裁剪 | `{Percentage}p`,`{Height}h`,`{Width}w`,`{ScaleType}e`,`{Cut}c`,`{HandleIfLarger}l`,`{Red}-{Green}-{Blue}bgc`, 详情请参阅下方表格 ||
| 高级裁剪 | `{x}-{y}-{Width}-{Height}a`, 其中`{x}`和`{y}`分别定义了裁剪开始位置的横纵坐标（以原图左上角的像素为原点）,`{Width}`和`{Height}`分别定义了裁剪区域的宽高, 取值 1-4096 | 原图范围以外的裁剪开始位置将导致 400`Bad Request`，超过原图范围或指定宽高为零的裁剪将裁减到原图结束位置 |
| 区域裁剪 | `{Width}x{Height}-{Position}`rc, 其中`{Width}`和`{Height}`分别定义了裁剪区域的宽高, 取值 0-4096，裁剪将按照`{Position}`定义的对其方式进行，不同取值的含义请参阅下方表格| 定义比原图尺寸更大的宽高值将导致返回原图,为空或为零的`Height`或`Width`表示使用原图的宽或高 |
| 内切圆 | `{Radius}-{Type}ci`, 其中`{Radius}`指定了内切圆的半径,`{Type}`: 为取值0或1的整数，若为 1，则将图像裁剪为能容纳内切圆的最小正方形后返回，否则不进行裁剪 ||
| 圆角矩形 | `{Radius}-2ci`, 其中`{Radius}`指定了圆角的半径 | 圆角半径不得大于原图最短边边长的一半 |
| 索引裁剪 | `{length}x-{index}ic`或`{length}y-{index}ic`,分别对应以横坐标或纵坐标进行切片，将返回前若干片Slice组成的图像，其中`{Length}`为切片的宽度，`{index}`为返回的切片的序号 | 切片序号从零开始，大于原图的切片设定将使得原图被返回 |
| 旋转 | `{Degree}r`, 其中`{Degree}`为图片顺时针旋转的度数 ||
| 锐化 | `{Degree}sh`, 其中`{Degree}`为指定锐化程度的非负整数 ||
| 模糊 | `{Radius}-{Sigma}bl`, 其中`{Radius}`为模糊半径，`{Sigma}`为模糊标准差 ||
| 亮度与对比度 | `{Brightness}b`,`{Contrast}c`, 其中`{Brightness}`为一个可以为负的整数，表示亮度相对原图进行的调整, 同时`{Contrast}`是一个可以为负的整数，表示对比度相对原图进行的调整 ||
| 水印 | 请参阅下方对应小节 | 不得与其他参数共同存在于管道的同一阶段中 |
| 图像质量 | `{Degree}q`, 其中`{Degree}`为表示图像质量的整数值，取值0-100 | 当且仅当输出格式为`jpg/jpeg`时有效 |
| 格式转换 | `.{format}`, 其中`{format}`为目标格式 | 目前支持的格式包括`jpg`,`jpeg`,`png`,`webp`及`gif` |
| 渐进显示 | `{Setting}pr`, 其中`{Setting}`是一个取值0-1的整数，当其为1时，图像将随着加载的进行渐进呈现给用户 | 当且仅当输出格式为`jpg/jpeg`时有效 |
| 图片信息 | `info`, 返回JSON格式图片信息，详情请参阅下方对应小节 | 不得与其他参数共用 |
| 图片EXIF信息 | `exif`, 返回JSON格式图片EXIF信息，详情请参阅下方对应小节 | 不得与其他参数共用 |
| 图片主色调 | `imageAve`, 返回JSON格式图片主色调信息，详情请参阅下方对应小节 | 不得与其他参数共用 |
| 管道 | `{Process1}`&#124;`{Process2}`, 其中`Process1`与`Process2`均为`处理字符串`并且图片会先被按照`Process1`定义的操作流程进行处理，而后按照`Process2`所定义的操作流程进行处理 | `Process`可以为`图片信息``EXIF信息`及`图片主色调`以外的任何上述参数 |


#### 缩放和裁剪

##### 参数

| 名称 | 描述 |
| :---- | :---------------------------------------------------------------------------------------------- |
| `{Percentage}` | 取值 1-1000 的整数，表示对宽高进行的处理 |
| `{Height}`,`{Width}` | 所需图像的高度值和宽度值，必须为不得超过尺寸限制的非负整数 |
| `{ScaleType}` | 定义缩放模式的整数，详细介绍请参阅下方表格 |
| `{Cut}` | 值为0或1的整数，默认为0即不进行其他操作，为1则自动裁剪原图至指定尺寸 |
| `{HandleIfLarger}` | 值为0或1的整数，默认为1即允许将图片缩放至大于原图的尺寸，为0则当指定尺寸大于原图时采用原图尺寸 |
| `{Red}`,`{Green}`及`{Blue}` | 取值0-255的整数数值，分别代表RGB色彩空间下填充的颜色红、绿、蓝三通道上的值 |

__注意__

* _任何情况下处理的结果不得使图片尺寸大于限制，具体限制请参阅`简介`中的`限制`一节_

* _`Percentage`可以与`Height`或`Width`共同使用, 图片将被缩放为指定宽高乘以指定百分比之后的大小_ 

* _`Height`和`Width`均可以单独存在，缺失的数值将默认使用原图尺寸_

* _`Percentage`可以单独存在，图片将被缩放为原始尺寸乘以指定百分比之后的大小_

* _`Cut`只有在当其与`Height`和`Width`共同存在时才会起效_

* _背景色必须与`{ScaleType}`4 共同使用_

##### 缩放模式

| 值 | 表现 |
| :---- | :--------------------------------------------------------------------------------------- |
| 0 或 不定义 | 锁定宽高比且长边优先 |
| 1 | 锁定宽高比且短边优先 |
| 2 | 强制宽高 |
| 4 | 锁定宽高比且短边优先，并在缩放后以指定颜色填充空白区域（若颜色未指定则自动采用白色填充） |

**注意** 此处`长边`与`短边`的概念基于`原始长度 / 目标长度`的**比值**决定.




#### 水印操作

水印功能的`处理字符串`是一个可以包含以下字段的 Query String：

| 名称 | 描述 |
|:----|:--------------------------------------------------------------------------------------------------------------|
| watermark | 定义水印类型的整数，取值 1-3，详细释义请参阅下方表格 |
| p | 定义水印位置的整数，取值 1-9，详细释义请参阅下方表格 |
| s | 定义水印透明度的整数，取值 0-100 |
| text | 定义水印文本内容的经过`Base64`编码的字符串 |
| type | 定义水印文本所采用字体的名称经过`Base64`编码的字符串，支持字体请参阅下方表格 |
| color | 定义水印文本颜色的经过`Base64`编码的8位RGB色彩空间下的六位十六进制数字 |
| size | 定义水印文本字号的正整数 |
| object | 图片水印所使用图片的名称，可能包含指定简单的预处理的`处理字符串`，详情请见下方小节 |

**注意** 

* 字段`watermark`为必需，且其必须为Query String中的第一个字段
* 被用于图片水印的图片必须与原始图片存在于同一个`Bucket`的同一个`Container`中

##### 水印类型

| 值 | 释义 |
| :---- | :---- |
| 1 | 图片水印 |
| 2 | 文字水印 |
| 3 | 图文水印 __尚不支持__ |

##### 水印位置

| 值 | 释义 |
| :---- | :---- |
| 1 | 左上 |
| 2 | 顶部 |
| 3 | 右上 |
| 4 | 左侧 |
| 5 | 中央 |
| 6 | 右侧 |
| 7 | 左下 |
| 8 | 底部 |
| 9 | 右下 |

##### 支持字体

| 名称 | 释义 | Base64编码 |
|:------  |:--------|:----------------------|
| dengxian | 等线 | ZGVuZ3hpYW4= |
| fangsong| 仿宋 | ZmFuZ3Nvbmc= |
| fangzhengshuti | 方正舒体 | ZmFuZ3poZW5nc2h1dGk= | 
| fangzhengyaoti | 方正姚体 | ZmFuZ3poZW5neWFvdGk= |
| kaiti | 楷体 | a2FpdGk= |
| lishu | 隶书 | bGlzaHU= |
| microsoftyahei | 微软雅黑 | bWljcm9zb2Z0eWFoZWk= | 
| microsoftyaheiui | 微软雅黑UI | bWljcm9zb2Z0eWFoZWl1aQ== |
| xinsongti | 新宋体 | eGluc29uZ3Rp |
| heiti | 黑体 | aGVpdGk= |
| songti | 中易宋体 | c29uZ3Rp |
| huawencaiyun | 华文彩云 | aHVhd2VuY2FpeXVu |
| huawenfangsong | 华文仿宋 | aHVhd2VuZmFuZ3Nvbmc= |
| huawenhupo | 华文琥珀 | aHVhd2VuaHVwbw== |
| huawenkaiti | 华文楷体 | aHVhd2Vua2FpdGk= |
| huawenlishu | 华文隶书 | aHVhd2VubGlzaHU= |
| huawensongti | 华文宋体 | aHVhd2Vuc29uZ3Rp |
| huawenxihei | 华文细黑 | aHVhd2VueGloZWk= |
| huawenxingkai | 华文行楷 | aHVhd2VueGluZ2thaQ== |
| huawenxinwei | 华文新魏 | aHVhd2VueGlud2Vp |
| huawenzhongsong | 华文中宋 | aHVhd2Vuemhvbmdzb25n |
| youyuan | 幼圆 | eW91eXVhbg== |




##### 图片水印预处理

通过在`Object`参数中包含`处理字符串`,图片水印所使用的图片可以先经过预处理，`处理字符串`的语法与先前介绍的相同。

支持的预处理操作如下：

* 相对缩放, `{Percentage}P`, 将用作水印的图片缩放为主图片的制定百分比大小，而后再叠加至主图片作为水印。 _(注意此处的`P`为 __大写__ )_
* 缩放和裁剪, 细节请参阅`支持的图片操作（参数）`
* 高级裁剪, 细节请参阅`支持的图片操作（参数）`
* 区域裁剪, 细节请参阅`支持的图片操作（参数）`
* 索引裁剪, 细节请参阅`支持的图片操作（参数）`
* 旋转, 细节请参阅`支持的图片操作（参数）`




#### 图片信息响应体

包含至少以下字段的JSON对象:

| 名称 | 内容 |
|:--- | :--------------------------------- | 
| Width | 图片宽度，单位像素 |
| Height | 图片高度，单位像素 |
| Size | 图片文件尺寸，单位字节 |
| MimeType | 原图文件类型 |

##### 响应体示例

	{
		"Width":221,
		"Height":284,
		"Size":119540,
		"MimeType":"image/png"
	}




#### 图片主色调响应体

包含至少以下字段的JSON对象:

| 名称 | 内容 |
|:--- | :--------------------------------- | 
| RGB | RGB色彩空间下十六进制表示的图片主色调 |

##### 响应体示例

	{
		"RGB": 0x000000
	}




####`Processing parameters`样例

	* info
	* 100w_100h_100sh
	* 100w_100h_10q.png
	* 150w_1e|25q.jpg
	* watermark=1&type=c29uZ3Rp&size=40&text=SGVsbG8g5Zu-54mH5pyN5YqhIQ




搜索图片
--

**Host**:`{`Bucket`名称}.{区域访问域名}`

**路径**: `/v1/ImageMetadatas`

**参数**: 

请参阅[此处](https://www.elastic.co/guide/en/elasticsearch/reference/current/search.html)Elasticsearch文档。

**方法**: POST

**请求头**: 无

**请求体**:

描述请求的JSON对象

详情请参阅[此处](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl.html)Elasticsearch文档。

**请求体示例**

  {  
    "query": {
      "match": {
        "analysisResult.description.tags": {
          "query": "sunset"
        }
      }
    }
  }

**响应体**:  

JSON格式数组,由一一对应于查询结果的至少包含以下字段的JSON对象所构成:

| 名称 | 描述 |
|:--- | :----------------------------------------------------------------------------------------------------------- | 
| Id | 对应图片的ID,即其MD5散列值 |
| AnalysisResult | 对应图片的分析结果,其详情请参阅下方表格 |

`AnalysisResult`为包含至少以下字段的JSON对象:

| 名称 | 描述 |
|:--- | :----------------------------------------------------------------------------------------------------------- | 
| Metadata | 原图的基本信息，包含高度值`Height`, 宽度值`Width`和格式名称`Format`等字段的JSON格式对象 |
| Color | 原图的颜色分析结果, 包含16进制8位RGB色彩`AccentColor`,主要前景色名称`DominantColorForeground`， 主要背景色名称`DominantColorBackground`， 包含主要色彩名称的数组`DominantColors`以及表示图片是否为黑白图片的布尔值`IsBWImg`等字段的JSON格式对象 |
| Adult | 原图的内容分级分析结果, 包含标识原图是否为成人内容的布尔值`IsAdultContent`及其浮点数置信度`AdultScore`和表示原图是否包含不雅内容的布尔值`IsRacyContent`及其置信度`RacyScore`等字段的JSON格式对象 |
| Categories | 原图的分类分析结果，包含分类对象的JSON数组，其中每个分类对象为至少包含文本类别名`Name`及其浮点数置信度`Score`等字段的JSON对象 |
| Faces | 原图人脸识别结果，包含每个人脸对应描述对象的JSON数组，其中每个对象为包含推测年龄`Age`，推测性别`Gender`和人脸位置`FaceRectangle`等字段的JSON对象，其中`FaceRectangle`的内容为包含图上人脸宽度`Width`,高度`Height`，水平位置`Left`及垂直位置`Top`等字段的JSON对象 |
| Tags | 原图的分类分析结果，由每个标签对应JSON对象组成的JSON数组，其中每个元素至少包含标签名`Name`,浮点数置信度`Confidence`等字段 |
| Description | 原图内容的分析结果，包含由标签名组成的数组`Tags`和有描述对象组成的额数组`Captions`等字段组成的JSON对象，其中`Captions`字段内容的每个元素都为一个包含对图片内容进行描述的语句`Test`的字段的JSO对象 |


**响应体示例**:

	[
    {
      "Id":"xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
      "AnalysisResult":{
            "RequestId":"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
            "Metadata":
            {
                "Height":250,
                "Width":236,
                "Format":"Png"
            },
            "ImageType":null,
            "Color":
            {
              "AccentColor":"923D39",
              "DominantColorForeground":"Brown",
              "DominantColorBackground":"Black",
              "DominantColors": [ "Black","Brown" ],
              "IsBWImg":false
            },
            "Adult":
            {
                "IsAdultContent":false,
                "IsRacyContent":false,
                "AdultScore":0.0245902631431818,
                "RacyScore":0.015675213187932968
            },
            "Categories": 
            [
                {
                  "Name":"people_portrait",
                  "Score":0.98828125
                }
            ],
            "Faces": 
            [
                {
                  "Age":60,
                  "Gender":"Male",
                  "FaceRectangle":
                  {
                      "Width":164,"Height":164,"Left":31,"Top":66
                  }
                }
            ],
            "Tags":
            [
                {
                  "Name":"person","Confidence":0.99807929992675781,"Hint":null
                },
                {
                  "Name":"man","Confidence":0.98912441730499268,"Hint":null
                },
                {
                  "Name":"indoor","Confidence":0.981221616268158,"Hint":null
                },
                {
                  "Name":"wall","Confidence":0.95026427507400513,"Hint":null
                }
            ],
            "Description":
            {
              "Tags":
      				["person","man","indoor","looking","sitting","camera","glasses","wearing","suit",
                "shirt","smiling","holding","close","black","food","white","table","red","standing"],
              "Captions":
              [
                {
                    "Text":"a man is smiling at the camera","Confidence":0.36183552978964767
                }
      				]
            }
        }
    }
	]
	
<font style="color: red">此部分API的参数或调用方式可能在未来的版本中被修改</font>




错误响应
--

当出现错误时（无效的参数或不存在的图片），`Azure图片服务`将返回对应的错误代码和包含至少以下字段的JSON对象：

| 字段名 | 描述 | 
|:------|:-------------------- |
| error | 错误码 | 
| title | 错误名称 |
| detail | 错误详细信息 |
Hdya-0450277
**响应体示例**

	{
		"error": 404, 
		"title": "Bucket not found",
		"detail": "Unable to find bucket 'bucketnotexist'"
	}
	
可能的错误来源

| 来源 | 描述 | 错误码 / HTTP状态码 |
| ------ | ----------------------------------------------------- | ---------- |
| 参数错误 | 参数的格式错误，或包含了不存在的参数关键字或非法的参数值 | 400 |
| 图片尺寸过大 | 指定参数导致图片在处理过程中尺寸大于限制 | 400 |
| 未被授权 | 请求使用了错误的或没有对应权限的授权Token | 401 |
| `Bucket`错误 | 请求使用的`Bucket`名称非法或对应`Bucket`不存在 | 404 |
| 文件过大 | 操作所需的图片文件尺寸大于限制 | 413 |
| 内部错误 | 服务器出现了一个内部错误 | 500 |
| 方法未实现 | 请求的方法尚未被支持 | 501 |

<font style="color: red">此部分API的返回值可能在未来的版本中被修改</font>