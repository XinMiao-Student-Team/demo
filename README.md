# 接口规范
鉴权类型暂时定为JWT

前端请求规范：
```JSON
POST /xxx HTTP/1.1
Accept: application/prs.gohub.v2+json // 区分api版本和请求数据类型
Accept-Charset: utf-8
Content-Type: application/json;charset=utf-8
Content-Encoding: gzip
Content-Length: 1234
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/96.0.4664.110 Safari/537.36 // 标识客户端的软件类型、操作系统、版本号等信息，有助于服务器了解请求的来源
Cookie：
X-CSRF-Token：//验证请求来源
Cache-Control:
Timeout: 5000

{
  "x":1,
  "y":2
}  
```

后端返回数据：
```JSON
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Content-Encoding: gzip
// 限流
X-RateLimit-Limit :10000 最大访问次数
X-RateLimit-Remaining :9993 剩余的访问次数
X-RateLimit-Reset :1513784506 //到该时间点，访问次数会重置
{
    "code": 0,
    "info": {
        "message": "", // 给用户的提示信息
        "detail": ""   // 用于排查错误的详细错误信息,err
    },
    "data": {},
}
```

- 错误码`code`规范：
    - 
- http状态码
    - 200 OK - 对成功的 GET、PUT、PATCH 或 DELETE 操作进行响应。也可以被用在不创建新资源的 POST 操作上
    - 201 Created - 对创建新资源的 POST 操作进行响应。应该带着指向新资源地址的 Location 头
    - 202 Accepted - 服务器接受了请求，但是还未处理，响应中应该包含相应的指示信息，告诉客户端该去哪里查询关于本次请求的信息
    - 204 No Content - 对不会返回响应体的成功请求进行响应（比如 DELETE 请求）
    - 304 Not Modified - HTTP 缓存 header 生效的时候用
    - 400 Bad Request - 请求异常，比如请求中的 body 无法解析
    - 401 Unauthorized - 没有进行认证或者认证非法
    - 403 Forbidden - 服务器已经理解请求，但是拒绝执行它
    - 404 Not Found - 请求一个不存在的资源
    - 405 Method Not Allowed - 所请求的 HTTP 方法不允许当前认证用户访问
    - 410 Gone - 表示当前请求的资源不再可用。当调用老版本 API 的时候很有用
    - 415 Unsupported Media Type - 如果请求中的内容类型是错误的
    - 422 Unprocessable Entity - 用来表示校验错误
    - 429 Too Many Requests - 由于请求频次达到上限而被拒绝访问
    - 500 Internal Server Error - 服务器遇见了未知的内部错误
    - 501 Not Implemented - 服务器还未实现次功能。
    - 503 Service Unavailable - 服务器繁忙，暂时无法处理客户端的请求。


- 接口字段规范:
    - 对于资源的操作类型, 使用 HTTP 动词来指定, 减少接口 URL 的数量
    - 图片的 URL 返回完整的 URL
    - 时间字段建议同时返回时间戳的原始值(或 ISO 标准格式)和用于统一显示的格式化文本
    - 分页请求格式:`url?page=10&limit=100`
    - 接口传入传出字段名统一为小驼峰


