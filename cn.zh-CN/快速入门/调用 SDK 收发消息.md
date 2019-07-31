# 调用 SDK 收发消息 {#concept_102057_zh .concept}

在创建完所有资源之后，便可调用消息队列 AMQP 的 SDK 收发消息。本文以 Java SDK 的 Demo 作为示例，提供调用 SDK 收发消息的流程以及相应代码示例。

若需其他语言或 Spring 框架下的示例代码，请参见 [Demo 工程与 SDK 下载](../../../../cn.zh-CN/SDK 参考/Demo 工程与 SDK 下载.md#)。

## 收发消息流程 {#section_si4_7bf_8bq .section}

调用消息队列 AMQP 的 Java SDK 收发消息的流程如[图 1](#fig_gqn_1eo_cw6) 所示。

![](images/53933_zh-CN.png "收发消息流程")

## 前提条件 {#section_cyg_x9r_zro .section}

您已创建调用 SDK 收发消息所需的所有资源。创建资源的具体操作，请参见[创建资源](cn.zh-CN/快速入门/创建资源.md#)。

## 获取接入点 {#section_si3_h0n_fg7 .section}

接入点会在配置 Binding Key、调用 Java SDK 收发消息时使用。

在控制台创建好资源后，需按以下步骤通过控制台获取 Producer 和 Consumer 的接入点。

1.  在[消息队列 AMQP 控制台](https://amqp.console.aliyun.com/?spm=a2c4g.11186623.2.10.23071e9aqGE54H)的左侧导航栏，单击**概览**。

2.  在概览页面右上方，单击**获取接入点**。

3.  在弹出的接入点对话框内单击**复制**即可获取接入点。


## 引入依赖 {#section_qsz_jv4_k4v .section}

可通过以下任一方式引入依赖：

-   Maven 方式

    ``` {#codeblock_434_nci_j3n .language-xml}
          <dependency>
                <groupId>com.rabbitmq</groupId>
                <artifactId>amqp-client</artifactId>
                <version>5.5.0</version>
          </dependency>
          <dependency>
               <groupId>com.alibaba.mq-amqp</groupId>
               <artifactId>mq-amqp-client</artifactId>
               <version>1.0.4</version>
          </dependency>
    ```

    `mq-amqp-client` 依赖的更多信息，请访问 [mq-amqp-client 源码](https://github.com/AliwareMQ/mq-amqp-client)。

-   下载依赖 JAR 包方式

## 配置 CredentialsProvider {#section_lyk_qlj_zll .section}

需配置 `CredentialsProvider` 类（Class）完成身份认证。

``` {#codeblock_53x_q49_k6y .language-java}
import com.alibaba.mq.amqp.utils.UserUtils;
import com.rabbitmq.client.impl.CredentialsProvider;
import org.apache.commons.lang3.StringUtils;
import java.security.InvalidKeyException;
import java.security.NoSuchAlgorithmException;

/**
 * 阿里云 UserName、Password 生成类（动态变化）
 */
public class AliyunCredentialsProvider implements CredentialsProvider {
    /**
     * Access Key ID.
     */
    private final String accessKeyId;
    /**
     * Access Key Secret.
     */
    private final String accessKeySecret;
    /**
     * (Optional) Security temp token.
     */
    private final String securityToken;
    /**
     * 资源 Owner 账号（主账号）
     */
    private final long resourceOwnerId;

    public AliyunCredentialsProvider(final String accessKeyId, final String accessKeySecret,
                                     final long resourceOwnerId) {
        this(accessKeyId, accessKeySecret, null, resourceOwnerId);
    }

    public AliyunCredentialsProvider(final String accessKeyId, final String accessKeySecret,
                                     final String securityToken, final long resourceOwnerId) {
        this.accessKeyId = accessKeyId;
        this.accessKeySecret = accessKeySecret;
        this.securityToken = securityToken;
        this.resourceOwnerId = resourceOwnerId;
    }

    @Override
    public String getUsername() {
        if(StringUtils.isNotEmpty(securityToken)) {
            return UserUtils.getUserName(accessKeyId, resourceOwnerId, securityToken);
        } else {
            return UserUtils.getUserName(accessKeyId, resourceOwnerId);
        }
    }

    @Override
    public String getPassword() {
        try {
            return UserUtils.getPassord(accessKeySecret);
        } catch (InvalidKeyException e) {
            //todo
        } catch (NoSuchAlgorithmException e) {
            //todo
        }
        return null;
    }
}
```

## 配置 Binding Key {#section_lzw_mzg_z8q .section}

以下是以 Java SDK 发送消息为例进行说明。

``` {#codeblock_1of_23m_5h2 .language-java}
import com.google.common.collect.Maps;
import com.rabbitmq.client.*;

import java.io.IOException;
import java.security.InvalidKeyException;
import java.security.NoSuchAlgorithmException;
import java.util.concurrent.TimeoutException;

public class BindingKeyTest {
    public static void main(String[] args) throws IOException, TimeoutException, NoSuchAlgorithmException, InvalidKeyException {
        long timestamp = System.currentTimeMillis();
        ConnectionFactory factory = new ConnectionFactory();
        // 设置接入点，到控制台概览页面上查看获取
        factory.setHost("xxx.xxx.aliyuncs.com");
        // ${UID} 为资源 Owner 账户 ID 信息，点击在控制台右上角客户头像进入账号管理查看
        // ${AccessKey} 阿里云身份验证，在阿里云服务器管理控制台创建  
        // ${SecretKey} 阿里云身份验证，在阿里云服务器管理控制台创建
        //一定要这个才能自动恢复
        factory.setCredentialsProvider(new AliyunCredentialsProvider(${AccessKey}, "${SecretKey}", "${UID}"));
        factory.setAutomaticRecoveryEnabled(true);
        factory.setNetworkRecoveryInterval(5000);

        // 设置 Vhost 名称，请确保已在控制台上创建完成
        factory.setVirtualHost("${VhostName}");
        // 固定端口
        factory.setPort(5672);
        factory.setConnectionTimeout(300 * 1000);
        factory.setHandshakeTimeout(300 * 1000);
        factory.setShutdownTimeout(0);
        Connection connection = factory.newConnection();
        Channel channel = connection.createChannel();

        // 创建 ${ExchangeName}
        //Exchange 可以在控制台创建，也可以用 API 创建。如果控制台已存在，则 Exchange 的类型必须和控制台一致
        channel.exchangeDeclare("${ExchangeName}", "${ExchangeType}"， true);

        // 创建 ${QueueName}。Queue 可以在控制台创建，也可以用 API 创建
        channel.queueDeclare("${QueueName}", true, false, false, Maps.newHashMap());

        // Queue 与 Exchange 进行绑定，注册 BindingKeyTest
        channel.queueBind("${QueueName}", "${ExchangeName}", "${BindingKeyTest}");

        connection.close();
    }
}
```

## 发送消息 {#section_x0h_l0l_ydm .section}

以下是以 Java SDK 发送消息为例进行说明。

``` {#codeblock_hxt_lhz_atk .language-java}
import com.rabbitmq.client.Channel;
import com.rabbitmq.client.Connection;
import com.rabbitmq.client.ConnectionFactory;

import java.io.IOException;
import java.nio.charset.StandardCharsets;
import java.security.InvalidKeyException;
import java.security.NoSuchAlgorithmException;
import java.util.concurrent.TimeoutException;

public class ProducerTest {

    public static void main(String[] args) throws IOException, TimeoutException, NoSuchAlgorithmException, InvalidKeyException {
        ConnectionFactory factory = new ConnectionFactory();
        // 设置接入点，到控制台概览页面上查看获取
        factory.setHost("xxx.xxx.aliyuncs.com");
        // ${UID} 为资源 Owner 账户 ID 信息，单击控制台右上角客户头像进入账号管理查看
        // ${AccessKey} 阿里云身份验证，在阿里云服务器管理控制台创建  
        // ${SecretKey} 阿里云身份验证，在阿里云服务器管理控制台创建
        //一定要这个才能自动恢复
        factory.setCredentialsProvider(new AliyunCredentialsProvider(${AccessKey}, "${SecretKey}",       "${UID}"));
        factory.setAutomaticRecoveryEnabled(true);
        factory.setNetworkRecoveryInterval(5000);

        // 设置 Vhost 名称，请确保已在控制台上创建完成
        factory.setVirtualHost("${VhostName}");
        // 默认端口，非加密端口 5672，加密端口 5671
        factory.setPort(5672);
        //基于网络环境合理设置超时时间
        factory.setConnectionTimeout(30 * 1000);
        factory.setHandshakeTimeout(30 * 1000);
        factory.setShutdownTimeout(0);
        Connection connection = factory.newConnection();
        Channel channel = connection.createChannel();

        // 开始发送消息
        for (int i = 0; i < 100; i++) {
            // ${ExchangeName} 必须在控制台上已存在，并且 Exchange 的类型与控制台上的类型一致
            // Binding Key 根据业务需求填入相应的 Binding Key
            channel.basicPublish("${ExchangeName}", "BindingKey", true, null,
                    ("消息发送Body" + i).getBytes(StandardCharsets.UTF_8));
        }
        connection.close();

    }
}
```

**结果验证**

消息发送后，您可以在控制台查看消息发送状态，步骤如下：

1.  在消息队列 AMQP 控制台的左侧导航栏，单击**Queue 管理**。
2.  在 Queue 管理页面，在搜索输入框内输入需查看的 Queue，单击**搜索**。
3.  在搜索结果中查看**堆积数量**，即可看到消息发送到服务器端的情况。

**说明：** 此步骤演示的是第一次使用消息队列 AMQP 的场景，此时 Consumer 从未启动过，所以消息状态显示暂无消费数据。要启动 Consumer 并订阅消息请继续下一步订阅消息。

## 订阅消息 {#section_y26_bfn_2kp .section}

以下是以 Java SDK 发送消息为例进行说明。

``` {#codeblock_2bv_rax_lum}
import com.google.common.collect.Maps;
import com.rabbitmq.client.*;

import java.io.IOException;
import java.security.InvalidKeyException;
import java.security.NoSuchAlgorithmException;
import java.util.concurrent.TimeoutException;

public class ConsumerTest {

    public static void main(String[] args) throws IOException, TimeoutException, NoSuchAlgorithmException, InvalidKeyException {
        long timestamp = System.currentTimeMillis();
        ConnectionFactory factory = new ConnectionFactory();
        // 设置接入点，到控制台概览页面上查看获取
        factory.setHost("xxx.xxx.aliyuncs.com");
         // ${UID} 为资源 Owner 账户 ID 信息，单击控制台右上角客户头像进入账号管理查看
        // ${AccessKey} 阿里云身份验证，在阿里云服务器管理控制台创建  
        // ${SecretKey} 阿里云身份验证，在阿里云服务器管理控制台创建
        //一定要这个才能自动恢复
        factory.setCredentialsProvider(new AliyunCredentialsProvider(${AccessKey}, "${SecretKey}", "${UID}"));
        factory.setAutomaticRecoveryEnabled(true);
        factory.setNetworkRecoveryInterval(5000);

        // 设置 Vhost 名称，请确保已在控制台上创建完成
        factory.setVirtualHost("${VhostName}");
        // 固定端口
        factory.setPort(5672);
        factory.setConnectionTimeout(300 * 1000);
        factory.setHandshakeTimeout(300 * 1000);
        factory.setShutdownTimeout(0);
        Connection connection = factory.newConnection();
        Channel channel = connection.createChannel();

        // 创建 ${ExchangeName}，该 Exchange 必须在控制台上已存在，并且 Exchange 的类型与控制台上的类型一致
        AMQP.Exchange.DeclareOk exchangeDeclareOk = channel.exchangeDeclare("${ExchangeName}", "${ExchangeType}", true, false, false, null);

        // 创建 ${QueueName} ，该 Queue 必须在控制台上已存在
        AMQP.Queue.DeclareOk queueDeclareOk = channel.queueDeclare("${QueueName}", true, false, false, Maps.newHashMap());

        // Queue 与 Exchange 进行绑定，并确认绑定的 BindingKeyTest
        AMQP.Queue.BindOk bindOk = channel.queueBind("${QueueName}", "${ExchangeName}", "BindingKeyTest");

        // 开始消费消息
        channel.basicConsume("${QueueName}", false, "ConsumerTag", new DefaultConsumer(channel) {
            @Override
            public void handleDelivery(String consumerTag, Envelope envelope,
                                       AMQP.BasicProperties properties, byte[] body)
                    throws IOException {
                //接收到的消息，进行业务逻辑处理
                System.out.println("Received： " + new String(body, "UTF-8") + ", deliveryTag: " + envelope.getDeliveryTag());
                channel.basicAck(envelope.getDeliveryTag(), false);
            }
        });
        connection.close();
    }
}
```

## 结果验证 {#section_cxb_3c5_8ti .section}

完成上述步骤后，您可以在控制台查看 Consumer 是否启动成功，即消息是否订阅成功。

1.  在消息队列 AMQP 控制台左侧导航栏，单击 **Queue 管理**。

2.  在 Queue 管理页面，在搜索输入框内输入需查看的 Queue，单击**搜索**。

3.  在搜索结果中查看**上次消费时间**，即可看到消费的上次消息时间（最新被消费的消息发布到服务器的时间）。


完成以上所有步骤后，您就成功接入了消息队列 AMQP 服务，可以继续使用消息队列 AMQP 发送和订阅消息。

