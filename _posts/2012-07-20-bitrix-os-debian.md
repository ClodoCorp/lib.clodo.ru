---
layout: article
title: "Bitrix под OS Debian"
category: CMS
description: "Оптимизация Bitrix под OS Debian на облачном сервере Clodo"
---

* t
{:toc}

### Требуемое ПО

Для работы CMS Bitrix под OS Linux необходимо следующее ПО:

1. Веб-сервер (apache/nginx/lighttpd);
2. Интерпретатор Php >= 5.2 (mod_php/php-cgi/php-fpm)
3. Сервер баз данных MySQL (mariadb,mysql,percona) с поддержкой InnoDB

Для работы CMS Bitrix под OS Linux желательно следующее ПО:

1. Акселератор Php (zendoptimizer/zendoptimizer+/apc/eaccelerator/xcache), возможно использование memcache

### Лирическое отступление про различные связки ПО и скорость работы

В ходе тестов на наших облачных серверах было выявленно, что максимальная производительность достигается в связке lighttpd+mysql+php-fcgi(zend server ce)+zendoptimizer+ либо nginx+apache+mysql+mod_php(zend server ce)+zendoptimizer+. Для хранения сессий используется файловая система tmpfs. Касаемо различных акселераторов Php и различных вариантов увеличения скорости работы было выявлено следующее:

