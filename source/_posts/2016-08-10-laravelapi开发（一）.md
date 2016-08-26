layout: laravel
title: api开发（一）
date: 2016-08-10 09:53:57
tags:
  - api
  - jwt
categories:
  - laravel
---

laravel5.1 api开发准备
<!-- more -->

# composer update or cpmposer require "特定pakage" （推荐）

api搭建：
https://github.com/dingo/api

JWT认证：
https://github.com/tymondesigns/jwt-auth

oauth:
https://github.com/lucadegasperi/oauth2-server-laravel

# 允许跨域和option请求

https://github.com/barryvdh/laravel-cors

# jwt获取认证信息

对于header携带 Authorization

\vendor\tymon\jwt-auth\src\JWTAuth.php
使用自定义获取认证的方法：
```php
protected function parseAuthHeader($header = 'authorization', $method = 'bearer')
{
    $header = $this->request->headers->get($header);

    /*if (! starts_with(strtolower($header), $method)) {
        return false;
    }
    return trim(str_ireplace($method, '', $header));*/

    $mode = '/(?P<key>\w+)\="(?P<val>[^"]+)"/ui';
    $status = preg_match_all($mode, $header, $match);
    $param = array_combine($match['key'], $match['val']);
    return $param['id'];
}
```


# JSON Web Token

### JWT定义及其组成

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

这里面的前6个字段都是由JWT的[标准](https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32)所定义的。

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

### refresh token

更新token后旧的token不能使用， 若还未过期则放入cache

```php
public function add(Payload $payload)
{
    $exp = Utils::timestamp($payload['exp']);
    $refreshExp = Utils::timestamp($payload['iat'])->addMinutes($this->refreshTTL);

    // there is no need to add the token to the blacklist
    // if the token has already expired AND the refresh_ttl
    // has gone by
    if ($exp->isPast() && $refreshExp->isPast()) {
        return false;
    }

    // Set the cache entry's lifetime to be equal to the amount
    // of refreshable time it has remaining (which is the larger
    // of `exp` and `iat+refresh_ttl`), rounded up a minute
    $cacheLifetime = $exp->max($refreshExp)->addMinute()->diffInMinutes();

    $this->storage->add($payload['jti'], [], $cacheLifetime);

    return true;
}
```
