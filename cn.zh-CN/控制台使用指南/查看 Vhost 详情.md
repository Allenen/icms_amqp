# 查看 Vhost 详情 {#concept_122896_zh .concept}

在使用消息队列 AMQP 发布和订阅消息后，可以在控制台上查看对应 Vhost 的详情，了解消息的发布和订阅详情。

## 前提条件 {#section_z2b_5t2_q4g .section}

-   所需查询的 Vhost 下的 Connection 已启动。


## 操作步骤 {#section_8zw_i8i_ayo .section}

1.  登录[消息队列 AMQP 控制台](https://amqp.console.aliyun.com)。在顶部导航栏，选择需查询的 Vhost 所在地域，如**华北2（上海）**。

2.  在左侧导航栏，单击 **Vhost 详情**。

3.  在 Vhost 详情页面，单击需查询的 Vhost 名称，即可看到相关信息。

    ![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/122896/cn_zh/1561545949593/%E5%8F%91%E9%80%81%E8%AF%A6%E6%83%85.png)

    图中所涉及的参数说明如下：

    -   **Connection**：Connection 的具体 URL。Connection 的含义请参见[名词解释](../cn.zh-CN/产品简介/名词解释.md#)。

    -   **Accesskey**：您账户的 AccessKeyId。

    -   **State**：当前 Connection 的服务状态。

    -   **SSL/TLS**：是否启用 SSL/TLS 连接。

    -   **Protocol**：当前使用的 AMQP 协议版本。

    -   **Channels**：该 Connection 下的 Channel 数量。Channel 的含义请参见[名词解释](../cn.zh-CN/产品简介/名词解释.md#)。

    -   **From Client（条/分钟）**：消息在 Consumer 中消费的速度。

    -   **To Client（条/分钟）**：消息从 Producer 发布的速度。

4.  （**可选**）在任一 Connection 所在行的**操作**列，单击**查看详情**。

    -   消息发布的 Connection 详情

        ![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/122896/cn_zh/1561546317407/%E5%8F%91%E9%80%81%E8%AF%A6%E6%83%85%20-%20%E5%89%AF%E6%9C%AC.png)

    -   消息订阅的 Connection 详情

        ![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/122896/cn_zh/1561546416589/%E6%B6%88%E8%B4%B9%E8%AF%A6%E6%83%85.png)

    其中涉及的参数说明如下：

    -   **Channel**：Channel 的具体 URL。Channel 的含义请参见[名词解释](../cn.zh-CN/产品简介/名词解释.md#)。
    -   **State**：当前 Channel 的服务状态。
    -   **Prefetch**：AMQP 0-9-1 协议通过指定 basic.qos 来实现消息消费中，限制某个 Channel（或 Connection）的 Unack 的消息数量。
    -   **Unacked**：Consumer 未返回 Ack 的消息条数。
    -   **Publish（条/分钟）**：消息从 Producer 中发布的速度。Confirm（条/分钟）：Consumer 确认消费的消息。
    -   **Get（条/分钟）**：Consumer 通过 GET 方式订阅的消息的消费速度。
    -   **Deliver（条/分钟）**：消息队列 AMQP 投递消息的速度。
    -   **Ack（条/分钟）**：Consumer 返回 Ack 的消息条数。

## 更多信息 {#section_9dc_bm8_7tr .section}

-   [查询消息](cn.zh-CN/控制台使用指南/查询消息.md#)
-   [监控报警](cn.zh-CN/控制台使用指南/监控报警.md#)

