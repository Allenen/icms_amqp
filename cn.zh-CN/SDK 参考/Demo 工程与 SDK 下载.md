# Demo 工程与 SDK 下载 {#concept_113889_zh .concept}

本文提供消息队列 AMQP 的 Demo 示例代码以及 SDK 的下载地址，以此满足多语言或框架下的消息收发。

Demo 工程包含了消息队列 AMQP 的各种应用场景的使用指导，请参见程序源码的注释进行理解。

SDK 是通过在`pom.xml`文件中添加依赖来获取。

## Demo 和 SDK 下载地址 {#section_ttx_awt_0jt .section}

消息队列 AMQP 目前支持的语言和框架，以及这些语言和框架所对应的示例代码和 SDK 下载地址如下表所示。

|语言/框架|Demo 和 SDK 的下载地址|
|-----|----------------|
|.NET|[amqp-dotnet-demo](https://github.com/AliwareMQ/amqp-demos/tree/master/amqp-dotnet-demo)|
|Go|[amqp-go-utils](https://github.com/AliwareMQ/amqp-demos/tree/master/amqp-go-utils)|
|Node.js|[amqp-node-demo](https://github.com/AliwareMQ/amqp-demos/tree/master/amqp-node-demo)|
|PHP|[amqp-php-demo](https://github.com/AliwareMQ/amqp-demos/tree/master/amqp-php-demo)|
|Python|[amqp-python-demo](https://github.com/AliwareMQ/amqp-demos/tree/master/amqp-python-demo)|
|Java| -   [纯 Java](cn.zh-CN/SDK 参考/Java SDK.md#)
-   [Spring Boot](https://github.com/AliwareMQ/amqp-demos/tree/master/amqp-springboot-demo/SprintBootDemo)
-   [Spring](https://github.com/AliwareMQ/amqp-demos/tree/master/spring-rabbit-demo)

 |

## 参数说明 {#section_r2p_063_ge3 .section}

在使用 Demo 和 SDK 时，请注意以下几个参数的填写：

|参数|说明|
|--|--|
|accessKey|您账号的 AccessKeyId，在阿里云管理控制台上获取|
|accessSecret|您账号的 AccessKeySecret，在阿里云管理控制台上获取|
|endPoint|您的 Vhost 的接入点，需在消息队列 AMQP 控制台的概览页获取|
|resourceOwnerId|您的 UID，既可通过阿里云管理控制台上的**账号 ID** 字段获取，也可通过消息队列 AMQP 控制台的概览页查看接入点获取，例如，接入点为**138015630679XXXX.mq-amqp.cn-hangzhou-a.aliyuncs.com** ，则您的 UID 为 **138015630679XXXX**。|
|userName|将您账号的 AccessKeyId、AccessKeySecret 和 UID 信息通过 Base64 编码后生成的字符串，具体生成办法请参见多语言的 Demo 和 SDK 下载链接。|
|passWord|将您账号的 AccessKeySecret 和 `timestamp` 参数（系统当前时间）通过 HMAC-SHA1 生成一个签名后，再将这个签名和 `timestamp` 参数（系统当前时间）通过 Base64 编码后生成的字符串，具体生成办法请参见多语言的 Demo 和 SDK 下载链接。|

