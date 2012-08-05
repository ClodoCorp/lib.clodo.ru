---
title: "Nginx + Apache"
description: "Установка и настройка связки Nginx+Apache"
layout: article
category: WEB-Сервер
---


Связка двух веб-серверов, один из которых выполняет функцию фонтенда (Nginx), другой - бэкенда (Apache2), предназначена для снижения общей нагрузки на сервер. Достигается это за счет того, что более легкий и не обремененный дополнительным функционалом Nginx первым принимает все запросы пользователей. Он самостоятельно выдает по запросам статический контент (изображения, html-файлы, javascript-скрипты..), не озадачивая этой функцией тяжеловесный Apache, который, в свою очередь, обрабатывает динамический контент. Apache не работает напрямую с пользователем, все их запросы проксируются Nginx, и ему же возвращаются ответы. Так достигается разделение труда: Nginx освобождает Apache от необходимости "общаться" с множеством пользователей и обрабатывать запросы на статику, которая составляет большую часть исходящего трафика. Apache не создает множества дочерних процессов, потребляющих оперативную память.

Данная связка часто применяется для обеспечения работы крупных ресурсов с большой посещаемостью. Для ресурсов с маленькой посещаемостью такая связка не даст ощутимого прироста производительности.



* t
{:toc}



Если Вы являетесь пользователем ISPmanager, нижеизложенная информация пригодится Вам лишь для ознакомления. Его функционал позволяет создать такую связку достаточно быстро и без манипуляции с командной строкой.

Данная статья была протестирована на CentOS 5 и Debian Squeeze. Связка работает в том же виде и на других дистрибутивах, но по причине наибольшей популярности первых, мы будем говорить именно о них. Основная часть данной статьи посвящена CentOS, но различия с Debian заключаются только в названиях пакетных менеджеров и нескольких незначительных моментах. Все особенности установки для Debian описаны в заключительной части данной статьи. Команды и примеры файлов конфигурации, не указанные в этом разделе, подходят для обеих систем.



## Установка nginx

### CentOS

Для начала нам необходимо подключить репозитории EPEL и CentALT. Это нужно для того, чтобы мы смогли установить Nginx с поддержкой модуля RPAF и сам модуль для Apache.

Для подключения этих репозиториев введите в консоли команды:

	# для 32-битных ОС  
	rpm -ihv http://dl.fedoraproject.org/pub/epel/5/i386/epel-release-5-4.noarch.rpm  
	rpm -ihv http://centos.alt.ru/repository/centos/5/i386/centalt-release-5-3.noarch.rpm  

	# для 64-битных ОС  
	rpm -ihv http://dl.fedoraproject.org/pub/epel/5/x86_64/epel-release-5-4.noarch.rpm  
	rpm -ihv http://centos.alt.ru/repository/centos/5/x86_64/centalt-release-5-3.noarch.rpm

Далее, выполните команду установки пакета nginx:

	yum install nginx

В большинстве случаев требуется, чтобы nginx загружался автоматически при запуске сервера. Для этого выполните следующую команду:

	chkconfig nginx on

### Debian/Ubuntu

Для установки пакета в ОС Debian или Ubuntu достаточно выполнить команду в консоли:

	apt-get install nginx

Nginx автоматически будет добавлен в автозагрузку при запуске сервера.



## Конфигурация Nginx

Следующий этап - изменение файла конфигурации Nginx. Путь к файлу конфигурации: `/etc/nginx/nginx.conf`

