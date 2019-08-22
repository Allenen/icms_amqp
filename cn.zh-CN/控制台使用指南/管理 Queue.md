# 管理 Queue {#concept_1460825 .concept}

本文向您介绍消息队列 AMQP 的 Queue 的创建、删除和被绑定。

## 创建 Queue {#section_k0a_9iv_e2g .section}

**前提条件**

您已完成以下操作：

-   [开通消息队列 AMQP 服务](../../../../cn.zh-CN/快速入门/开通消息队列 AMQP 服务.md#)
-   [创建 Vhost](../../../../cn.zh-CN/快速入门/创建资源.md#section_aqc_kem_4lu)

**操作步骤**

-   登录[消息队列 AMQP 控制台](https://amqp.console.aliyun.com/?spm=a2c4g.11186623.2.16.35f71e9a882si7)，在顶部菜单栏选择地域（Region）。
-   在左侧导航栏，选择 **Queue 管理**。
-   在 Queue 管理页面，选择 Vhost，单击**创建 Queue**。
-   在创建 Queue对话框，填写 Queue 的名称，选择是否为 **Auto Delete** 类型，设置 **Arguments**，然后单击**确认**。

    **说明：** 

    -   **Queue**：消息队列。

        取值说明如下：

        -   Queue 名称只能包含字母、数字、短横线（-）和下划线（\_），长度限制在 1-255 字节之间。
        -   Queue 一旦创建将无法再修改，只能删除重建。
    -   **Auoto Delete**：在订阅该 Queue 消息的最后一个 Consumer 取消订阅该 Queue 的消息后，是否自动删除该 Queue。

        取值说明如下：

        -   “true”：在订阅该 Queue 消息的最后一个 Consumer 取消订阅该 Queue 的消息后，自动删除该 Queue。
        -   “false”：在订阅该 Queue 消息的最后一个 Consumer 取消订阅该 Queue 的消息后，不自动删除该 Queue。
    -   **Arguments**：针对 Queue 的参数设置，可用于设置死信 Exchange、死信 Routing Key 和消息过期时间。

        取值说明如下：

        -   “DeadLetterExchange”：指定死信消息发往的 Exchange。
        -   “DeadLetterRoutingKey”：指定死信消息的 Routing Key，即死信 Exchange 会将消息发往至匹配该死信 Routing Key 的 Binding Key 所对应的 Queue。
        -   “MessageTTL”：消息过期时间，单位毫秒（ms）。超过指定时间段内还未被消费的消息是死信消息，该消息将会被发往死信 Exchange。

完成后，您创建的 Queue 将在 Queue 管理页面的列表中显示。

## 删除 Queue {#section_gw0_iue_ejv .section}

**前提条件**

您已完成以下操作：

-   [开通消息队列 AMQP 服务](../../../../cn.zh-CN/快速入门/开通消息队列 AMQP 服务.md#)
-   [创建 Vhost](../../../../cn.zh-CN/快速入门/创建资源.md#section_aqc_kem_4lu)
-   [创建 Queue](#section_k0a_9iv_e2g)

**操作步骤**

-   登录[消息队列 AMQP 控制台](https://amqp.console.aliyun.com/?spm=a2c4g.11186623.2.16.35f71e9a882si7)，在顶部菜单栏选择地域（Region）。
-   在左侧导航栏，选择 **Queue 管理**。
-   在 Queue 管理页面，选择 Vhost，在目标 Queue 右侧的**操作**列中单击**删除**。
-   在弹出的提示框，单击**确认**。

    **说明：** 若该 Queue 中有未消费消息会同时被删除，且不可恢复，请谨慎操作。


完成后，您删除的 Queue 将不再在 Queue 管理页面的列表中显示。

## 被 Exchange 绑定 {#section_oai_qgk_9he .section}

**前提条件**

您已完成以下操作：

-   [开通消息队列 AMQP 服务](../../../../cn.zh-CN/快速入门/开通消息队列 AMQP 服务.md#)
-   [创建 Vhost](../../../../cn.zh-CN/快速入门/创建资源.md#section_aqc_kem_4lu)
-   [创建 Queue](#section_k0a_9iv_e2g)
-   [创建 Exchange](cn.zh-CN/控制台使用指南/管理 Exchange.md#section_93p_eg9_ize)

**操作步骤**

-   登录[消息队列 AMQP 控制台](https://amqp.console.aliyun.com/?spm=a2c4g.11186623.2.16.35f71e9a882si7)，在顶部菜单栏选择地域（Region）。
-   在左侧导航栏，选择 **Queue 管理**。
-   在 Queue 管理页面，选择 Vhost，找到目标 Queue，单击其右侧**操作**列中的**绑定关系**。
-   在该 Queue 的绑定关系对话框，单击**添加绑定**，选择源 Exchange，设置 Binding Key，然后单击**确认**。

    **说明：** 

    -   不支持中文的 Binding Key。
    -   普通类型的 Binding Key 只能包含字母、数字、下划线（\_）、中划线（-）、点号（.）、at 符号（@），长度限制在 1-255 字节之间。
    -   Topic 类型的 Binding Key 只能包含字母、数字、下划线（\_）、中划线（-）、点号（.）、at 符号（@）、星号（\*）、井号（\#），长度限制在 1-255 字节之间，支持以井号（\#）开头或结尾。

完成后，源 Exchange 将在该 Queue 的被绑定情况对话框的列表中显示。

