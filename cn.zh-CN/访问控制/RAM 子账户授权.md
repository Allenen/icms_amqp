# RAM 子账户授权 {#concept_110522_zh .concept}

消息队列 AMQP 支持云账户（主账号）将资源授权给 RAM 用户（子账号），有权限的 RAM 用户可在消息队列 AMQP 的控制台上进行权限所允许范围内的资源管理，以及通过 SDK 发布与订阅消息。

关于 RAM 基本概念的说明，请参见[基本概念](../../../../cn.zh-CN/产品简介/基本概念.md#)。

关于授权相关的操作和用户组授权的概念，请参见[为 RAM 用户授权](../../../../cn.zh-CN/用户指南/用户/为 RAM 用户授权.md#)。

## 系统权限策略 {#section_mco_nbs_zet .section}

消息队列 AMQP 目前提供以下两种系统默认的授权策略：

|授权策略名称|说明|备注|
|------|--|--|
|AliyunAMQPFullAccess|管理消息队列 AMQP 的权限|等同于主账号的权限：具有所有消息收发权限且有控制台所有功能操作权限|
|AliyunAMQPReadOnlyAccess|消息队列 AMQP 的只读权限|被授予该权限的 RAM 用户对所有资源的只读权限|

## 自定义授权策略 {#section_gpe_5ad_8kl .section}

如果您需要有更细粒度的授权需求，可以通过创建自定义策略来进行访问控制。

**说明：** 对 Exchange 或 Queue 等资源进行操作的前提是要有这些资源所在 Vhost 的读权限（`amqp:GetVhost`）。

关于如何创建自定义策略的具体步骤，请参见[创建自定义策略](../../../../cn.zh-CN/用户指南/权限策略/自定义策略/创建自定义策略.md#)。

在创建自定义策略时，如需了解 RAM 权限策略语言的基本结构和语法，请参见[权限策略语法和结构](../../../../cn.zh-CN/用户指南/权限策略/权限策略语言/权限策略语法和结构.md#)。

为了方便您自定义 RAM 权限策略，本文提供了消息队列 AMQP 的授权映射表。

|API|RAM 授权操作（Action）|权限说明|资源（Resource）|
|---|----------------|----|------------|
|ListVhost|amqp:ListVhost|获取 Vhost 列表|acs:amqp:$region:$accountid:/vhosts/\*|
|CreateVhost|amqp:CreateVhost|创建 Vhost|acs:amqp:$region:$accountid:/vhosts/\*|
|DeleteVhost|amqp:DeleteVhost|删除 Vhost|acs:amqp:$region:$accountid:/vhosts/$vhostName|
|GetVhost|amqp:GetVhost|查看 Vhost|acs:amqp:$region:$accountid:/vhosts/$vhostName|
|ListExchange|amqp:ListExchange|获取 Exchange 列表|acs:amqp:$region:$accountid:/vhosts/$vhostName/exchanges/\*|
|CreateExchange|amqp:CreateExchange|创建 Exchange|acs:amqp:$region:$accountid:/vhosts/$vhostName/exchanges/\*|
|DeleteExchange|amqp:DeleteExchange|删除 Exchange|acs:amqp:$region:$accountid:/vhosts/$vhostName/exchanges/$exchangeName|
|GetExchange|amqp:GetExchange|查看 Exchange|acs:amqp:$region:$accountid:/vhosts/$vhostName/exchanges/$exchangeName|
|exchange.declare（`passive=false`）|amqp:CreateExchange|声明 Exchange，验证 Exchange 是否存在。如果 `passive` 参数设置为 `false` 且指定的 Exchange 不存在，则会新建 Exchange；如果已存在，则会校验该 Exchange 的信息是否正确；如果信息匹配，则会返回声明成功；如果信息不匹配，则会报错。|acs:amqp:$region:$accountid:/vhosts/$vhostName/exchanges/\*|
|exchange.declare（`passive=true`）|amqp:GetExchange|声明 Exchange，验证 Exchange 是否存在。如果 `passive` 参数设置为 `true` 且指定的 Exhange 已经存在，则会返回声明成功；如果不存在，则会报错。|acs:amqp:$region:$accountid:/vhosts/$vhostName/exchanges/$exchangeName|
|exchange.bind| -   amqp:GetExchange（源 Exchange）
-   amqp:CreateExchange（目标 Exchange）

 |将源 Exchange 绑定到目标 Exchange| -   acs:amqp:$region:$accountid:/vhosts/$vhostName/exchanges/$exchangeName（源 Exchange）
-   acs:amqp:$region:$accountid:/vhosts/$vhostName/exchanges/$exchangeName（目标 Exchange）

 |
|exchange.unbind| -   amqp:GetExchange（源 Exchange）
-   amqp:CreateExchange（目标 Exchange）

 |解除源 Exchange 到目标 Exchange 的绑定| -   acs:amqp:$region:$accountid:/vhosts/$vhostName/exchanges/$exchangeName（源 Exchange）
-   acs:amqp:$region:$accountid:/vhosts/$vhostName/exchanges/$exchangeName（目标 Exchange）

 |
|ListQueue|amqp:ListQueue|获取 Queue 列表|acs:amqp:$region:$accountid:/vhosts/$vhostName/queues/\*|
|CreateQueue|amqp:CreateQueue|创建 Queue|acs:amqp:$region:$accountid:/vhosts/$vhostName/queues/\*|
|DeleteQueue|amqp:DeleteQueue|删除 Queue|acs:amqp:$region:$accountid:/vhosts/$vhostName/queues/$queueName|
|GetQueue|amqp:GetQueue|查看 Queue|acs:amqp:$region:$accountid:/vhosts/$vhostName/queues/$queueName|
|queue.declare（`passive=false`）|amqp:CreateQueue|声明 Queue，验证 Queue 是否存在。如果 `passive` 参数设置为 `false` 且指定的 Queue 不存在，则会新建 Queue；如果已存在，则会校验该 Queue 的信息是否正确；如果信息匹配，则会返回声明成功；如果信息不匹配，则会报错。|acs:amqp:$region:$accountid:/vhosts/$vhostName/queues/\*|
|queue.declare（`passive=true`）|amqp:GetQueue|声明 Queue，验证 Exchange 是否存在。如果 `passive` 参数设置为 `true` 且指定的 Exhange 已经存在，则会返回声明成功；如果不存在，则会报错。|acs:amqp:$region:$accountid:/vhosts/$vhostName/queues/$queueName|
|queue.declare（有死信 Exchange）| -   amqp:CreateQueue
-   amqp:GetQueue
-   amqp:CreateExchange（死信 Exchange）

 |声明 Queue，且该 Queue 有绑定死信 Exchange| -   acs:amqp:$region:$accountid:/vhosts/$vhostName/queues/\*
-   acs:amqp:$region:$accountid:/vhosts/$vhostName/queues/$queueName
-   acs:amqp:$region:$accountid:/vhosts/$vhostName/exchanges/$exchangeName（死信 Exchange）

 |
|queue.bind| -   amqp:CreateQueue
-   amqp:GetExchange

 |绑定 Queue 到 Exchange| -   acs:amqp:$region:$accountid:/vhosts/$vhostName/queues/\*
-   acs:amqp:$region:$accountid:/vhosts/$vhostName/exchanges/$exchangeName

 |
|queue.unbind|amqp:CreateQueueamqp:GetExchange|解除 Queue 和 Exchange 间的绑定| -   acs:amqp:$region:$accountid:/vhosts/$vhostName/queues/\*
-   acs:amqp:$region:$accountid:/vhosts/$vhostName/exchanges/$exchangeName

 |
|BasicRecover|amqp:BasicRecover|重新投递没被 Consumer 确认消费（Ack）的消息|acs:amqp:$region:$accountid:/vhosts/$vhostName/queues/\*|
|BasicCancel|amqp:BasicCancel|取消订阅|acs:amqp:$region:$accountid:/vhosts/$vhostName/queues/$queueName/messages/\*|
|BasicPublish|amqp:BasicPublish|发布一条消息|acs:amqp:$region:$accountid:/vhosts/$vhostName/exchanges/$exchangeName/messages/\*|
|BasicConsume|amqp:BasicConsume|启动一个 Consumer|acs:amqp:$region:$accountid:/vhosts/$vhostName/queues/$queueName/messages/\*|
|BasicAck|amqp:BasicAck|确认消费一条或多条消息|acs:amqp:$region:$accountid:/vhosts/$vhostName/queues/$queueName/messages/\*|
|BasicNack|amqp:BasicNack|拒绝一条或多条消息|acs:amqp:$region:$accountid:/vhosts/$vhostName/queues/$queueName/messages/\*|
|BasicReject|amqp:BasicReject|拒绝一条消息|acs:amqp:$region:$accountid:/vhosts/$vhostName/queues/$queueName/messages/\*|
|QueuePurge|amqp:QueuePurge|清空一个 Queue 里的所有消息|acs:amqp:$region:$accountid:/vhosts/$vhostName/queues/$queueName/messages/\*|
|BasicGet|amqp:BasicGet|直接访问 Queue 的消息|acs:amqp:$region:$accountid:/vhosts/$vhostName/queues/$queueName/messages/\*|

## 自定义权限策略示例 {#section_45g_0ek_m98 .section}

本文提供了三种常见的授权场景，即需某个 Vhost 内的以下权限：

-   发布权限
-   订阅权限
-   发布和订阅权限

**示例一：自定义发布消息授权策略（在某个 Vhost 内）**

``` {#codeblock_982_5tb_snd}
{
    "Version": "1",
    "Statement": [
        {
            "Action": [
                "amqp:GetVhost"
            ],
            "Resource": "acs:amqp:*:*:/vhosts/$vhost",
            "Effect": "Allow"
        },
        {
            "Action": [
                "amqp:CreatExchange",
                "amqp:GetExchange",
                "amqp:CreateQueue",
                "amqp:GetQueue",
                "amqp:BasicRecover",
                "amqp:BasicPublish",
                "amqp:BasicAck",
                "amqp:BasicNack"
            ],
            "Resource": "acs:amqp:*:*:/vhosts/$vhost/*",
            "Effect": "Allow"
        }
    ]
}
```

**示例二：自定义订阅消息授权策略（在某个 Vhost 内）**

``` {#codeblock_1oz_r3g_bzb}
{
    "Version": "1",
    "Statement": [
        {
            "Action": [
                "amqp:GetVhost"
            ],
            "Resource": "acs:amqp:*:*:/vhosts/$vhost",
            "Effect": "Allow"
        },
        {
            "Action": [
                "amqp:CreateExchange",
                "amqp:GetExchange",
                "amqp:GetQueue",
                "amqp:CreateQueue",
                "amqp:BasicRecover",
                "amqp:BasicCancel",
                "amqp:BasicConsume",
                "amqp:BasicAck",
                "amqp:BasicNack",
                "amqp:BasicReject",
                "amqp:QueuePurge",
                "amqp:BasicGet"
            ],
            "Resource": "acs:amqp:*:*:/vhosts/$vhost/*",
            "Effect": "Allow"
        }
    ]
}
```

**示例三：自定义发布和订阅消息授权策略（在某个 Vhost 内）**

``` {#codeblock_7re_gv6_44y}
{
    "Version": "1",
    "Statement": [
        {
            "Action": [
                "amqp:GetVhost"
            ],
            "Resource": "acs:amqp:*:*:/vhosts/$vhost",
            "Effect": "Allow"
        },
        {
            "Action": [
                "amqp:ListExchange",
                "amqp:CreateExchange",
                "amqp:DeleteExchange",
                "amqp:GetExchange",
                "amqp:ListQueue",
                "amqp:DeleteQueue",
                "amqp:GetQueue",
                "amqp:CreateQueue",
                "amqp:BasicRecover",
                "amqp:BasicCancel",
                "amqp:BasicPublish",
                "amqp:BasicConsume",
                "amqp:BasicAck",
                "amqp:BasicNack",
                "amqp:BasicReject",
                "amqp:QueuePurge",
                "amqp:BasicGet"
            ],
            "Resource": "acs:amqp:*:*:/vhosts/$vhost/*",
            "Effect": "Allow"
        }
    ]
}
```

