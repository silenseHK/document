[文档链接](https://www.bookstack.cn/read/swoft-doc-v2.x/ready-index.md)

### swoftcli运行websocket服务

```shell
swoftcli run -c ws:start -b bin/swoft
```

### 1.http服务

```php
在app\Http\Cpntroller中创建控制器文件
<?php
/**
 * Created by PhpStorm.
 * User: 27989
 * Date: 2020/12/24
 * Time: 11:07
 */

namespace App\Http\Controller;

use Swoft\Context\Context;
use Swoft\Http\Server\Annotation\Mapping\Controller;
use Swoft\Http\Server\Annotation\Mapping\Middleware;
use Swoft\Http\Server\Annotation\Mapping\RequestMapping;
use Swoft\Http\Server\Annotation\Mapping\RequestMethod;
use App\Http\Middleware\TestMiddleware;

/**
 * Class Product
 * @Controller(prefix="/product")
 * @Middleware(TestMiddleware::class)
 */
class Product
{
    /**
     * @RequestMapping(route="", method={RequestMethod::GET})
     */
    public function index()
    {
        $data = makeProduct('第一个商品',3);
//        return response()->withContentType("application/json")->withData([$data]);
        return [$data, $data, $data];
    }

    /**
     * @RequestMapping(route="{id}", method={RequestMethod::GET})
     */
    public function detail(int $id)
    {
//        $id = request()->get('id');
        return $id;
    }

    /**
     * @RequestMapping(route="check", method={RequestMethod::GET})
     */
    public function check()
    {
        $data = makeProduct('第二个商品',3);
        return $data;
    }

}
?>
```

##### 请求

```php
Swoft\Context\Context::get()->getRequest();
```

##### 响应

```php
Swoft\Context\Context::get()->getResponse();
```

### 事件

#### 监听事件

###### 1.Listener

>事件监听器类注解tag `@Listener`

> - 注解类： `Swoft\Event\Annotation\Mapping\Listener`
> - 作用范围： `CLASS`
> - 拥有属性：
>   - `event` *string* 要监听的事件名称
>   - `priority` *int* 此监听器的优先级，值越大越先被调用

> >注意你的类必须实现接口: `Swoft\Event\EventHandlerInterface`

###### 2.Subscriber

> 事件监听器类注解tag `@Subscriber`，**与 `@Listener` 不同的是，允许在同一个类里处理多个事件**。
>
> - 注解类： `Swoft\Event\Annotation\Mapping\Subscriber`
> - 作用范围： `CLASS`
>
> > 注意你的类必须实现接口: `Swoft\Event\EventSubscriberInterface`

#### 触发事件

```php
\Swoft::trigger('event name', null, $arg0, $arg1);
##或者
\Swoft::triggerByArray('event name', null,['arg0' => 'hello', 'arg1' => 'world']);
```

### 协程

```php
//一套多混合IO并发操作的方法，一般用于多个流程没有依赖，可以并发执行，提高执行效率
use Swoft\Co;
/**
 * Class CoTest
 *
 * @since 2.0
 */
class CoTest
{
    /**
     * @throws \ReflectionException
     * @throws \Swoft\Bean\Exception\ContainerException
     */
    public function testMulti()
    {
        $requests = [
            'method'       => [$this, 'requestMethod'],
            'staticMethod' => "SwoftTest\Unit\CoTest::requestMehtodByStatic",
            'closure'      => function () {
                $cli = new Client('www.baidu.com', 80);
                $cli->get('/');
                $result = $cli->body;
                $cli->close();
                return $result;
            }
        ];
        $response = Co::multi($requests);
    }
    /**
     * @return mixed
     */
    public function requestMethod()
    {
        $cli = new Client('www.baidu.com', 80);
        $cli->get('/');
        $result = $cli->body;
        $cli->close();
        return $result;
    }
    /**
     * @return mixed
     */
    public static function requestMehtodByStatic()
    {
        $cli = new Client('www.baidu.com', 80);
        $cli->get('/');
        $result = $cli->body;
        $cli->close();
        return $result;
    }
}
```

### RPC

**rpc和http区别**

> 由于HTTP在应用层中完成，整个通信的代价较高，远程过程调用中直接基于TCP进行远程调用，数据传输在传输层TCP层完成，更适合对效率要求比较高的场景，RPC主要依赖于客户端和服务端之间建立Socket链接进行，底层实现比REST更复杂。

>微服务调用不同模块

### 任务

##### 声明任务

```php
/**
 * Class TestTask
 *
 * @since 2.0
 *
 * @Task(name="testTask")
 */
class TestTask
{
    /**
     * @TaskMapping(name="list")
     *
     * @param int    $id
     * @param string $default
     *
     * @return array
     */
    public function getList(int $id, string $default = 'def'): array
    {
        return [
            'list'    => [1, 3, 3],
            'id'      => $id,
            'default' => $default
        ];
    }
    /**
     * @TaskMapping()
     *
     * @param int $id
     *
     * @return bool
     */
    public function delete(int $id): bool
    {
        if ($id > 10) {
            return true;
        }
        return false;
    }
}
```

###### 协程任务

```php
##单个投递
$result = Task::co('testTask', 'delete', [12]);

##批量投递
$tasks = [
    [
        'taskName',
        'method',
        ['params']
    ]
];
Task::cos(array $tasks, float $timeout = 3, array $ext = [])
```

###### 异步任务

```php
##投递
$data = Task::async('testTask', 'delete', [12]);
```

### 进程

> Process

- 安装

  ```
  composer require swoft/process
  ```

  > 进程分两种，用户进程，进程池进程

- - 用户进程

    > 必须继承**UserProcess**

    - 声明进程

    ```php
    <?php declare(strict_types=1);
    
    namespace App\Process;
    
    use App\Model\Logic\MonitorLogic;
    use Swoft\Bean\Annotation\Mapping\Bean;
    use Swoft\Bean\Annotation\Mapping\Inject;
    use Swoft\Db\Exception\DbException;
    use Swoft\Process\Process;
    use Swoft\Process\UserProcess;
    
    /**
     * Class MonitorProcess
     *
     * @since 2.0
     *
     * @Bean()
     */
    class MonitorProcess extends UserProcess
    {
        /**
         * @Inject()
         *
         * @var MonitorLogic
         */
        private $logic;
    
        /**
         * @param Process $process
         *
         * @throws DbException
         */
        public function run(Process $process): void
        {
            $this->logic->monitor($process);
        }
    }
    ```

    - 启动进程

      > 进程必须配置才会生效

      ```php
       return [
          'httpServer'     => [
                  'class'    => HttpServer::class,
                  'port'     => 18306,
                  'listener' => [
                      'rpc' => bean('rpcServer')
                  ],
                  'process' => [
                      'monitor' => bean(MonitorProcess::class)  //重点是这一行
                  ],
                  // ...
              ],
       ];
      ```

      > 如果配置成功，服务启动后，用户进程里面的业务会自动执行，无需其它操作。

- - 进程池

    >必须继承**ProcessInterface**

    - 声明进程

    ```php
    <?php
    
    
    namespace App\Process;
    
    
    use App\Lib\rabbitmq\TestConsumer;
    use Swoft\Process\Annotation\Mapping\Process;
    use Swoft\Process\Contract\ProcessInterface;
    use Swoft\Process\UserProcess;
    use Swoole\Process\Pool;
    
    /**
     * Class TestConsumerProcess
     * @Process(workerId={3,4,5})
     * @package App\Process
     */
    class TestConsumerProcess implements ProcessInterface
    {
    
        public function run(Pool $pool, int $workerId): void
        {
            $consumer = new TestConsumer('127.0.0.1','5672','guest','guest','exchange_1','queue_1','routing_key_1');
            $consumerName = rand(1000,9999);
            echo $workerId;
            $consumer->listen($consumerName);
        }
    
    }
    ```

    - 启动进程

    - - 进程池配置

        ```php
        ##app/bean.php
        return [
            'processPool' => [
                'class' => \Swoft\Process\ProcessPool::class,
                'workerNum' => 3
            ]
        ];
        /*
        	workerNum worker 进程数
        	ipcType IPC类型
        	coroutine 是否开启协程，默认是开启
        */
        ```

      > 进程数必须全部用完,否则报错

    - - 启动进程

        ```php
        ##前台启动
        php bin/swoft process:start
        
        ##后台启动
        bin/swoft process:start -d
            
        ## 重启进程
        php bin/swoft process:reload
            
        ## 停止服务
        php bin/swoft process:stop
        ```

        

### windows + docker 搭建 swoft 热更新环境

- 修改dockerfile8

  ```dockerfile
  CMD ["php","/var/www/swoft/swoftcli.phar", "run", "-c","http:start","-b", "/bin/swoft","-w","/"]
  ```

- 修改docker-compose.yml

  ```dockerfile
  command: ["php","/var/www/swoft/swoftcli.phar", "run", "-c","http:start","-b", "/bin/swoft","-w","/"]
  ```

- 下载swoftcli.phar

  [链接](https://github.com/swoft-cloud/swoft-cli)

  > 将**swoftcli.phar**放在swoft项目根目录下

- 启动swoft

  ```shell
  docker-compose up
  ```

> 热更新会比较慢



### 部署http服务

- 80端口监听其他端口

  > nginx增加location即可

  ```nginx
  location / {
      proxy_pass          http://127.0.0.1:18306;
      proxy_set_header    Host                $host:$server_port;
      proxy_set_header    X-Real-IP           $remote_addr;
      proxy_set_header    X-Real-PORT         $remote_port;
      proxy_set_header    X-Forwarded-For     $proxy_add_x_forwarded_for;
  }
  ```

  