Наш конфиг файл должен выглядеть примерно так:

	user www-data;
	error_log /var/log/nginx/error.log debug; 
	pid /var/run/nginx.pid;
	worker_rlimit_nofile 80000;

	events {
		worker_connections 2048;
	}

	http {
		include /etc/nginx/mime.types;
		default_type application/octet-stream;
		log_format main ‘$remote_addr – $remote_user [$time_local] $status ‘
		‘»$request» $body_bytes_sent «$http_referer» ‘
		‘»$http_user_agent» «http_x_forwarded_for»‘;
		access_log /var/log/nginx/access.log main;

		server {
			listen 		88.88.88.11:80; # 88.88.88.11 нужно заменить на IP Вашего сервера
			server_name mysite.ru www.mysite.ru; # здесь и далее вместо mysite.ru указывается имя Вашего сайта
			access_log 	/var/log/nginx/host.access.log main;

			server_name_in_redirect off;

			# Секция ниже описывает параметры, по которых фронтенд обменевается с бэкендом,
			#такие, как адрес бэкенда, параметры прямого редиректа, параметры передачи заголовков, максимальный размер принимаемых файлов и пр.
			location / {
				proxy_pass 			http://127.0.0.1:8080/;
				proxy_redirect 		off;
				proxy_set_header 	Host $host;
				proxy_set_header 	X-Real-IP $remote_addr;
				proxy_set_header 	X-Forwarded-For $proxy_add_x_forwarded_for;
				client_max_body_size 10m;
				proxy_connect_timeout 90;
			}

			#Эта секция отвечает за местонахождение и типы статичных файлов, обрабатываемых Nginx. Динамические файлы мы будем отсылать на Apache
			location ~* ^.+\.(jpg|jpeg|gif|png|ico|css|zip|tgz|gz|rar|bz2|doc|xls|exe|pdf|ppt|txt|tar|wav|bmp|rtf|js)$ {
				root /var/www/mysite.ru;
			}
		}
	}



## Установка Apache2

### CentOS

В списке пакетов для CentOS Apache2 значится как httpd, потому необходимо выполнить следующую команду в консоли:

	yum install httpd

### Debian/Ubuntu

Для Debian/Ubuntu установить Apache нужно командой:

	apt-get install apache2



## Конфигурация Apache

Приводим соответствующую часть файла конфигурации Apache к такому виду:

Файл конфигурации располагается:  
**Debian/Ubuntu:** /etc/apache2/apache2.conf  
**CentOS:** /etc/httpd/conf/httpd.conf

	Listen 127.0.0.1:8080
	NameVirtualHost 127.0.0.1:8080

	<VirtualHost 127.0.0.1:8080>
	  # В строке ниже указывается адрес почтового ящика администратора сервера,
	  # т. е. Ваш. Имя-пример "mysite.ru" здесь и далее необходимо заменить на имя Вашего сайта
	  ServerAdmin webmaster@mysite.ru
	  DocumentRoot /var/www/mysite.ru/
	  ServerName mysite.ru
	  ErrorLog logs/mysite.ru-error_log
	  CustomLog logs/mysite.ru-access_log common
	</VirtualHost>



## Установка модуля RPAF

Т.к. теперь все запросы к Apache приходят не от удалённых клиентов, а от Nginx, то в итоге IP-адрес клиента Apache определяет как локальный (127.0.0.1). Для решения этой проблемы нам нужен модуль RPAF. Он берет тело заголовка X-Forwarded-For, присланного от фронтенда (Nginx) и заменяет значение заголовка REMOTE_ADDR на бекенде (Apache).

### CentOS

Установка в CentOS выполняется следующей командой:

	yum install mod_rpaf

### Debian/Ubuntu

В Debian или Ubuntu установка и включение модуля RPAF в Apache выполняется следующими командами:

	apt-get install libapache2-mod-rpaf
	a2enmod rpaf



## Настройка модуля RPAF

Файл конфигурации RPAF находится:  
**Debian/Ubuntu:** /etc/apache2/mods-enabled/rpaf.conf  
**CentOS:** /etc/httpd/conf.d/rpaf.conf

Он должен содержать следующие строки:

> RPAFenable On  
> RPAFsethostname Off  
> RPAFproxy_ips 127.0.0.1  
> RPAFheader X-Real-IP  

Если у вас установлена ОС CentOS, то в начало этого файла обязательно добавьте строку:

> LoadModule rpaf_module modules/mod_rpaf-2.0.so



## Завершение настройки (перезапуск сервисов)

На этом настройка связки закончена. Теперь нужно только перезапусть Apache и Nginx. Команды перезапуска сервисов различаются для ОС (из-за различий в названиях пакетов).

Для CentOS выполните команды:

	/etc/init.d/httpd restart
	/etc/init.d/nginx restart

Для Debian и Ubuntu команды будут следующие:

	/etc/init.d/apache2 restart
	/etc/init.d/nginx restart



Теперь связка работает, Nginx обрабатывает статичные данные, Apache - динамические.

Обращаем Ваше внимание, что данный пример настройки действителен только для одного хоста.  
В случае наличия более чем одного сайта, содержимое файлов конфигурации будет отличаться.
{:.tip}