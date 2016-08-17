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


# 输出最后一条SQL

```php
use DB;
DB::connection()->enableQueryLog();

// database operate
$sql = DB::getQueryLog();
var_dump($sql);
```
