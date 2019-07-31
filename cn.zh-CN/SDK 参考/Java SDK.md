# Java SDK {#concept_106230_zh .concept}

消息队列 AMQP 支持使用多语言的 SDK 收发消息。本文围绕纯 Java 语言来提供如何添加依赖将 Java SDK 接入消息队列 AMQP，以及使用 Java SDK 收发消息的示例代码。

若需其他语言的 SDK 示例代码，请参见 [Demo 工程与 SDK 下载](cn.zh-CN/SDK 参考/Demo 工程与 SDK 下载.md#)。

## 1. 添加 Maven 依赖 {#section_r26_afo_58m .section}

请添加以下 Maven 依赖。

``` {#codeblock_gni_r4i_vkl .language-xml}
//客户端建议使用版本是 5.5.0
  <dependencies>
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
      <!-- https://mvnrepository.com/artifact/com.google.common/google-collect -->
      <dependency>
          <groupId>org.apache.commons</groupId>
          <artifactId>commons-lang3</artifactId>
          <version>3.4</version>
      </dependency>
  </dependencies>
			
```

`mq-amqp-client` 依赖的更多信息，请访问 [mq-amqp-client 源码](https://github.com/AliwareMQ/mq-amqp-client)。

## 2. 使用 Java SDK 收发消息 {#section_5cq_o2h_c8d .section}

**2.1 配置权限认证相关类**

示例代码如下所示。

``` {#codeblock_vx3_ywg_r5i .language-java}
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
     * security temp token. (optional)
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

**2.2 绑定 Binding Key**

示例代码如下所示。

``` {#codeblock_98b_yx7_p3a .language-java}
import com.rabbitmq.client.*;
import java.io.IOException;
import java.security.InvalidKeyException;
import java.security.NoSuchAlgorithmException;
import java.util.HashMap;
import java.util.concurrent.TimeoutException;
public class BindingKeyTest {
    public static void main(String[] args) throws IOException, TimeoutException, NoSuchAlgorithmException, InvalidKeyException {
        long timestamp = System.currentTimeMillis();
        ConnectionFactory factory = new ConnectionFactory();
        // 设置接入点，到控制台概览页面上查看获取
        factory.setHost("xxx.xxx.aliyuncs.com");
        // ${UID} 为资源 Owner 账户 ID 信息，单击控制台右上角客户头像进入账号管理查看
        // ${AccessKey} 阿里云身份验证，在阿里云服务器管理控制台创建
        // ${SecretKey} 阿里云身份验证，在阿里云服务器管理控制台创建
        //一定要这个才能自动恢复
        factory.setCredentialsProvider(new AliyunCredentialsProvider("${AccessKey}", "${SecretKey}", "${UID}"));
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
        channel.exchangeDeclare("${ExchangeName}", "${ExchangeType}", true);
        // 创建 ${QueueName}。Queue 可以在控制台创建，也可以用 API 创建
        channel.queueDeclare("${QueueName}", true, false, false, new HashMap<>());
        // Queue 与 Exchange 进行绑定，注册 BindingKeyTest
        channel.queueBind("${QueueName}", "${ExchangeName}", "${BindingKeyTest}");
        connection.close();
    }
}

			
```

**2.3 发送消息**

示例代码如下所示。

``` {#codeblock_e37_hxa_8b4 .language-java}
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
        factory.setCredentialsProvider(new AliyunCredentialsProvider("${AccessKey}", "${SecretKey}", "${UID}"));
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
            // BindingKey 根据业务需求填入相应的 BindingKey
            channel.basicPublish("${ExchangeName}", "BindingKey", true, null,
                    ("消息发送Body" + i).getBytes(StandardCharsets.UTF_8));
        }
        connection.close();
    }
}
```

**2.3 接收消息**

示例代码如下所示。

``` {#codeblock_z56_4b1_9y8}
import com.rabbitmq.client.*;
import java.io.IOException;
import java.security.InvalidKeyException;
import java.security.NoSuchAlgorithmException;
import java.util.HashMap;
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
        factory.setCredentialsProvider(new AliyunCredentialsProvider("${AccessKey}", "${SecretKey}", "${UID}"));
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
        final Channel channel = connection.createChannel();
        // 创建 ${ExchangeName}，该 Exchange 必须在控制台上已存在，并且 Exchange 的类型与控制台上的类型一致
        AMQP.Exchange.DeclareOk exchangeDeclareOk = channel.exchangeDeclare("${ExchangeName}", "${ExchangeType}", true, false, false, null);
        // 创建 ${QueueName} ，该 Queue 必须在控制台上已存在
        AMQP.Queue.DeclareOk queueDeclareOk = channel.queueDeclare("${QueueName}", true, false, false, new HashMap<String, Object>());
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

## 更多信息 {#section_pif_3b4_030 .section}

如需使用 Spring Boot 或者 Spring 框架，请访问 [Spring Boot](https://github.com/AliwareMQ/amqp-demos/tree/master/amqp-springboot-demo/SprintBootDemo) 和 [Spring](https://github.com/AliwareMQ/amqp-demos/tree/master/spring-rabbit-demo) 获取相关 Demo 示例代码和下载 SDK。

