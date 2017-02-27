Azure CDN 图片服务 RESTful API 文档
==

对应API版本`v1`




简介
--

Azure CDN图片服务是由Azure CDN服务提供的一个可靠、安全且经济的图片处理服务。

通过Azure CDN图片服务，用户可以利用Azure Storage和Azure CDN服务，在任何时间、从任何位置和设备获取存储在Microsoft Azure上的图片或对其进行过一些处理后的版本。




### 服务说明

Azure CDN图片服务是作为Azure CDN服务的一个增值功能引入的，所以使用Azure CDN图片服务的前提是首先创建一个名为“图片处理”加速类型的CDN加速节点。**图片服务本身无法作为一个单独Azure服务来使用。**

除此之外，用户需要使用一个Azure Storage账户的Blob服务来上传原始待处理的图片。Azure CDN图片服务会用此服务来访问用户的原始图片。

目前Azure CDN图片服务没有提供相应的图形化管理界面，相应的CDN加速节点创建好之后，所有的图片处理请求目前都是通过RESTful API的形式来提供访问。



#### 限制

##### 1. 支持格式转换的文件格式包括 jpg, png, bmp, webp, gif
##### 2. 图片源文件的大小不可大于20MB。
##### 3. 处理后的图片尺寸不得大于 4096 * 4096像素, 且任意边边长不得大于 4096*4像素


### 服务创建流程


#### 1. 创建Azure Storage 账户

创建Azure Storage账户请参见 [创建存储账户](https://www.azure.cn/documentation/articles/storage-create-storage-account/)

#### 2. 上传原始图片

创建好Azure Storage账户之后，用户就可以将原始图片上传到Blob当中。

这里提供两种上传方式：

#####  - [通过Azure Storage API](https://www.azure.cn/documentation/articles/storage-dotnet-how-to-use-blobs/)

#####  - [Microsoft Azure Storage Explorer](http://storageexplorer.com/)


#### 3. 创建“图片处理”CDN加速节点



				



图片处理
--

### 图片处理访问规则

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