#laravel

##官网

> [[官网](https://laravel.com)]
> [[开发文档](https://laravel.com/docs)]
> [[github](https://github.com/laravel/laravel)]


##定时器

### crontab

- crontab 检测

1.创建 `toLog.php` 文件
```php
<?php
	file_put_contents('/home/www/laravel/run.log', date('Y-m-d H:i:s')."\r\n", FILE_APPEND);
?>
```

2.运行命令（打开 crontab）

```
crontab -e
```

3.复制以下代码到 crontab

```crontab
* * * * * /usr/local/php/bin/php /home/www/laravel/toLog.php
```
4.运行命令（查看 run.log 文件）

```
tail -f run.log
```

- 定时执行 laravel artisan 任务（每隔 60 秒执行一次）

复制以下代码到 crontab

```crontab
* * * * * /usr/local/php/bin/php /home/www/laravel/artisan schedule:run 1>> /dev/null 2>&1
```

- crontab 调用 shell（暂不可用）

```crontab
#Borrowed from anacron
SHELL=/bin/sh
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin
#End borrowed from anacron
* * * * * root /usr/bin/bash /home/www/laravel/crontab.sh
```

- 创建文件 `crontab.sh` （windows下要在 linux 中 touch 文件，保证编码正确）

```shell
#!/bin/bash
step=2

for (( i = 0; i < 10; i=(i+step) )); do
	echo 'yes'
	php '/home/www/laravel/artisan' schedule:run
	sleep $step
done

exit 0
```