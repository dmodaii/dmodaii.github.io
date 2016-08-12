title: laravelapi-jwt
toc: false
date: 2016-08-12 15:58:16
tags:
  - jwt
categories:
  - laravel
---

JSON Web Toke
<!-- more -->

# JWT定义及其组成

JWT（JSON Web Token） 一个JWT实际上就是一个字符串，它由三部分组成，头部、载荷与签名。

### 载荷（Payload）
```
{
    "sub": "1",
    "iss": "http://localhost:8000/auth/login",
    "iat": 1451888119,
    "exp": 1454516119,
    "nbf": 1451888119,
    "jti": "37c107e4609ddbcc9c096ea5ee76c667"
}
```

这里面的前6个字段都是由JWT的(标准)[https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32]所定义的。

sub: 该JWT所面向的用户
iss: 该JWT的签发者
iat(issued at): 在什么时候签发的token
exp(expires): token什么时候过期
nbf(not before)：token在此时间之前不能被接收处理
jti：JWT ID为web token提供唯一标识

### 头部（Header）
头部用于描述关于该JWT的最基本的信息，例如其类型以及签名所用的算法等
```
{
  "typ": "JWT",
  "alg": "HS256"
}
```

### 签名（签名）

将上面的两个base64编码后的字符串都用句号.连接在一起（头部在前）成为被加密字串

我们将上面拼接完的字符串用HS256算法进行加密。在加密的时候，我们还需要提供一个密钥（secret）:

HMACSHA256(
    base64UrlEncode(header) + "." +
    base64UrlEncode(payload),
    secret
)

### 生成JWT
base64UrlEncode(header) + "." +
base64UrlEncode(payload) + "." +
签名

http://laravelacademy.org/post/3640.html

# 使用