* Использование в качестве ускорения работы CMS Bitrix memcache не давало прироста в скорости, а зачастую только снижало быстродействие
* Apc и Xcache дают примерно одинаковую скорость работы
* Разница в скорости работы apache(prefork) и php-fpm минимальна. Поэтому если на сервере установлена только CMS Bitrix, то Php-fpm стоит ставить только, если требуется разграничение прав доступа для скриптов разных сайтов.
* Минимальный размер оперативной памяти 512M (запустить CMS Bitrix можно и на 256, но производительность под нагрузкой оставляют желать лучшего)
* Размер памяти, требуемый для Apc/Xcache для демо сайта не менее 32M
* ZendOptimizer+ падает при попытке использования его с любым Php, отличным от Zend Server.
* Шейпер дисковых операций отключен (при наличии достаточного объема оперативной памяти не оказывает никакого влияния)
* Для MySQL и Php используется [tcmalloc](http://goog-perftools.sourceforge.net/doc/tcmalloc.html "tcmalloc")

### Лирическое отступление про файловые системы

Всестороннее тестирование различных файловых систем не производилось. Наилучшую скорость работы среди тестируемых фс (ext3 и ext4) дала ext4. Для конвертации текущей файловой системы были выполнены следующие операции (производить на свой страх и риск, выполняя команды Вы берете на себя всю ответственность за порчу данных):

    1. tune2fs -O extents,uninit_bg,dir_index,dir_nlink,filetype /dev/xvda1
    2. reboot
    3. find / -xdev -type d -print0 | xargs -0 chattr +e
    4. find / -xdev -type f -print0 | xargs -0 chattr +e

Комманды 3 и 4 производят операции над всеми уже созданными файлами, поэтому перед этой операцией в целях экономии средств шефпер дисковых операций стоит ВКЛЮЧИТЬ.

Внимание: Так как смена размера блочного устройства через панель управления с использованием ext4 нами не производилась, то после конвертации файловой системы в ext4 изменение размера диска через панель управления может начать работать неправильно, что может привести к порче данных.

Для ускорения создания сессий и минимизации числа дисковых операций /tmp перемонтирован в tmpfs (по-умолчанию в Debian /tmp располагается на жестком диске) /etc/fstab:

    /dev/xvda1   /            ext4      defaults,auto,relatime,async,barrier=0,nodiratime,journal_async_commit,max_batch_time=30000,journal_ioprio=0      1 1
    /swap        swap         swap      defaults      0 0

    none         /dev/pts     devpts    defaults      0 0
    proc         /proc        proc      defaults      0 0
    none         /proc/xen    xenfs     defaults      0 0

    tmpfs        /tmp         tmpfs     size=100M     0 0


### Установка и настройка необходимого ПО

Добавляем необходимые gpg ключи:

    wget http://repos.zend.com/zend.key -O - | apt-key add -

Добавляем требуемые репозитории:

    echo "deb http://repos.zend.com/zend-server/deb server non-free" >> /etc/apt/sources.list.d/zend.list

Обновляем списки пакетов:

    apt-get update

Устанавливаем необходимое ПО:

    for i in zip zem xsl sockets optimizer-plus mysql mcrypt mbstring json gd ftp fileinfo exif curl ctype bz2 bin bcmath;
    do
      apt-get -y install "php-5.3-${i}-zend-server"
    done

Создаем файл с переменными окружения, которые использует Zend Server CE:

    cat >> /etc/profile.d/zend.sh <<EOF
    export PATH=$PATH:/usr/local/zend/bin
    EOF

Подгружаем новые переменные окружения в текущий shell:

    source /etc/profile 
    
Продолжаем установку ПО:

    apt-get -y install exim4-daemon-light nginx-full mysql-server

Перенастраиваем отправку почты:

    dpkg-reconfigure exim4-config

![exim4_config_00](/images/cms/bitrix/exim4-config_00.jpg "exim4_config_00")
![exim4_config_01](/images/cms/bitrix/exim4-config_01.jpg "exim4_config_01")
![exim4_config_02](/images/cms/bitrix/exim4-config_02.jpg "exim4_config_02")
![exim4_config_03](/images/cms/bitrix/exim4-config_03.jpg "exim4_config_03")
![exim4_config_04](/images/cms/bitrix/exim4-config_04.jpg "exim4_config_04")
![exim4_config_05](/images/cms/bitrix/exim4-config_05.jpg "exim4_config_05")
![exim4_config_06](/images/cms/bitrix/exim4-config_06.jpg "exim4_config_06")
![exim4_config_07](/images/cms/bitrix/exim4-config_07.jpg "exim4_config_07")
![exim4_config_08](/images/cms/bitrix/exim4-config_08.jpg "exim4_config_08")
![exim4_config_09](/images/cms/bitrix/exim4-config_09.jpg "exim4_config_09")


В файле /etc/default/exim4 нужно поменять значение QUEUERUNNER с combined на queueonly если данный сервер не должен принимать входящую почту.
Также стоит скорректировать время обхода очереди поменяв параметр QUEUEINTERVAL с 30m на 1m (обход очереди раз в минуту)

    /etc/init.d/exim4 restart

Создаем директорию для будущего сайта:

    mkdir -p /var/www/htdocs

Создаем виртуальный сервер для nginx:

    cat >> /etc/nginx/sites-available/bitrix <<EOF
    gzip_vary on;
    gzip_proxied any;
    gzip_comp_level 6;
    gzip_buffers 16 8k;
    gzip_http_version 1.1;
    gzip_types text/plain text/css application/json application/x-javascript text/xml application/xml application/xml+rss text/javascript;

    directio 4m;
    max_ranges 4;
    
    open_file_cache          max=60000 inactive=60s;
    open_file_cache_valid    60s;
    open_file_cache_min_uses 2;
    open_file_cache_errors   on;

    postpone_output 512;
    reset_timedout_connection on;

    sendfile_max_chunk 2M;
    underscores_in_headers on;

    upstream apache {
      server 127.0.0.1:8080;
      keepalive 32;
    }

    server {
      listen   80 default_server backlog=1024 deferred ;
      root /var/www/htdocs;
      index index.php;
      
      server_name localhost _ "";
      server_name_in_redirect off;

      proxy_redirect http://apache:8080 http://$host:80;

      client_max_body_size 120m;
      client_body_buffer_size 4M;

      proxy_connect_timeout 90;
      proxy_send_timeout 90;
      proxy_read_timeout 90;
      proxy_buffer_size 4k;
      proxy_buffers 4 32k;
      proxy_busy_buffers_size 64k;
      proxy_temp_file_write_size 64k;

      proxy_set_header Host $host;
      proxy_set_header X-Real-IP $remote_addr;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

      location ~* ^.+\.(jpg|jpeg|gif|png|svg|js|css|mp3|ogg|mpe?g|avi|zip|gz|bz2?|rar)$ {
        try_files $uri @backend;
        expires  max;
      }

      location / {
        try_files $uri $uri/ @backend;
      }

      location ~ \.php$ {
        try_files  $uri @backend;
        proxy_http_version 1.1;
        proxy_set_header Connection "";
        proxy_pass http://apache;
      }

      location @backend {
	rewrite  ^(.*)$  /bitrix/urlrewrite.php last;
      }

      location ^~ /bitrix/admin/ {
        try_files $uri @backend_admin;
        proxy_http_version 1.1;
        proxy_set_header Connection "";
        proxy_pass http://apache;
      }
 
      location @backend_admin {
        rewrite  ^(.*)$  /bitrix/admin/404.php last;
      }

      location = /favicon.ico {
        log_not_found off;
      }
   
      location = /robots.txt {
        log_not_found off;
      }

      location ~ (/\.|/bitrix/modules|/upload/support/not_image|/bitrix/php_interface) {
        deny all;
      }
    }
    EOF

    ln -s /etc/nginx/sites-available/bitrix /etc/nginx/sites-enabled/bitrix

Выключаем ненужные модули apache
    
    a2dismod autoindex cgi deflate env negotiation reqtimeout setenvif

Создаем виртуальный сервер для apache

    <VirtualHost 127.0.0.1:8080>
      ServerAdmin webmaster@localhost
      DocumentRoot /var/www/htdocs

      <Directory />
        Options FollowSymLinks
        AllowOverride None
      </Directory>
     
      <Directory /var/www/htdocs>
        Options Indexes FollowSymLinks MultiViews
        AllowOverride All 
        Order allow,deny
        allow from all
      </Directory>
    </VirtualHost>


Редактируем /etc/apache2/ports.conf меняя строки

    NameVirtualHost *:80
    Listen 80

на

    NameVirtualHost 127.0.0.1:8080
    Listen 127.0.0.1:8080

Редактируем /etc/apache2/apache2.conf меняя блок

    <IfModule mpm_prefork_module>
      StartServers          5
      MinSpareServers       5
      MaxSpareServers      10
      MaxClients          150
      MaxRequestsPerChild   0
    </IfModule>

на

    <IfModule mpm_prefork_module>
      StartServers          34
      MinSpareServers       34
      MaxSpareServers       34
      MaxClients            34
      MaxRequestsPerChild   0
    </IfModule>

Редактируем параметры Php:

    sed -i 's|zend_extension_manager.recheck_license_interval=5|zend_extension_manager.recheck_license_interval=0|g' /usr/local/zend/etc/ext.d/extension_manager.ini
    echo "mbstring.func_overload=2" >> /usr/local/zend/etc/ext.d/mbstring.ini
    echo "mbstring.internal_encoding=UTF-8" >> /usr/local/zend/etc/ext.d/mbstring.ini
    cat >> /usr/local/zend/etc/ext.d/bitrix.ini <<EOF
    display_errors = on
    log_errors = STDERR
    memory_limit = 128M
    realpath_cache_size = 4096k
    allow_call_time_pass_reference = on
    upload_max_filesize = 32M
    post_max_size = 32M
    precision = 10
    y2k_compliance = off
    session.entropy_file = /dev/urandom
    session.entropy_length = 512
    session.save_path = /tmp
    date.timezone = Europe/Moscow
    EOF

    sed -i 's|zend_optimizerplus.revalidate_path=0|zend_optimizerplus.revalidate_path=1|g' /usr/local/zend/etc/ext.d/optimizerplus.ini
    sed -i 's|zend_optimizerplus.dups_fix=0|zend_optimizerplus.dups_fix=1|g' /usr/local/zend/etc/ext.d/optimizerplus.ini
    sed -i 's|zend_optimizerplus.memory_consumption=(.*)|zend_optimizerplus.memory_consumption=32|g' /usr/local/zend/etc/ext.d/optimizerplus.ini
    sed -i 's|zend_optimizerplus.max_accelerated_files=(.*)|zend_optimizerplus.max_accelerated_files=60000|g' /usr/local/zend/etc/ext.d/optimizerplus.ini

    cat >> /usr/local/zend/etc/ext.d/optimizerplus.ini <<EOF
    zend_optimizerplus.save_comments=0
    EOF

    cwd=`pwd` ; cd /usr/local/zend/etc/conf.d && ln -s ../ext.d/bitrix.ini bitrix.ini; cd $pwd

После этого перезапускаем apache и nginx:

    /etc/init.d/apache2 restart && /etc/init.d/nginx restart

Настройка базы данных:

    cat >> /etc/mysql/conf.d/bitrix.cnf <<EOF
    [mysqld_safe]
    socket		= /var/run/mysqld/mysqld.sock
    nice		= -5

    [mysqld]
    skip-external-locking

    bind-address		= 127.0.0.1
    max_connections		= 34
    max_user_connections	= 32
    connect_timeout		= 20
    wait_timeout		= 60
    max_allowed_packet	  	= 4M
    thread_cache_size           = 128

    sort_buffer_size	        = 4M
    bulk_insert_buffer_size	= 6M
    tmp_table_size		= 6M
    max_heap_table_size	        = 6M

    table_cache		        = 1000
    myisam_recover              = BACKUP
    key_buffer_size		= 2M
    myisam_sort_buffer_size	= 1M
    concurrent_insert	        = 2
    read_buffer_size	        = 4M
    read_rnd_buffer_size	= 2M

    query_cache_limit		= 4M
    query_cache_size		= 24M
    query_cache_type		= 1

    default_storage_engine	= InnoDB
    innodb_log_buffer_size	= 80M
    innodb_file_per_table	= 1
    innodb_open_files	        = 1000

    innodb_buffer_pool_size=24M
    thread_concurrency=6
    thread_cache = 16
    innodb_flush_log_at_trx_commit = 2
    innodb_flush_method     = O_DIRECT
    join_buffer_size=2M
    transaction-isolation=READ-COMMITTED

    skip_name_resolve
    skip_networking
    EOF


Скачиваем установщик CMS Bitrix

    wget http://www.1c-bitrix.ru/download/scripts/bitrixsetup.php -O /var/www/htdocs/bitrixsetup.php
    chown -R www-data /var/www/

После установки необходимо включить кеширование в Apc:

    cat >> /var/www/htdocs/bitrix/php_interface/dbconn.php <<EOF
    
    <?php
    define("BX_CACHE_TYPE", "apc");
    define("BX_CACHE_SID", $_SERVER["DOCUMENT_ROOT"]."#01");
    ?>
    EOF

Также стоит указать сравнение:

    cat >> /var/www/htdocs/bitrix/php_interface/after_connect.php <<EOF

    $DB->Query('SET collation_connection = "utf8_unicode_ci"');
    EOF

Настройки модулей:

* Проактивная защита - удален
* Компрессия - удален
* Реклама/баннеры - удален
* Автокеширование - HTML кеш - включен


Настройки сети:

    cat >> /etc/sysctl.d/bitrix.conf <<EOF
    net.core.rmem_max = 16777216
    net.core.wmem_max = 16777216
    net.ipv4.tcp_rmem = 4096 87380 16777216 
    net.ipv4.tcp_wmem = 4096 65536 16777216
    net.ipv4.tcp_sack = 0
    net.ipv4.tcp_timestamps = 0
    net.ipv4.tcp_fin_timeout = 1
    net.ipv4.tcp_tw_recycle = 1

    net.core.netdev_max_backlog = 262144
    net.core.somaxconn = 262144

    net.ipv4.tcp_syncookies = 1
    net.ipv4.tcp_max_orphans = 262144
    net.ipv4.tcp_max_syn_backlog = 262144
    net.ipv4.tcp_synack_retries = 2
    net.ipv4.tcp_syn_retries = 2

    net.ipv4.tcp_max_tw_buckets = 1440000

    net.ipv4.icmp_echo_ignore_broadcasts=1
    net.ipv4.conf.all.forwarding=0
    net.ipv4.conf.all.mc_forwarding=0

    net.ipv4.tcp_tw_reuse=1
    EOF

### Результаты тестов производительности

#### nginx+apache2+mysql+mod_php(zend server ce)+zendoptimizer+
![performance](/images/cms/bitrix/bitrix-os-debian-performance.png "performance")

