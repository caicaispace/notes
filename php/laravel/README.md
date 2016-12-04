#laravel
##定时器

### crontab

- crontab 检测

1.创建 `toLog.php` 文件
```
<?php
	file_put_contents('/home/www/laravel/run.log', date('Y-m-d H:i:s')."\r\n", FILE_APPEND);
?>
```

2.运行命令 crontab

```
crontab -e
```

3.复制以下代码到 crontab

```
* * * * * /usr/local/php/bin/php /home/www/laravel/toLog.php
```
4.查看 run.log 文件

```
tail -f run.log
```

- 定时执行 laravel artisan 任务（每隔 60 秒执行一次）

复制以下代码到 crontab

```
* * * * * /usr/local/php/bin/php /home/www/laravel/artisan schedule:run 1>> /dev/null 2>&1
```