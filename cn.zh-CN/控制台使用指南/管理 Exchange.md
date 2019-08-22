# 管理 Exchange {#concept_1460806 .concept}

本文向您介绍消息队列 AMQP 的 Exchange 的创建、删除、绑定、被绑定。

## 创建 Exchange {#section_93p_eg9_ize .section}

**前提条件**

您已完成以下操作：

-   [开通消息队列 AMQP 服务](../../../../cn.zh-CN/快速入门/开通消息队列 AMQP 服务.md#)
-   [创建 Vhost](../../../../cn.zh-CN/快速入门/创建资源.md#section_aqc_kem_4lu)

**操作步骤**

-   登录[消息队列 AMQP 控制台](https://amqp.console.aliyun.com/?spm=a2c4g.11186623.2.16.35f71e9a882si7)，在顶部菜单栏选择地域（Region）。
-   在左侧导航栏，单击 **Exchange 管理**。
-   在 Exchange 管理页面，选择 Vhost，单击**创建 Exchange**。
-   在创建 Exchange 对话框，填写 Exchange 的名称，选择 Exchange 的类型，选择是否为 **Internal**，然后单击**确认**。

完成后，您创建的 Exchange 将在 Exchange 管理页面的列表中显示。

## 删除 Exchange {#section_lda_vs5_3lo .section}

**前提条件**

您已完成以下操作：

-   [开通消息队列 AMQP 服务](../../../../cn.zh-CN/快速入门/开通消息队列 AMQP 服务.md#)
-   [创建 Vhost](../../../../cn.zh-CN/快速入门/创建资源.md#section_aqc_kem_4lu)
-   [创建 Exchange](#section_93p_eg9_ize)

**操作步骤**

-   登录[消息队列 AMQP 控制台](https://amqp.console.aliyun.com/?spm=a2c4g.11186623.2.16.35f71e9a882si7)，在顶部菜单栏选择地域（Region）。
-   在左侧导航栏，单击 **Exchange 管理**。
-   在 Exchange 管理页面，选择 Vhost，找到目标 Exchange，单击其右侧**操作**列中的**删除**。

    **说明：** 4 个内置的 Exchange 不可删除。

-   在弹出的提示框，单击**确定**。

完成后，该 Exchange 将不再在 Exchange 管理页面的列表中显示。

## 绑定 Exchange 或 Queue {#section_k7p_xoe_vvv .section}

**前提条件**

您已完成以下操作：

-   [开通消息队列 AMQP 服务](../../../../cn.zh-CN/快速入门/开通消息队列 AMQP 服务.md#)
-   [创建 Vhost](../../../../cn.zh-CN/快速入门/创建资源.md#section_aqc_kem_4lu)
-   [创建 Exchange](#section_93p_eg9_ize)
-   [创建 Queue](cn.zh-CN/控制台使用指南/管理 Queue.md#section_k0a_9iv_e2g)

**操作步骤**

-   登录[消息队列 AMQP 控制台](https://amqp.console.aliyun.com/?spm=a2c4g.11186623.2.16.35f71e9a882si7)，在顶部菜单栏选择地域（Region）。
-   在左侧导航栏，单击 **Exchange 管理**。
-   在 Exchange 管理页面，选择 Vhost，找到目标 Exchange，单击其右侧**操作**列中的**绑定关系**。
-   在该 Exchange 的绑定关系对话框，选择**绑定**，单击**添加绑定**，选择绑定目标类型，选择绑定目标，设置 Binding Key，然后单击**确认**。

    **说明：** 

    -   不支持中文的 Binding Key。
    -   普通类型的 Binding Key 只能包含字母、数字、下划线（\_）、中划线（-）、点号（.）、at 符号（@），长度限制在 1-255 字节之间。
    -   Topic 类型的 Binding Key 只能包含字母、数字、下划线（\_）、中划线（-）、点号（.）、at 符号（@）、星号（\*）、井号（\#），长度限制在 1-255 字节之间，支持以井号（\#）开头或结尾。

完成后，您绑定的 Queue 或 Exchange 将在该 Exchange 的绑定关系对话框中显示。

## 被 Exchange 绑定 {#section_3t3_few_bn7 .section}

**前提条件**

您已完成以下操作：

-   [开通消息队列 AMQP 服务](../../../../cn.zh-CN/快速入门/开通消息队列 AMQP 服务.md#)
-   [创建 Vhost](../../../../cn.zh-CN/快速入门/创建资源.md#section_aqc_kem_4lu)
-   [创建 Exchange](#section_93p_eg9_ize)

**操作步骤**

-   登录[消息队列 AMQP 控制台](https://amqp.console.aliyun.com/?spm=a2c4g.11186623.2.16.35f71e9a882si7)，在顶部菜单栏选择地域（Region）。
-   在左侧导航栏，**Exchange 管理**。
-   在 Exchange 管理页面，选择 Vhost，找到目标 Exchange，单击其右侧**操作**列中的**绑定关系**。
-   在该 Exchange 的绑定关系对话框，选择**被绑定**，单击**添加绑定**，选择源 Exchange，设置 Binding Key，然后单击**确认**。

    **说明：** 

    -   不支持中文的 Binding Key。
    -   普通类型的 Binding Key 只能包含字母、数字、下划线（\_）、中划线（-）、点号（.）、at 符号（@），长度限制在 1-255 字节之间。
    -   Topic 类型的 Binding Key 只能包含字母、数字、下划线（\_）、中划线（-）、点号（.）、at 符号（@）、星号（\*）、井号（\#），长度限制在 1-255 字节之间，支持以井号（\#）开头或结尾。

完成后，源 Exchange 将在该 Exchange 的绑定关系对话框的列表中显示。

