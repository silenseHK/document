#### 执行服务

```shell
php artisan serve
```



#### 新建路由模块

* 在routes文件下新建路由文件

* 注册路由文件 App/Http/Providers/RouteServiceProvider.php

  ```php
  public function map()
  {
      Route::prefix('open')//路由前缀
      ->middleware('open')//路由组
      ->namespace("{$this->namespace}\\Open")//命名空间
      ->group(base_path('routes/open.php'));//路由文件路径
      //...其他路由代码忽略
  }
  ```

#### 路由定义

```php
Route::group(['prefix'=>'admin'], function(){
    Route::get('/','AdminController@lists');
    Route::get('/{id}','AdminController@first');
    Route::post('/', 'AdminController@add');
    Route::post('/{id}','AdminController@edit');
    Route::put('/{id}','AdminController@update');
    Route::delete('/{id}','AdminController@delete');
});
## 注意：默认的命名空间是从App开始的，可以在路由的服务提供者中添加全局的
```

#### trait

* 创建一个trait类文件

  ```php
  <?php
  
  
  namespace App\Http\Traits;
  
  
  trait ApiReturn
  {
  
      public $code = 200;
  
      public $msg = '';
  
      public $data = [];
  
      public function apiReturn($code=0, $msg='', $data=[])
      {
          if(!$code)
              $code = $this->code;
          if(!$msg)
              $msg = $this->msg;
          if(is_array($data) && empty($data))
              $data = $this->data;
          return response()->json(compact('code','msg','data'));
      }
  
  }
  ```

* 在需要使用的文件中引入，就可以直接使用了

  ```php
  use ApiReturn
  ```

#### 添加自定义类和公共方法文件

* 在app下添加Helpers/functions.php文件 和 Libs文件夹

  ![例如](https://i.imgur.com/wyTSCIn.png)

* 在composer.json中

  ```json
  "autoload": {
          "psr-4": {
              "App\\": "app/",
              "Database\\Factories\\": "database/factories/",
              "Database\\Seeders\\": "database/seeders/"
          },
          "files": [
              "app/Helpers/functions.php"
          ],
          "classmap": [
              "app/Libs"
          ]
      },
  ```

* 执行代码，更新autoload

  ```shell
  composer dump-autoload
  ```

#### 自定义异常捕获

* 在app\Exception中创建类文件

  ```php
  <?php
  
  
  namespace App\Exceptions;
  
  class BaseException extends \Exception
  {
  
      public $code = 0;
  
      public $message = '';
  
      public function __construct($params)
      {
          if (!is_array($params)) {
              return ;
          }
          if (array_key_exists('code', $params)) {
              $this->code = $params['code'];
          }
          if (array_key_exists('msg', $params)) {
              $this->message = $params['msg'];
          }
      }
  
  }
  
  ```

* 在同目录下的Handler.php中修改render方法(没有该方法就增加)

  ```php
  ##下面就是捕获到了异常之后的处理，怎么处理都可以
  public function render($request, Throwable $exception)
      {
          if ($exception instanceof BaseException) { 
              // 如果是自定义的异常
              $this->code    = $exception->code;
              $this->message = $exception->message;
          } else {
              // 定义异常开关
              if (config('app.debug')) {
                  return parent::render($request, $exception);
              }
  
              $this->code    = 0;
              $this->message = '服务器内部错误';
              // todo 记录日志 和 消息通知（邮件短信等）
              Log::error($exception->getMessage());
          }
  
          $result = [
              'code'  => $this->code,
              'msg'   => $this->message,
          ];
  
          // 显示中文
          return response()->json($result)->setEncodingOptions(JSON_UNESCAPED_UNICODE);
      }
  ```


#### 模型

```php
const UPDATED_AT = '字段名'  //设置更新时间字段
const UPDATED_AT = null //不自动添加更新时间

//设置时间戳返回的时间格式
protected function serializeDate(\DateTimeInterface $date)
{
	return $date->format('Y-m-d H:i:s');
}

//判断模型查询的数据是否为空
->paginate() || ->get()
$data->isEmpty()
->first() || ->find()
!$data

```

#### 为request增加属性

```php
##增加属性
request()->atrribute->add(['key'=>'value']);
##取出属性
request()->get('key');
```

