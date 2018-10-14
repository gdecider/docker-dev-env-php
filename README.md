# Рабочее окружение для PHP проектов

**Изначально используется на ОС Ubuntu 18.04, все инструкции описанные в данном файле так же относятся к указанной ОС**

## Из чего состоит

Использованы официальные docker образы:

* php:7.2.10-apache [https://hub.docker.com/_/php/](https://hub.docker.com/_/php/)
* mysql:5.7.23 [https://hub.docker.com/_/mysql/](https://hub.docker.com/_/mysql/)
* adminer:latest [https://hub.docker.com/_/adminer/](https://hub.docker.com/_/adminer/)
* composer:latest [https://hub.docker.com/_/composer/](https://hub.docker.com/_/composer/) **!!! используется отдельно и не зависит от остальных компонентов**

## Как использовать

### Получение и запуск

* Устанавливаем Docker [официальная инструкция](https://docs.docker.com/install/linux/docker-ce/ubuntu/)
* Устанавливаем docker-compose [официальная инструкция](https://docs.docker.com/compose/install/)
* Клонируем репозиторий в нужную папку
* Выполняем ```docker-compose up --build```
* Проверяем в браузере http://localhost должен отобразиться результат работы phpinfo()

### Настройка хостов

* В корне находится файл hosts - это ссылка на файл /etc/hosts в вашей системе, расположена она тут исключительно для удобства. Для редактирования этого файла нужны права суперпользователя root. В этом файле нaстраиваем нужные хосты.
Пример:
```
127.0.0.1 local.docker-test.ru
```
* В корне находится файл sites-available.conf - это ссылка на файл ./configs/apache2/sites-available/000-default.conf хосты apache можно настроить в нем, либо можете создать по отдельному файлу для каждого проекта и разместить их в папке 
```/configs/apache2/sites-enabled```, кому как удобно.
Пример конфигурации хоста:
```
<VirtualHost *:80>
    ServerName local.docker-test.ru
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/local.docker-test.ru
    <Directory />
	    Options FollowSymLinks
	    AllowOverride None
    </Directory>
    <Directory /var/www/local.docker-test.ru>
		Options Indexes FollowSymLinks MultiViews
		AllowOverride All
		Order allow,deny
		allow from all
	</Directory> 
	<IfModule mod_php7.c>
	    php_admin_value mbstring.func_overload 2
	    php_value mbstring.internal_encoding UTF-8
	    php_admin_value realpath_cache_size "4096k"
	    php_value short_open_tag "On"
	    php_flag session.use_trans_sid off
		php_value display_errors 1
		php_value display_startup_errors 1
		php_value error_reporting E_ALL
		php_value max_input_vars 10000
		php_value post_max_size 128M
		php_value upload_max_filesize 128M
	</IfModule> 
    ErrorLog /var/log/apache2/docker-test_error.log
    LogLevel warn
    CustomLog /var/log/apache2/docker-test_access.log combined
</VirtualHost>

```

### Использование adminer

Adminer доступен по адресу ```localhost:8080```, адрес сервера MySql - db
логин по умолчанию: root, пароль по умолчанию: root

### Использование composer

**!!! Composer ни как не связан с остальными образами используемыми в данной сборке, тут приведен пример самостоятельного использования composer из официального образа. Обращаю на это внимание, т.к. версия php и его расширений в официальном образе composer и в образах окружения разработки могут отличаться и повлиять на получаемые композером репозитории приложений.**

В файл ~/.bashrc вставить код:

```
composer () {
    tty=
    tty -s && tty=--tty
    docker run \
        $tty \
        --interactive \
        --rm \
        --user $(id -u):$(id -g) \
        --volume /etc/passwd:/etc/passwd:ro \
        --volume /etc/group:/etc/group:ro \
        --volume $(pwd):/app \
        composer "$@"
}
```

После этого открыть новую консоль, т.к. в уже открытых эти изменения доступны не будут, и использовать composer как будто он установлен на вашей локальной машине.