# 接口规范
## JWT 的组成
- header
```json
{
    "typ": "JWT",
    "alg" : "HS256",
}
```

- payload
```json
{
    "issue": "ZeroHzzzz",      // 签发者
    "aud": "Eyemeow",          // 接收方
    "role": "student",         // 面向对象
    "issueAt": 1441593502,     // 签发时间, Unix时间戳
    "expiration": 1441594722,  // 过期时间
}
```

- signature

## tip
必须使用HttpOnly属性来防止Cookie被JavaScript读取，从而避免跨站脚本攻击（XSS攻击）。
```go
func main() {
    http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
        cookie := http.Cookie{
            Name:     "sessionid",
            Value:    "123456789",
            HttpOnly: true, // 设置HttpOnly属性
        }
        http.SetCookie(w, &cookie)
        w.Write([]byte("Cookie with HttpOnly attribute set"))
    })

    http.ListenAndServe(":8080", nil)
}
```


## 前端请求规范
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

## 后端返回数据
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
    - 采用前缀+错误码的形式构成。前缀有两种：F和B，分别代表Front-End和Back-End。
    - F -> Front-End
        - F500 表示前端未能正确发送请求
    - B -> Back-End
        - B200403 表示服务器成功接收到请求，但是可能由于未到规定时间而拒绝访问
        - B200404 表示服务器成功接收到请求，但是找不到请求的资源
        - B200500 表示服务器成功接收到请求，但是服务器内部发生错误

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
    - 一般来说，复杂的结构化数据的传输使用json，有文件需要上传的时候使用formdata格式进行传输



# commit message格式

## type(必须)

用于说明git commit的类别，只允许使用下面的标识。
docs: 文档

feat：新功能（feature）。

fix/to：修复bug，可以是QA发现的BUG，也可以是研发自己发现的BUG。

fix：产生diff并自动修复此问题。适合于一次提交直接修复问题
to：只产生diff不自动修复此问题。适合于多次提交。最终修复问题提交时使用fix
docs：文档（documentation）。

style：格式（不影响代码运行的变动）。

refactor：重构（即不是新增功能，也不是修改bug的代码变动）。

perf：优化相关，比如提升性能、体验。

test：增加测试。

chore：构建过程或辅助工具的变动。

revert：回滚到上一个版本。

merge：代码合并。

sync：同步主线或分支的Bug。

## scope(可选)

scope用于说明 commit 影响的范围，比如数据层、控制层、视图层等等，视项目不同而不同。

例如在Angular，可以是location，browser，compile，compile，rootScope， ngHref，ngClick，ngView等。如果你的修改影响了不止一个scope，你可以使用*代替。

## subject(必须)

subject是commit目的的简短描述，不超过50个字符。

建议使用中文（感觉中国人用中文描述问题能更清楚一些）。

结尾不加句号或其他标点符号。
根据以上规范git commit message将是如下的格式：
fix(DAO):用户查询缺少username属性 
feat(Controller):用户查询接口开发