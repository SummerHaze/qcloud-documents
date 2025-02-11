SDK 3.0是云 API 3.0平台的配套工具，您可以通过 SDK 使用所有 [语音消息 API](https://cloud.tencent.com/document/product/1128/51569)。新版 SDK 实现了统一化，具有各个语言版本的 SDK 使用方法相同，接口调用方式相同，错误码相同以及返回包格式相同等优点。
>!
>- 发送语音验证码
>只需提供验证码数字，如需自定义内容，可以 [发送语音通知](#SendTtsVoice)。例如，当 msg=“5678” 时，您收到的语音通知为`您的语音验证码是五六七八。`。
>- 发送语音通知
>数字默认按照个十百千万进行播报，可通过在数字前添加英文逗号（,）改变播报方式。例如，当 msg=`您的语音验证码是5678。` 时，您收到的语音通知为`您的语音验证码是五千六百七十八。`，当 msg=`您的语音验证码是5,6,7,8。` 时，您收到的语音通知为`您的语音验证码是五六七八。`。



## 前提条件

- 已开通语音消息服务，具体操作请参见 [快速入门](https://cloud.tencent.com/document/product/1128/37343)。
- 已准备依赖环境：Python 2.7, 3.6-3.9 版本。
- 已在访问管理控制台 >**[API密钥管理](https://console.cloud.tencent.com/cam/capi)**页面获取 SecretID 和 SecretKey。
 - SecretID 用于标识 API 调用者的身份。
 - SecretKey 用于加密签名字符串和服务器端验证签名字符串的密钥，**SecretKey 需妥善保管，避免泄露**。
- 语音消息的调用地址为 `vms.tencentcloudapi.com`。

## 相关资料
- 各个接口及其参数的详细介绍请参见 [API 文档](https://cloud.tencent.com/document/product/1128/51569)。
- 下载 SDK 源码请访问 [Python SDK 源码](https://github.com/TencentCloud/tencentcloud-sdk-python)。

## 安装 SDK

### 通过 pip 安装（推荐）
1. 下载并安装 [pip](https://pip.pypa.io/en/stable/installing/?spm=a3c0i.o32026zh.a3.6.74134958lLSo6o)。
2. 执行以下命令安装 SDK。
```bash
pip install tencentcloud-sdk-python
```

### 通过源码包安装
1. 前往 [Github 代码托管地址](https://github.com/tencentcloud/tencentcloud-sdk-python) 或 [快速下载地址](https://tencentcloud-sdk-1253896243.file.myqcloud.com/tencentcloud-sdk-python/tencentcloud-sdk-python.zip) 下载最新代码。
2. 解压后依次执行以下命令安装 SDK。
```
 $ cd tencentcloud-sdk-python
    $ python setup.py install
```

## 示例代码[](id:example)
>?所有示例代码仅作参考，无法直接编译和运行，需根据实际情况进行修改，您也可以根据实际需求使用 [API 3.0 Explorer](https://console.cloud.tencent.com/api/explorer?Product=vms&Version=2020-09-02&Action=SendCodeVoice) 自动化生成 Demo 代码。

>!
>- 建议用户使用子账号密钥 + 环境变量的方式调用 SDK，提高 SDK 使用的安全性。为子账号授权时，请遵循 [最小权限指引原则](https://cloud.tencent.com/document/product/436/38618)，防止泄漏目标存储桶或对象之外的资源。
- 如果您一定要使用永久密钥，建议遵循 [最小权限指引原则](https://cloud.tencent.com/document/product/436/38618) 对永久密钥的权限范围进行限制。

每个接口都有一个对应的 Request 结构和一个 Response 结构。示例代码如下所示。

### 发送语音验证码

```
# -*- coding: utf-8 -*-
from tencentcloud.common import credential
from tencentcloud.common.exception.tencent_cloud_sdk_exception import TencentCloudSDKException
# 导入 VMS 模块的 client models
from tencentcloud.vms.v20200902 import vms_client, models

# 导入可选配置类
from tencentcloud.common.profile.client_profile import ClientProfile
from tencentcloud.common.profile.http_profile import HttpProfile

try:
    # 必要步骤：
    # 实例化一个认证对象，入参需要传入腾讯云账户 secretId，secretKey
    # 本示例采用从环境变量读取的方式，需要预先在环境变量中设置这两个值
    # 您也可以直接在代码中写入密钥对，但需谨防泄露，不要将代码复制、上传或者分享给他人
    # CAM 密钥查询：https://console.cloud.tencent.com/cam/capi
    cred = credential.Credential("secretId", "secretKey")
    # cred = credential.Credential(
    #     os.environ.get(""),
    #     os.environ.get("")
    # )

    # 实例化一个 http 选项，可选，无特殊需求时可以跳过
    httpProfile = HttpProfile()
    httpProfile.reqMethod = "POST"  # POST 请求（默认为 POST 请求）
    httpProfile.reqTimeout = 30    # 请求超时时间，单位为秒（默认60秒）
    httpProfile.endpoint = "vms.tencentcloudapi.com"  # 指定接入地域域名（默认就近接入）

    # 非必要步骤:
    # 实例化一个客户端配置对象，可以指定超时时间等配置
    clientProfile = ClientProfile()
    clientProfile.signMethod = "TC3-HMAC-SHA256"  # 指定签名算法
    clientProfile.language = "en-US"
    clientProfile.httpProfile = httpProfile


    # 实例化 VMS 的 client 对象
    # 第二个参数是地域信息，可以直接填写字符串 ap-guangzhou，或者引用预设的常量
    client = vms_client.VmsClient(cred, "ap-guangzhou", clientProfile)

    # 实例化一个请求对象，根据调用的接口和实际情况，可以进一步设置请求参数
    # 您可以直接查询 SDK 源码确定 SendCodeVoiceRequest 有哪些属性可以设置
    # 属性可能是基本类型，也可能引用了另一个数据结构
    # 推荐使用 IDE 进行开发，可以方便的跳转查阅各个接口和数据结构的文档说明
    # 实例化一个请求对象
    req = models.SendCodeVoiceRequest()

    # 基本类型的设置:
    # SDK 采用的是指针风格指定参数，即使对于基本类型也需要用指针来对参数赋值
    # SDK 提供对基本类型的指针引用封装函数
    # 帮助链接：
    # 语音消息控制台：https://console.cloud.tencent.com/vms
    # vms helper：https://cloud.tencent.com/document/product/1128/37720

    # 验证码，仅支持填写数字，实际播报语音时，会自动在数字前补充语音文本"您的验证码是"
    req.CodeMessage = "xxxx"
    # 被叫手机号码，采用 e.164 标准，格式为+[国家或地区码][用户号码]
    # 例如：+8613711112222，其中前面有一个+号，86为国家码，13711112222为手机号
    req.CalledNumber = "+8613711112222"
    # 在 [语音控制台] 添加应用后生成的实际SdkAppid，示例如1400006666
    req.VoiceSdkAppid = "1400006666"
    # 播放次数，可选，最多3次，默认2次
    req.PlayTimes = 2
    # 用户的 session 内容，腾讯 server 回包中会原样返回
    req.SessionContext = "xxxx"

    # 通过 client 对象调用 SendCodeVoice 方法发起请求，注意方法名与请求的对象是对应的
    resp = client.SendCodeVoice(req)

    # 输出 json 格式的字符串回包
    print(resp.to_json_string())

except TencentCloudSDKException as err:
    print(err)
```

### 指定模板发送语音通知[](id:SendTtsVoice)

```
# -*- coding: utf-8 -*-
from tencentcloud.common import credential
from tencentcloud.common.exception.tencent_cloud_sdk_exception import TencentCloudSDKException
# 导入 VMS 模块的 client models
from tencentcloud.vms.v20200902 import vms_client, models

# 导入可选配置类
from tencentcloud.common.profile.client_profile import ClientProfile
from tencentcloud.common.profile.http_profile import HttpProfile

try:
    # 必要步骤：
    # 实例化一个认证对象，入参需要传入腾讯云账户 secretId，secretKey
    # 本示例采用从环境变量读取的方式，需要预先在环境变量中设置这两个值
    # 您也可以直接在代码中写入密钥对，但需谨防泄露，不要将代码复制、上传或者分享给他人
    # CAM 密钥查询：https://console.cloud.tencent.com/cam/capi
    cred = credential.Credential("AKIDonWyGLFi3EsPlGcg68KJp43zau0xxxxx", "ThjhNq68uTSv7GCsGSP6ZorjKhuxxxxx")
    # cred = credential.Credential(
    #     os.environ.get(""),
    #     os.environ.get("")
    # )

    # 实例化一个 http 选项，可选，无特殊需求时可以跳过
    httpProfile = HttpProfile()
    httpProfile.reqMethod = "POST"  # POST 请求（默认为 POST 请求）
    httpProfile.reqTimeout = 30    # 请求超时时间，单位为秒（默认60秒）
    httpProfile.endpoint = "vms.tencentcloudapi.com"  # 指定接入地域域名（默认就近接入）

    # 非必要步骤:
    # 实例化一个客户端配置对象，可以指定超时时间等配置
    clientProfile = ClientProfile()
    clientProfile.signMethod = "TC3-HMAC-SHA256"  # 指定签名算法
    clientProfile.language = "en-US"
    clientProfile.httpProfile = httpProfile


    # 实例化 VMS 的 client 对象
    # 第二个参数是地域信息，可以直接填写字符串 ap-guangzhou，或者引用预设的常量
    client = vms_client.VmsClient(cred, "ap-guangzhou", clientProfile)

    # 实例化一个请求对象，根据调用的接口和实际情况，可以进一步设置请求参数
    # 您可以直接查询 SDK 源码确定 SendCodeVoiceRequest 有哪些属性可以设置
    # 属性可能是基本类型，也可能引用了另一个数据结构
    # 推荐使用 IDE 进行开发，可以方便的跳转查阅各个接口和数据结构的文档说明
    # 实例化一个请求对象
    req = models.SendTtsVoiceRequest()

    # 基本类型的设置:
    # SDK 采用的是指针风格指定参数，即使对于基本类型也需要用指针来对参数赋值
    # SDK 提供对基本类型的指针引用封装函数
    # 帮助链接：
    # 语音消息控制台：https://console.cloud.tencent.com/vms
    # vms helper：https://cloud.tencent.com/document/product/1128/37720

    # 模板 ID，必须填写在控制台审核通过的模板 ID，可登录 [语音消息控制台] 查看模板 ID
    req.TemplateId = "4356"
    # 模板参数，若模板没有参数，请提供为空数组
    req.TemplateParamSet = ["7652"]
    # 被叫手机号码，采用 e.164 标准，格式为+[国家或地区码][用户号码]
    # 例如：+8613711112222，其中前面有一个+号，86为国家码，13711112222为手机号
    req.CalledNumber = "+8613711112222"
    # 在语音控制台添加应用后生成的实际SdkAppid，示例如1400006666
    req.VoiceSdkAppid = "1400006666"
    # 播放次数，可选，最多3次，默认2次
    req.PlayTimes = 2
    # 用户的 session 内容，腾讯 server 回包中会原样返回
    req.SessionContext = "xxxx"

    # 通过 client 对象调用 SendTtsVoice 方法发起请求，注意方法名与请求的对象是对应的
    resp = client.SendTtsVoice(req)

    # 输出 json 格式的字符串回包
    print(resp.to_json_string())

except TencentCloudSDKException as err:
    print(err)
```
