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

### 输出最后一条SQL
```php
use DB;
DB::connection()->enableQueryLog();

// database operate

$sql = DB::getQueryLog();
var_dump($sql);
```

# 原生SQL

### Illuminate\Support\Facades\DB

```PHP
$users = DB::select(DB::raw("select * from users where id > :id"),[
    'id' => 2
]);

// return stdClass
```
Lastly, if you are performing queries which don't return data, then using a SELECT query will result in errors. For example, if you want to start the auto-increment ID of a MySQL table to something other than zero, we can use the statement method. With statement, we don't need to use the raw() method:
```
// Warning: This is a MySQL-specific query
DB::statement( 'ALTER TABLE HS_Request AUTO_INCREMENT=9999' );
// The statement method can also accept parameters:
DB::statement( 'ALTER TABLE HS_Request AUTO_INCREMENT=:incrementStart', array('incrementStart' => 9999) );
```
参考:
[http://laravelacademy.org/post/126.html]()
[http://laraveldaily.com/select-with-dbraw-make-your-database-work/]()
