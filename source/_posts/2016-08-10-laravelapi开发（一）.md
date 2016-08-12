layout: laravel
title: api开发（一）
date: 2016-08-10 09:53:57
tags:
  -- api
categories:
  -- laravel
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
