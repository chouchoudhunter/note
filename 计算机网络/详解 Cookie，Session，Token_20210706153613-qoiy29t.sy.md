# 前言

## 无状态的 HTTP 协议

很久很久之前， Web 基本都是文档的浏览而已。既然是浏览， 作为服务器， 不需要记录在某一段时间里都浏览了什么文档， 每次请求都是一个新的 HTTP 协议，就是请求加响应。不用记录谁刚刚发了 HTTP 请求， 每次请求都是全新的。

## 如何管理会话

随着交互式 Web 应用的兴起， 像在线购物网站，需要登录的网站等，马上面临一个问题，就是要管理回话，记住那些人登录过系统，哪些人往自己的购物车中放商品，也就是说我必须把每个人区分开。

本文主要讲解 cookie，session, token 这三种是如何管理会话的；

# cookie

cookie 是一个非常具体的东西，指的就是浏览器里面能永久存储的一种数据。跟服务器没啥关系，仅仅是浏览器实现的一种数据存储功能。

cookie 由服务器生成，发送给浏览器，浏览器把 cookie 以 KV 形式存储到某个目录下的文本文件中，下一次请求同一网站时会把该 cookie 发送给服务器。由于 cookie 是存在客户端上的，所以浏览器加入了一些限制确保 cookie 不会被恶意使用，同时不会占据太多磁盘空间。所以每个域的 cookie 数量是有限制的。

## 如何设置

### 客户端设置

```javascript
document.cookie = "name=xiaoming; age=12 "
```

* 客户端可以设置 cookie 的一下选项： expires, domain, path, secure(只有在 https 协议的网页中， 客户端设置 secure 类型 cookie 才能生效), 但无法设置 httpOnly 选项

> 设置 cookie => cookie 被自动添加到 request header 中 => 服务端接收到 cookie

### 服务端设置

不管你是请求一个资源文件(如 html/js/css/图片), 还是发送一个 AJAX 请求， 服务端都会返回 response.而 response header 中有一项叫`set-cookie`, 是服务端专门用来设置 cookie 的;

* 一个 set-cookie 只能设置一个 cookie, 当你想设置多个， 需要添加同样多的`set-cookie`
* 服务端可以设置 cookie 的所有选项： expires, domain, path, secure, HttpOnly

### Cookie，SessionStorage，LocalStorage

HTML5 提供了两种本地存储的方式 sessionStorage 和 localStorage；

![img](assets\1)

# session

## 什么是 session

session 从字面上讲，就是会话。这个就类似你和一个人交谈，你怎么知道当时和你交谈的是张三而不是李四呢？对方肯定有某种特征（长相等）表明他是张三；
session 也是类似的道理，服务器要知道当前请求发给自己的是谁。为了做这种区分，服务器就是要给每个客户端分配不同的"身份标识"，然后客户端每次向服务器发请求的时候，都带上这个”身份标识“，服务器就知道这个请求来自与谁了。
至于客户端怎么保存这个”身份标识“，可以有很多方式，对于浏览器客户端，大家都采用 cookie 的方式。

## 过程(服务端 session + 客户端 sessionId)

