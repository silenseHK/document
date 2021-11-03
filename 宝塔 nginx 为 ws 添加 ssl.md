## 宝塔 nginx 为 ws 添加 ssl

#### 1.先添加http的ssl

#### 2.打开站点修改 -> 配置文件

```shell
## 在server之前添加以下内容
map $http_upgrade $connection_upgrade {
  default upgrade;
  ''      close;
}
upstream websocket {
  # 这里的localhost是映射本地服务器，也可以是外网ip，2345是我ws开启的端口。
  server 127.0.0.1:18308;
}
```

```shell
## 在#SSL-END后添加以下内容
location / { 
	proxy_pass http://127.0.0.1:18308;
	proxy_http_version 1.1;
	proxy_set_header Upgrade $http_upgrade;
	proxy_set_header Connection "Upgrade";
}
```

#### 3.原则上可以直接访问，如果不行可以重启nginx试一下

#### 4.访问时需要注意

> 请求的端口号是443而不是swoole的18308
>
> 例如：wss:ws.kiki12318.xyz:443/test

#### 5.访问的一些注意

> ```shell
> location =/ws {
> 	...
> }
> #这里面的访问需要用ws://域名/ws来访问
> ```



