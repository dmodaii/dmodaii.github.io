title: laravel-Database
toc: false
date: 2016-08-17 12:45:58
tags:
 - database
categories:
 - laravel
---

laravel database
<!-- more -->


# debugger

- 输出最后一条SQL
```php
use DB;
DB::connection()->enableQueryLog();

// database operate
$sql = DB::getQueryLog();
var_dump($sql);
```

# 原声SQL

### Illuminate\Support\Facades\DB

```PHP
$users = DB::select(DB::raw("select * from users where id > :id"),[
    'id' => 2
]);

// return stdClass
```