![session](https://user-gold-cdn.xitu.io/2019/6/13/16b4fb158d3a7cbb?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

* 1.用户向服务器发送用户名和密码
* 2.服务器验证通过后，在当前对话(session)里面保存相关数据，比如用户角色， 登陆时间等;
* 3.服务器向用户返回一个`session_id`, 写入用户的`cookie`
* 4.用户随后的每一次请求， 都会通过`cookie`, 将`session_id`传回服务器
* 5.服务端收到`session_id`, 找到前期保存的数据， 由此得知用户的身份

## 存在的问题

### 扩展性不好

单机当然没问题， 如果是服务器集群， 或者是跨域的服务导向架构， 这就要求 session 数据共享，每台服务器都能够读取 session。

举例来说， A 网站和 B 网站是同一家公司的关联服务。现在要求，用户只要在其中一个网站登录，再访问另一个网站就会自动登录，请问怎么实现？这个问题就是如何实现单点登录的问题

1. Nginx ip_hash 策略，服务端使用 Nginx 代理，每个请求按访问 IP 的 hash 分配，这样来自同一 IP 固定访问一个后台服务器，避免了在服务器 A 创建 Session，第二次分发到服务器 B 的现象。
2. Session 复制：任何一个服务器上的 Session 发生改变（增删改），该节点会把这个 Session 的所有内容序列化，然后广播给所有其它节点。![SessionCopy](https://user-gold-cdn.xitu.io/2019/6/13/16b4fb158be01294?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)
3. 共享 Session：将 Session Id 集中存储到一个地方，所有的机器都来访问这个地方的数据。这种方案的优点是架构清晰，缺点是工程量比较大。另外，持久层万一挂了，就会单点失败；![session 共享](https://user-gold-cdn.xitu.io/2019/6/13/16b4fb158bbf763c?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

另一种方案是服务器索性不保存 session 数据了，所有数据就保存在客户端，每次请求都发回服务器。这种方案就是接下来要介绍的基于 Token 的验证;

# Token

## 过程

![Token](https://user-gold-cdn.xitu.io/2019/6/13/16b4fb158d0b2254?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

1. 用户通过用户名和密码发送请求
2. 程序验证
3. 程序返回一个签名的 token 给客户端
4. 客户端储存 token, 并且每次用每次发送请求
5. 服务端验证 Token 并返回数据

这个方式的技术其实很早就已经有很多实现了，而且还有现成的标准可用，这个标准就是 JWT;

## JWT(JSON Web Token)

### 数据结构

实际的 JWT 大概就像下面这样：

![JWT](https://user-gold-cdn.xitu.io/2019/6/13/16b4fb176b42367c?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

JSON Web Tokens 由 dot（.）分隔的三个部分组成，它们是：

* Header（头部）
* Payload（负载）
* Signature（签名）

因此，JWT 通常如下展示：

**xxxxx.yyyyy.zzzz**

#### Header（头部）

Header 是一个 JSON 对象

```javascript
{
  "alg": "HS256", // 表示签名的算法，默认是 HMAC SHA256（写成 HS256）
  "typ": "JWT"  // 表示Token的类型，JWT 令牌统一写为JWT
}
复制代码
```

#### Payload（负载）

Payload 部分也是一个 JSON 对象，用来存放实际需要传递的数据

```javascript
{
  // 7个官方字段
  "iss": "a.com", // issuer：签发人
  "exp": "1d", // expiration time： 过期时间
  "sub": "test", // subject: 主题
  "aud": "xxx", // audience： 受众
  "nbf": "xxx", // Not Before：生效时间
  "iat": "xxx", // Issued At： 签发时间
  "jti": "1111", // JWT ID：编号
  // 可以定义私有字段
  "name": "John Doe",
  "admin": true
}
复制代码
```

JWT 默认是不加密的，任何人都可以读到，所以不要把秘密信息放在这个部分。

#### Signature（签名）

Signature 是对前两部分的签名，防止数据被篡改。

首先，需要指定一个密钥(secret)。这个密钥只有服务器才知道，不能泄露给用户。然后，使用 Header 里面指定的签名算法（默认是 HMAC SHA256），按照下面的公式产生签名。

```
HMACSHA256(base64UrlEncode(header) + "." + base64UrlEncode(payload), secret)
复制代码
```

算出签名后，把 Header、Payload、Signature 三个部分拼成一个字符串，每个部分之间用"点"（.）分隔，就可以返回给用户。

```
JWT = Base64(Header) + "." + Base64(Payload) + "." + $Signature
复制代码
```

> 如何保证安全？

* 发送 JWT 要使用 HTTPS；不使用 HTTPS 发送的时候，JWT 里不要写入秘密数据
* JWT 的 payload 中要设置 expire 时间

### 使用方式

客户端收到服务器返回的 JWT，可以储存在 Cookie 里面，也可以储存在 localStorage。此后，客户端每次与服务端通信，都要带上这个 JWT。你可以把它放在 Cookie 里面自动发送，但是这样不能跨域，所以更好的做法是放在 HTTP 请求的头信息 Authorization 字段里面。

```
Authorization: Bearer <token>
复制代码
```

另一种做法是， 跨域的时候， JWT 就放在 POST 请求的数据体里。

### JWT 的作用

JWT 最开始的初衷是为了实现授权和身份认证作用的，可以实现无状态，分布式的 Web 应用授权。大致实现的流程如下

![JWT](https://user-gold-cdn.xitu.io/2019/6/13/16b4fb15a64494a1?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

1. 客户端需要携带用户名/密码等可证明身份的的内容去授权服务器获取 JWT 信息；
2. 每次服务都携带该 Token 内容与 Web 服务器进行交互，由业务服务器来验证 Token 是否是授权发放的有效 Token，来验证当前业务是否请求合法。

这里需要注意：不是每次请求都要申请一次 Token，这是需要注意，如果不是对于安全性要求的情况，不建议每次都申请，因为会增加业务耗时；比如只在登陆时申请，然后使用 JWT 的过期时间或其他手段来保证 JWT 的有效性；

### Acesss Token，Refresh Token

JWT 最大的优势是服务器不再需要存储 Session，使得服务器认证鉴权业务可以方便扩展。这也是 JWT 最大的缺点由于服务器不需要存储 Session 状态，因此使用过程中无法废弃某个 Token，或者更改 Token 的权限。也就是说一旦 JWT 签发了，到期之前就会始终有效。
我们可以基于上面提到的问题做一些改进。

前面讲的 Token，都是 Acesss Token，也就是访问资源接口时所需要的 Token，还有另外一种 Token，Refresh Token。一般情况下，Refresh Token 的有效期会比较长。而 Access Token 的有效期比较短，当 Acesss Token 由于过期而失效时，使用 Refresh Token 就可以获取到新的 Token，如果 Refresh Token 也失效了，用户就只能重新登录了。Refresh Token 及过期时间是存储在服务器的数据库中，只有在申请新的 Acesss Token 时才会验证，不会对业务接口响应时间造成影响，也不需要向 Session 一样一直保持在内存中以应对大量的请求。

![Refresh Token](https://user-gold-cdn.xitu.io/2019/6/14/16b5568b15b433a3?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

### 一个简单的 JWT 使用示例

#### 准备

```
npm i --save koa koa-route koa-bodyparser @koa/cors jwt-simple
复制代码
```

#### 服务端代码

```javascript
const Koa = require("koa");
const app = new Koa();
const route = require('koa-route');
var bodyParser = require('koa-bodyparser');
const jwt = require('jwt-simple');
const cors = require('@koa/cors');

const secret = 'your_secret_string'; // 加密用的SECRET字符串，可随意更改
app.use(bodyParser()); // 处理post请求的参数

const login = ctx => {
    const req = ctx.request.body;
    const userName = req.userName;
    const expires = Date.now() + 1000 * 60; // 为了方便测试，设置超时时间为一分钟后
  
    const payload = { 
        iss: userName,
        exp: expires
    };
    const Token = jwt.encode(payload, secret);
    ctx.response.body = {
        data: Token,
        msg: '登陆成功'
    };
}
const getUserName = ctx => {
    const token = ctx.get('authorization').split(" ")[1];
    const payload = jwt.decode(token, secret);
  
    // 每次请求只判断Token是否过期，不重新去更新Token过期时间(更新不更新Token的过期时间主要看实际的应用场景)
    if(Date.now() >  payload.exp) {
        ctx.response.body = {
            errorMsg: 'Token已过期，请重新登录'
        };
    } else {
        ctx.response.body = {
            data: {
                username: payload.iss,
            },
            msg: '获取用户名成功',
            errorMsg: ''
        };
    }
  
}
app.use(cors());
app.use(route.post('/login', login));
app.use(route.get('/getUsername', getUserName));
app.listen(3200, () => {
    console.log('启动成功');
});


复制代码
```

#### 客户端代码

```xml
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>JWT-demo</title>
    <style>
        .login-wrap {
            height: 100px;
            width: 200px;
            border: 1px solid #ccc;
            padding: 20px;
            margin-bottom: 20px;
        }
    </style>
</head>

<body>
    <div class="login-wrap">
        <input type="text" placeholder="用户名" class="userName">
        <br>
        <input type="password" placeholder="密码" class="password">
        <br>
        <br>
        <button class="btn">登陆</button>
    </div>
  
    <button class="btn1">获取用户名</button>
    <p class="username"></p>
</body>
<script>
    var btn = document.querySelector('.btn');
  
    btn.onclick = function () {
        var userName = document.querySelector('.userName').value;
        var password = document.querySelector('.password').value;
  
        fetch('http://localhost:3200/login', {
            method: 'POST', 
            body: `userName=${userName}&password=${password}`,
            headers:{
                'Content-Type': 'application/x-www-form-urlencoded'
            },
            mode: 'cors' // no-cors, cors, *same-origin
        })
            .then(function (response) {
                return response.json();
            })
            .then(function (res) {
                // 获取到Token,将Token放在localStorage
                document.cookie = `token=${res.data}`;
                localStorage.setItem('token', res.data);
                localStorage.setItem('token_exp', new Date().getTime());
                alert(res.msg);
            })
            .catch(err => {
                message.error(`本地测试错误${err.message}`);
                console.error('本地测试错误', err);
            });
    }
    var btn1 = document.querySelector('.btn1');
    btn1.onclick = function () {
        var username = document.querySelector('.username');
        const token = localStorage.getItem('token');
        fetch('http://localhost:3200/getUsername', {
            headers:{
                'Authorization': 'Bearer ' + token
            },
            mode: 'cors' // no-cors, cors, *same-origin
        })
            .then(function (response) {
                return response.json();
            })
            .then(function (res) {
                console.log('返回用户信息结果', res);
                if(res.errorMsg !== '') {
                    alert(res.errorMsg);
                    username.innerHTML = '';
                } else {
                    username.innerHTML = `姓名：${res.data.username}`;
                }
  
            })
            .catch(err => {
                console.error(err);
            });
    }
</script>

</html>
复制代码
```

#### 运行代码

![JWT](assets/20201126094229-2ctuvdr.gif)

> [源码地址](https://github.com/funnycoderstar/demos/tree/master/JWT)
> 以上只是一个特别简单的例子， 对于 Token 过期只做了简单的处理，很多边界条件没有做处理，比如异常的处理；

# 区别

## Cookie 和 Session 的区别

1. 存储位置不同： cookie 数据存放在客户的浏览器上，session 数据放在服务器上
2. 隐私策略不同：cookie 不是很安全， 别人可以分析存放在本地的 cookie 并进行 cookie 欺骗，考虑到安全应当使用 session
3. session 会在一定时间内保存在服务器上。当访问增多，就会比较占用你服务器的性能，考虑到减轻服务器性能方面，应当使用 cookie
4. 存储大小不同： 单个 cookie 保存的数据不能超过 4k, 很多浏览器都限制一个站点最多保存 20 个 cookie

> 一般建议： 将登陆信息等重要信息存放为 session, 其他信息如果需要保留，可以放在 cookie 中

## Token 和 Session 的区别

Session 是一种 HTTP 储存机制， 为无状态的 HTTP 提供持久机制;
Token 就是令牌， 比如你授权(登录)一个程序时，它就是个依据，判断你是否已经授权该软件；

Session 和 Token 并不矛盾，作为身份认证 Token 安全性比 Session 好，因为每一个请求都有签名还能防止监听以及重放攻击，而 Session 就必须依赖链路层来保障通讯安全了。如上所说，如果你需要实现有状态的回话，仍然可以增加 Session 来在服务端保存一些状态。

# 总结

cookie，session，Token 没有绝对的好与坏之分，只要还是要结合实际的业务场景和需求来决定采用哪种方式来管理回话，当然也可以三种都用。
{: id="20201126094121-z14buid" type="doc"}
