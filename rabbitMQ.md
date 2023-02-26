## 简介

### 什么是消息队列

==消息指的是两个应用间传递的数据。==数据的类型有很多种形式，可能只包含文本字符串，也可能包含嵌入对象。

==消息队列(Message Queue)是在消息的传输过程中保存消息的容器。==
在消息队列中，通常有生产者和消费者两个角色。生产者只负责发送数据到消息队列，谁从消息队列中取出数据处理，他不管。
消费者只负责从消息队列中取出数据处理，他不管这是谁发送的数据。

![](https://mingtypora.oss-cn-beijing.aliyuncs.com/rabbitMq/mq1.png)

### Mq作用

**1）解耦**

假设有系统B、C、D都需要系统A的数据，于是系统A调用三个方法发送数据到B、C、D。这时，系统D不需要了，那就需要在系统A把相关的代码删掉。假设这时有个新的系统E需要数据，这时系统A又要增加调用系统E的代码。为了降低这种强耦合，就可以使用MQ，==系统A只需要把数据发送到MQ，其他系统如果需要数据，则从MQ中获取即可==

![](https://mingtypora.oss-cn-beijing.aliyuncs.com/rabbitMq/mqEffect1.png)

**2）异步**

一个客户端请求发送进来，系统A会调用系统B、C、D三个系统，同步请求的话，响应时间就是系统A、B、C、D的总和，也就是800ms。
如果使用MQ，系统A发送数据到MQ，然后就可以返回响应给客户端，不需要再等待系统B、C、D的响应，可以大大地提高性能。对于一些非必要的业务，比如发送短信，发送邮件等等，就可以采用MQ。

![](https://mingtypora.oss-cn-beijing.aliyuncs.com/rabbitMq/mqEffect2.png)

**3）削峰**

假设系统A在某一段时间请求数暴增，有5000个请求发送过来，系统A这时就会发送5000条SQL进入MySQL进行执行，MySQL对于如此庞大的请求当然处理不过来，MySQL就会崩溃，导致系统瘫痪。
如果使用MQ，系统A不再是直接发送SQL到数据库，而是把数据发送到MQ，MQ短时间积压数据是可以接受的，然后由消费者每次拉取2000条进行处理，防止在请求峰值时期大量的请求直接发送到MySQL导致系统崩溃。

![](https://mingtypora.oss-cn-beijing.aliyuncs.com/rabbitMq/mqEffect3.png)

### rabbitMq特点

RabbitMQ是一款使用Erlang语言开发的，实现AMQP(高级消息队列协议)的开源消息中间件

- 可靠性。支持持久化，传输确认，发布确认等保证了MQ的可靠性。
- 灵活的分发消息策略。这应该是RabbitMQ的一大特点。在消息进入MQ前由Exchange(交换机)进行路由消息。分发消息策略有：简单模式、工作队列模式、发布订阅模式、路由模式、通配符模式。
- 支持集群。多台RabbitMQ服务器可以组成一个集群，形成一个逻辑Broker。
- 多种协议。RabbitMQ支持多种消息队列协议，比如 STOMP、MQTT 等等。
- 支持多种语言客户端。RabbitMQ几乎支持所有常用编程语言，包括 Java、.NET、Ruby 等等。
- 可视化管理界面。RabbitMQ提供了一个易用的用户界面，使得用户可以监控和管理消息 Broker。
- 插件机制。RabbitMQ提供了许多插件，可以通过插件进行扩展，也可以编写自己的插件。

## 安装

1. 拉取

在[docker hub](https://hub.docker.com/)里搜索rabbitmq，选择带有mangement的版本

``` bash
docker pull rabbitmq:3.11.9-management
```

2. 安装

``` bash
docker run \
-d --name rabbitmq \
-p 5672:5672 \
-p 15672:15672 \
-v /root/rabbitmq:/var/lib/rabbitmq \
--hostname myRabbit \
-e RABBITMQ_DEFAULT_VHOST=my_vhost \
-e RABBITMQ_DEFAULT_USER=admin \
-e RABBITMQ_DEFAULT_PASS=169736 \
rabbitmq:3.11.9-management

#-p 指定服务运行的端口（5672：应用访问端口；15672：控制台Web端口号）
#--hostname  主机名（RabbitMQ的一个重要注意事项是它根据所谓的 “节点名称” 存储数据，默认为主机名）
#-e 指定环境变量，默认虚拟机名，默认用户名和默认用户名密码
```

## 集成springboot

使用低版本springboot，不然会有ConnectionFactory 和RabbitTemplate的自动装配、循环依赖等问题，如果要想在Spring Boot 2.7.0以上使用，则需要自己创建这些 bean。

### 生产者provider

1. 依赖

``` xaml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-amqp</artifactId>
</dependency>
```

2. 创建一个公共配置类，定义一些配置信息，比如队列主题，交换机名称，路由匹配键名称等

``` java
package com.config;

/**
 * 创建一个公共配置类，定义一些配置信息，比如队列主题，交换机名称，路由匹配键名称等
 */
public class RabbitMQConfig {
    //rabbitMq的队列主题名称
    public static final String  RABBITMQ_DEMO_TOPIC = "rabbitmqDemoTopic";
    //rabbitMq的DIRECT交换机名称
    public static final String  RABBITMQ_DEMO_DIRECT_EXCHANGE = "rabbitmqDemoDirectExchange";
    //rabbitMq的DIRECT交换机和队列绑定的匹配键 DirectRouting
    public static final String  RABBITMQ_DEMO_DIRECT_ROUTING = "rabbitmqDemoDirectRouting";
}
```

3. application.yml文件加上RabbitMQ的配置信息

``` yaml
server:
  port: 8080

spring:
  rabbitmq:
    host: 123.57.93.167
    port: 5672
    virtual-host: my_vhost
    username: admin
    password: 169736
    
#virtual-host是创建容器时自己设定的默认虚拟机名，在管理后台的admin里可以看到
```

4. 创建一个Direct交换机以及队列的配置类

``` java
@Configuration
public class DirectRabbitConfig {
    @Bean
    public Queue rabbitmqDemoDirectQueue() {
        /**
         * 1、name:    队列名称
         * 2、durable: 是否持久化
         * 3、exclusive: 是否独享、排外的。如果设置为true，定义为排他队列。则只有创建者可以使用此队列。也就是private私有的。
         * 4、autoDelete: 是否自动删除。也就是临时队列。当最后一个消费者断开连接后，会自动删除。
         * */
        return new Queue(RabbitMQConfig.RABBITMQ_DEMO_TOPIC, true, false, false);
    }

    @Bean
    public DirectExchange rabbitmqDemoDirectExchange() {
        //Direct交换机
        return new DirectExchange(RabbitMQConfig.RABBITMQ_DEMO_DIRECT_EXCHANGE, true, false);
    }

    @Bean
    public Binding bindDirect() {
        //链式写法，绑定交换机和队列，并设置匹配键
        return BindingBuilder
                //绑定队列
                .bind(rabbitmqDemoDirectQueue())
                //到交换机
                .to(rabbitmqDemoDirectExchange())
                //并设置匹配键
                .with(RabbitMQConfig.RABBITMQ_DEMO_DIRECT_ROUTING);
    }
}
```

5. 创建一个发送消息的Service类

``` java
@Service
public class RabbitMQServiceImpl implements RabbitMQService {
    //日期格式化
    private static SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");

    @Autowired
    private RabbitTemplate rabbitTemplate;
    @Override
    public String sendMsg(String msg) {
        try {
            String msgId = UUID.randomUUID().toString().replace("-", "").substring(0, 32);
            String sendTime = sdf.format(new Date());
            Map<String, Object> map = new HashMap<>();
            map.put("msgId", msgId);
            map.put("sendTime", sendTime);
            map.put("msg", msg);
            rabbitTemplate.convertAndSend(RabbitMQConfig.RABBITMQ_DEMO_DIRECT_EXCHANGE, RabbitMQConfig.RABBITMQ_DEMO_DIRECT_ROUTING, map);
            return "ok";
        } catch (Exception e) {
            e.printStackTrace();
            return "error";
        }
    }
}
```

6. 然后根据业务放在需要用的地方，比如定时任务，或者接口。这里就简单一点使用Controller层进行发送

``` java
@RestController
@RequestMapping("/mall/rabbitmq")
public class RabbitMQController {
    @Autowired
    private RabbitMQService rabbitMQService;
    //发送消息

    @PostMapping("/sendMsg")
    public String sendMsg(String msg){
        return rabbitMQService.sendMsg(msg);
    }
}
```

### 消费者consumer

其maven依赖，yml文件配置和生产者一样。
只需要创建一个类，@RabbitListener注解写上监听队列的名称

``` java
//{RabbitMQConfig.RABBITMQ_DEMO_TOPIC} 队列名称，可写多个
//RabbitMQConfig.RABBITMQ_DEMO_TOPIC 需要在maven里引入生产者的依赖
@Component
@RabbitListener(queues = {RabbitMQConfig.RABBITMQ_DEMO_TOPIC})
public class RabbitMQConsumer {
    @RabbitHandler
    public void process(Map map){
        System.out.println("消费者从rabbitMq服务端消费消息："+map.toString());
    }
}
```

启动生产者，然后在Postman里调用接口，即可发送一条消息
此时，在RabbitMQ后台里，就可以看到创建了一个队列，并且有一条待消费的消息

然后启动消费者，进行消费
这时候就会持续监听队列的消息，只要生产者发送一条消息到MQ，消费者就消费一条

### 生产者和消费者都创建队列

如果生产者还没有调用接口，发送消息，此时的MQ服务器里就还没有创建队列
启动消费者就会启动报错 `- no queue`

最好的方法是生产者和消费者都尝试创建队列

生产者的配置类里加上

``` java
//实现BeanPostProcessor类，使用Bean的生命周期函数
@Component
@Configuration
public class DirectRabbitConfig implements BeanPostProcessor {
    
    //这是创建交换机和队列用的rabbitAdmin对象
    @Resource
    private RabbitAdmin rabbitAdmin;
    
    //初始化rabbitAdmin对象
    @Bean
    public RabbitAdmin rabbitAdmin(ConnectionFactory connectionFactory) {
        RabbitAdmin rabbitAdmin = new RabbitAdmin(connectionFactory);
        // 只有设置为 true，spring 才会加载 RabbitAdmin 这个类
        rabbitAdmin.setAutoStartup(true);
        return rabbitAdmin;
    }
    
    //实例化bean后，也就是Bean的后置处理器
    @Override
    public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
        //创建交换机
        rabbitAdmin.declareExchange(rabbitmqDemoDirectExchange());
        //创建队列
        rabbitAdmin.declareQueue(rabbitmqDemoDirectQueue());
        return null;
    }
}
```

==这样启动生产者就会自动创建交换机和队列，不用等到发送消息才创建==



消费者代码里加上

``` java
@Component
//使用queuesToDeclare属性，如果不存在则会创建队列
@RabbitListener(queuesToDeclare = @Queue(RabbitMQConfig.RABBITMQ_DEMO_TOPIC))
public class RabbitDemoConsumer {
    //...省略
}
```

==这样，如果先启动消费者，如果不存队列，则会创建队列==



## RabbitMQ的组成

**1）组成部分**

- Broker：消息队列服务进程。此进程包括两个部分：Exchange和Queue。
- Exchange：消息队列交换机。==按一定的规则将消息路由转发到某个队列==。
- Queue：消息队列，存储消息的队列。
- Producer：消息生产者。生产方客户端将消息同交换机路由发送到队列中。
- Consumer：消息消费者。消费队列中存储的消息。

**2）工作流程**

![](https://mingtypora.oss-cn-beijing.aliyuncs.com/rabbitMq/rabbitMQCompose.png)

1. 消息生产者连接到RabbitMQ Broker，创建connection，开启channel。
2. 生产者声明交换机类型、名称、是否持久化等。
3. 生产者发送消息，并指定消息是否持久化等属性和routing key。
4. exchange收到消息之后，==根据routing key路由到跟当前交换机绑定的相匹配的队列里面==。
5. 消费者监听接收到消息之后开始业务处理。
