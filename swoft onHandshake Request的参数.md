#### swoft onHandshake Request的参数

```
(
    [coRequest:protected] => Swoole\Http\Request Object
        (
            [fd] => 3
            [header] => Array
                (
                    [host] => 67.229.243.37:18308
                    [connection] => Upgrade
                    [pragma] => no-cache
                    [cache-control] => no-cache
                    [user-agent] => Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/87.0.4280.88 Safari/537.36
                    [upgrade] => websocket
                    [origin] => http://coolaf.com
                    [sec-websocket-version] => 13
                    [accept-encoding] => gzip, deflate
                    [accept-language] => zh-CN,zh;q=0.9
                    [sec-websocket-key] => 4Uoh0YFhh9w1O0UPH1CtaA==
                    [sec-websocket-extensions] => permessage-deflate; client_max_window_bits
                )

            [server] => Array
                (
                    [request_method] => GET
                    [request_uri] => /ping
                    [path_info] => /ping
                    [request_time] => 1609834219
                    [request_time_float] => 1609834219.3394
                    [server_protocol] => HTTP/1.1
                    [server_port] => 18308
                    [remote_port] => 33880
                    [remote_addr] => 117.136.63.173
                    [master_time] => 1609834219
                )

            [cookie] =>
            [get] =>
            [files] =>
            [post] =>
            [tmpfiles] =>
        )

    [attributes:protected] => Array
        (
        )

    [cookieParams:protected] => Array
        (
        )

    [parsedBody:Swoft\Http\Message\Request:private] =>
    [parsedQuery:Swoft\Http\Message\Request:private] =>
    [parsedPath:Swoft\Http\Message\Request:private] =>
    [queryParams:Swoft\Http\Message\Request:private] => Array
        (
        )

    [serverParams:Swoft\Http\Message\Request:private] => Array
        (
            [request_method] => GET
            [request_uri] => /ping
            [path_info] => /ping
            [request_time] => 1609834219
            [request_time_float] => 1609834219.3394
            [server_protocol] => HTTP/1.1
            [server_port] => 18308
            [remote_port] => 33880
            [remote_addr] => 117.136.63.173
            [master_time] => 1609834219
        )

    [uploadedFiles:Swoft\Http\Message\Request:private] => Array
        (
        )

    [uriPath:Swoft\Http\Message\Request:private] => /ping
    [uriQuery:Swoft\Http\Message\Request:private] =>
    [requestTime:Swoft\Http\Message\Request:private] => 0
    [parsers:Swoft\Http\Message\Request:private] => Array
        (
            [text/xml] => Swoft\Http\Server\Parser\XmlRequestParser Object
                (
                )

            [application/json] => Swoft\Http\Server\Parser\JsonRequestParser Object
                (
                )

        )

    [method:protected] => GET
    [requestTarget:protected] => /ping
    [uri:protected] => Swoft\Http\Message\Uri\Uri Object
        (
            [scheme:Swoft\Http\Message\Uri\Uri:private] =>
            [userInfo:Swoft\Http\Message\Uri\Uri:private] =>
            [host:Swoft\Http\Message\Uri\Uri:private] =>
            [port:Swoft\Http\Message\Uri\Uri:private] =>
            [path:Swoft\Http\Message\Uri\Uri:private] =>
            [query:Swoft\Http\Message\Uri\Uri:private] =>
            [fragment:Swoft\Http\Message\Uri\Uri:private] =>
            [params:Swoft\Http\Message\Uri\Uri:private] => Array
                (
                    [host] => 67.229.243.37:18308
                    [path] => /ping
                    [query] =>
                    [https] =>
                    [http_host] =>
                    [server_name] =>
                    [server_addr] =>
                    [server_port] => 18308
                )

        )

    [headers:protected] => Array
        (
            [host] => Array
                (
                    [0] => 67.229.243.37:18308
                )

            [connection] => Array
                (
                    [0] => Upgrade
                )

            [pragma] => Array
                (
                    [0] => no-cache
                )

            [cache-control] => Array
                (
                    [0] => no-cache
                )

            [user-agent] => Array
                (
                    [0] => Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/87.0.4280.88 Safari/537.36
                )

            [upgrade] => Array
                (
                    [0] => websocket
                )

            [origin] => Array
                (
                    [0] => http://coolaf.com
                )

            [sec-websocket-version] => Array
                (
                    [0] => 13
                )

            [accept-encoding] => Array
                (
                    [0] => gzip, deflate
                )

            [accept-language] => Array
                (
                    [0] => zh-CN,zh;q=0.9
                )

            [sec-websocket-key] => Array
                (
                    [0] => 4Uoh0YFhh9w1O0UPH1CtaA==
                )

            [sec-websocket-extensions] => Array
                (
                    [0] => permessage-deflate; client_max_window_bits
                )

        )

    [headerNames:protected] => Array
        (
            [host] => host
            [connection] => connection
            [pragma] => pragma
            [cache-control] => cache-control
            [user-agent] => user-agent
            [upgrade] => upgrade
            [origin] => origin
            [sec-websocket-version] => sec-websocket-version
            [accept-encoding] => accept-encoding
            [accept-language] => accept-language
            [sec-websocket-key] => sec-websocket-key
            [sec-websocket-extensions] => sec-websocket-extensions
        )

    [protocol:protected] =>
    [stream:protected] =>
)
```

