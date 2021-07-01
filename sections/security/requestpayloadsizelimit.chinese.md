# 使用反向代理或中间件限制负载大小

### 一段解释

解析请求正文（例如 JSON 编码的payloads）是一项性能繁重的操作，尤其是对于较大的请求。
在您的 Web 应用程序中处理传入请求时，您应该限制其各自有效负载的大小。传入请求由于拒绝服务中断或其他不需要的副作用，无限的主体/有效负载大小可能会导致您的应用程序性能不佳或崩溃。
许多流行的解析请求体的中间件解决方案，例如已经包含的用于 express 的 `body-parser` 包，暴露了限制请求有效负载大小的选项，使开发人员可以轻松实现此功能。如果支持，你也可以在您的反向代理/网络服务器软件中集成请求正文大小限制。 以下是使用限制请求大小的示例 `express` 和/或 `nginx`。

### `express` 的示例代码

```javascript
const express = require('express');

const app = express();

app.use(express.json({ limit: '300kb' })); // body-parser 默认的 body 大小限制为 100kb

// 使用 json 请求
app.post('/json', (req, res) => {

    // 检查请求内容类型是否与 json 匹配，因为 body-parser 不检查内容类型
    if (!req.is('json')) {
        return res.sendStatus(415); // -> 如果请求没有 JSON 正文，则返回不支持的媒体类型
    }

    res.send('Hooray, it worked!');
});

app.listen(3000, () => console.log('Example app listening on port 3000!'));
```

🔗 [**Express express.json()文档**](http://expressjs.com/en/4x/api.html#express.json)

### `nginx` 的示例配置

```nginx
http {
    ...
    # 将所有传入请求的正文大小限制为 1 MB
    client_max_body_size 1m;
}

server {
    ...
    # 将此特定服务器块的传入请求的正文大小限制为 1 MB
    client_max_body_size 1m;
}

location /upload {
    ...
    # 将此路由的传入请求的正文大小限制为 1 MB
    client_max_body_size 1m;
}
```

🔗 [**Nginx client_max_body_size文档 **](http://nginx.org/en/docs/http/ngx_http_core_module.html#client_max_body_size)