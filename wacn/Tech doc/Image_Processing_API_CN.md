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

也可以不创建新的Azure Storage账户，使用一个现有的。

#### 2. 上传原始图片

创建好Azure Storage账户之后，用户就可以将原始图片上传到Blob当中。

这里提供两种上传方式：

#####  - [通过Azure Storage API](https://www.azure.cn/documentation/articles/storage-dotnet-how-to-use-blobs/)

#####  - [Microsoft Azure Storage Explorer](http://storageexplorer.com/)


#### 3. 创建“图片处理”CDN加速节点

“图片处理”加速类型的CDN节点仅限于在[Azure新的管理门户](https://portal.azure.cn/)中创建


##### - 创建CDN Profile

![][1]

这一步的Azure订阅选择注意要和第一步创建Azure Storage账户所使用的订阅保持一致。
“Price Tier”选择上图所示的S1，S1包含新增加的“image processing”加速类型。

##### - 创建图片处理类型的CDN加速节点

![][2]

如上图所示，这一步中的加速类型选择“image processing acceleration”，storage account选择第一步当中所创建的Azure Storage账户。				

等这个CDN加速节点配置完成后，用户可以将自定义域名CNAME到Azure CDN平台。CNAME生效之后，就可以进行图片处理操作了。
后续的所有原始图片以及经过处理后的图片都是经过CDN加速的。

在进行接下来的具体图片处理之前，我们先来验证一下到目前为止的所有配置是否生效：

1.  假设用户的原始图片访问链接为：` http://yourstorageaccount.blob.core.chinacloudapi.cn/container_name/img_name.jpg ` （首先确认此链接可以访问）
2.  如果经过CDN加速的原始图片访问链接 （以上图中所使用的自定义域名为例） ` http://imgprocess.yourcompany.cn/container_name/img_name.jpg ` 可以被访问的话，可以确认所有配置生效。



图片处理
--

### 图片处理访问规则

通过如下URL进行访问：

`http://your_CDN_custom_domain/container_name/image_object?basic=<处理字符串>`

**your_CDN_custom_domain**: 用户用来创建图片处理加速类型CDN节点的自定义域名，如：`imgprocess.yourcompany.cn`

**container_name**:  用户原始图片所在的Azure Storage container名字

**image_object**: 待处理的原始图片名称，如：`image.jpg`

具体的`处理字符串`的解释如下。


### 图片处理语法


#### `处理字符串`的详细定义

`处理字符串`是若干个形如`{值}{参数关键字}`参数的组合，每个参数之间由`_`相互连接；

若需要进行格式转换则`Process String`必须以`.{目标格式扩展名}`结尾


#### 支持的基本图片操作（参数）


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
| 图像质量 | `{Degree}q`, 其中`{Degree}`为表示图像质量的整数值，取值0-100 | 当且仅当输出格式为`jpg/jpeg`时有效 |
| 格式转换 | `.{format}`, 其中`{format}`为目标格式 | 目前支持的格式包括`jpg`,`jpeg`,`png`,`webp`及`gif` |
| 渐进显示 | `{Setting}pr`, 其中`{Setting}`是一个取值0-1的整数，当其为1时，图像将随着加载的进行渐进呈现给用户 | 当且仅当输出格式为`jpg/jpeg`时有效 |
| 图片信息 | `info`, 返回JSON格式图片信息，详情请参阅下方对应小节 | 不得与其他参数共用 |
| 图片EXIF信息 | `exif`, 返回JSON格式图片EXIF信息，详情请参阅下方对应小节 | 不得与其他参数共用 |
| 图片主色调 | `imageAve`, 返回JSON格式图片主色调信息，详情请参阅下方对应小节 | 不得与其他参数共用 |
| 管道 | `{Process1}`&#124;`{Process2}`, 其中`Process1`与`Process2`均为`处理字符串`并且图片会先被按照`Process1`定义的操作流程进行处理，而后按照`Process2`所定义的操作流程进行处理 | `Process`可以为`图片信息``EXIF信息`及`图片主色调`以外的任何上述参数 |
| 水印 | 请参阅下方对应小节 | 不得与其他参数共同存在于管道的同一阶段中 |


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


#### 示例 （持续更新中）

假设 ` http://imgprocess.yourcompany.cn/container_name/img_name.jpg ` 是经过Azure CDN加速的原始图片URL

![][3]

##### 1. 将原始图片缩小到原来的60%：

` http://imgprocess.yourcompany.cn/container_name/img_name.jpg?basic=60p `

![][4]

##### 2. 将原始图片进行圆角矩形处理（圆角半径为20）：

` http://imgprocess.yourcompany.cn/container_name/img_name.jpg?basic=20-2ci `

![][5]

##### 3. 将原始图片先缩小到原来的60%，然后再进行圆角处理（圆角半径为20）：

` http://imgprocess.yourcompany.cn/container_name/img_name.jpg?basic=60p_20-2ci `

![][6]





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
| object | 图片水印所使用图片的文件名经过`Base64`编码的字符串 |

**注意** 

* 字段`watermark`为必需，且其必须为Query String中的第一个字段
* 被用于图片水印的图片必须与原始图片存在于同一个Azure Storage账户当中
* Base64的编码转换可以使用在线工具直接处理，比如[base64encode](https://www.base64encode.org/)

##### 水印类型

| 值 | 释义 |
| :---- | :---- |
| 1 | 图片水印 |
| 2 | 文字水印 |

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



##### 示例 （持续更新中）

假设水印图片文件存在用 `container_name/watermark.jpg`，其对应的Base64编码为 `Y29udGFpbmVyX25hbWUvd2F0ZXJtYXJrLmpwZw==`

##### 1. 将原始图片加入水印图片：

` http://imgprocess.yourcompany.cn/container_name/img_name.jpg?basic=watermark=1;p=3;s=50;object=Y29udGFpbmVyX25hbWUvd2F0ZXJtYXJrLmpwZw==`

![][7]

##### 2. 将原始图片先缩小到原来的60%，然后再进行圆角处理（圆角半径为20），最后加入图片水印（注意此处使用管道**|**操作符）：

` http://imgprocess.yourcompany.cn/container_name/img_name.jpg?basic=60p_20-2ci|watermark=1;p=3;s=50;object=Y29udGFpbmVyX25hbWUvd2F0ZXJtYXJrLmpwZw==`

![][8]



<!--Image references-->
[1]: ./images/imgp01.png
[2]: ./images/imgp02.png
[3]: ./images/azure.jpg
[4]: ./images/60p.jpg
[5]: ./images/20ci.jpg
[6]: ./images/60p_20ci.jpg
[7]: ./images/w1.jpg
[8]: ./images/w2.jpg