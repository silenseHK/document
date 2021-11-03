### 安装

**采用docker安装**

- 获取镜像

  ```
  #指定版本，该版本包含了web控制页面
  docker pull rabbitmq:management
  ```

- 运行镜像

  ```shell
  #方式一：默认guest 用户，密码也是 guest
  docker run -d --hostname my-rabbit --name rabbit -p 15672:15672 -p 5672:5672 rabbitmq:management
  
  #方式二：设置用户名和密码
  docker run -d --hostname my-rabbit --name rabbit -e RABBITMQ_DEFAULT_USER=user -e RABBITMQ_DEFAULT_PASS=password -p 15672:15672 -p 5672:5672 rabbitmq:management
  ```

- 访问可视化界面

  ```
  http://localhost:15672/
  ```

### PHP代码

**先下载php-amqplib**

```shell
composer require php-amqplib/php-amqplib -vvv
```

##### producer

```php
<?php

require_once __DIR__.'/vendor/autoload.php';

use PhpAmqpLib\Connection\AMQPStreamConnection;
use PhpAmqpLib\Message\AMQPMessage;
use PhpAmqpLib\Exchange\AMQPExchangeType;

##连接
$conn = new AMQPStreamConnection('127.0.0.1','5672','guest','guest');

##创建通道
$channel = $conn->channel();

$exchangeName = 'exchange_1';

##创建交换机
/**
 * exchange 交换机名
 * type 交换机类型
 * passive 如果设置true则存在返回OK,否则就报错;设置false存在返回OK,不存在则自动创建
 * durable true是持久化 设置false是存放在内存中,rabbitMQ重启后会丢失
 * auto_delete 是否自动删除,当最后一个消费者断开连接之后队列是否自动被删除
 */
$channel->exchange_declare($exchangeName,AMQPExchangeType::DIRECT,false,true,false);

$queueName = 'queue_1';

/**
 * queue 队列名
 * passive 如果设置true则存在返回OK,否则就报错;设置false存在返回OK,不存在则自动创建
 * durable true是持久化 设置false是存放在内存中,rabbitMQ重启后会丢失
 * exclusive 是否排他,指定该选项为true,则队列支队当前连接有效,连接断开后自动删除
 * auto_delete 是否自动删除,当最后一个消费者断开连接之后队列是否自动被删除
 */
##创建队列
$channel->queue_declare($queueName,false,true,false,false);

##路由键
$routingKey = 'routing_key_1';

##绑定交换机和队列
$channel->queue_bind($queueName, $exchangeName, $routingKey);

##推送成功
$channel->set_ack_handler(function(AMQPMessage $message){
    echo "Message acked with content " . $message->body . PHP_EOL;
});

##推送失败
$channel->set_nack_handler(function(AMQPMessage $message){
    echo "Message nacked with content " . $message->body . PHP_EOL;
});

##创建信息
$data = [
    'msg_id' => uniqid(),
    'create_time' => time(),
    'notify_url' => '127.0.0.1/shop/notify'
];
$message = new AMQPMessage(json_encode($data), ['delivery_mode'=>AMQPMessage::DELIVERY_MODE_NON_PERSISTENT]);

##发布消息
$channel->basic_publish($message, $exchangeName, $routingKey);
```

##### consumer

```php
<?php

require_once __DIR__.'/vendor/autoload.php';

use PhpAmqpLib\Connection\AMQPStreamConnection;
use PhpAmqpLib\Message\AMQPMessage;
use PhpAmqpLib\Exchange\AMQPExchangeType;

##连接
$conn = new AMQPStreamConnection('127.0.0.1','5672','guest','guest');

##创建通道
$channel = $conn->channel();

$exchangeName = 'exchange_1';

##创建交换机
/**
 * exchange 交换机名
 * type 交换机类型
 * passive 如果设置true则存在返回OK,否则就报错;设置false存在返回OK,不存在则自动创建
 * durable true是持久化 设置false是存放在内存中,rabbitMQ重启后会丢失
 * auto_delete 是否自动删除,当最后一个消费者断开连接之后队列是否自动被删除
 */
$channel->exchange_declare($exchangeName,AMQPExchangeType::DIRECT,false,true,false);

$queueName = 'queue_1';

/**
 * queue 队列名
 * passive 如果设置true则存在返回OK,否则就报错;设置false存在返回OK,不存在则自动创建
 * durable true是持久化 设置false是存放在内存中,rabbitMQ重启后会丢失
 * exclusive 是否排他,指定该选项为true,则队列支队当前连接有效,连接断开后自动删除
 * auto_delete 是否自动删除,当最后一个消费者断开连接之后队列是否自动被删除
 */
##创建队列
$channel->queue_declare($queueName,false,true,false,false);

##路由键
$routingKey = 'routing_key_1';

##绑定交换机和队列
$channel->queue_bind($queueName, $exchangeName, $routingKey);

##获取队列中的消息,绑定监听
/**
 * queue 被消费的队列名称
 * consumer_tag 消费者客户端身份标识,用于区分多个客户端
 * no_local 这个功能属于AMQP的标准,但是RabbitMQ并没有做实现
 * no_ack 收到消息后,是否不需要回复确认即被认为被消费
 * exclusive 是否排他,即这个队列只能由一个消费者消费.适用于任务不允许进行并发处理的情况
 * nowait 不返回执行结果,但是如果排他开启的话,则必须需要等待结果的.如果两个一起开就会报错
 * callback 回调逻辑处理函数
 */
while(true){
    $channel->basic_consume($queueName,'test',false,false,false,false, function($message){
        ##操作
        var_dump($message->body);
        ##确认消费信息
        $message->delivery_info['channel']->basic_ack($message->delivery_info['delivery_tag']);//响应ack
    });
    ##监听
    while($channel->is_consuming()){
        $channel->wait(); //阻塞消费
    }
}
```

#### 