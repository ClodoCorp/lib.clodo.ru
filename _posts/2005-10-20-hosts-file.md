---
title: Проверка сайта без делегации домена
description: "Проверка сайта без делегации домена"
layout: article
category: Первые шаги
---

* t
{:toc}

## Проверка сайта без делегации доменного имени

Для проверки работоспособности сайта, без изменения текущей конфигурации домена, достаточно на вашем компьютере вручную прописать IP-адрес домена.

Допустим, у вас есть сайт на домене `domain.ru` и он сейчас работает на неком хостинге. Вы решили переехать к нам и хотите протестировать ваш сайт, не изменяя при этом записи DNS.

Для этого нужно в файле hosts прописать соответствие между доменом и IP-адресом.

Предположим, что ваш тестируемый сервер имеет IP-адрес 12.34.56.78. Тогда вам нужно в файл hosts прописать строку:

> 12.34.56.78 domain.ru

В разных ОС данный файл располагается по следующим путям:

- **Windows 95/98/ME:** WINDOWS\hosts
- **Windows NT/2000:** WINNT\system32\drivers\etc\hosts
- **Windows XP/2003/Vista/7:** WINDOWS\system32\drivers\etc\hosts
- **Mac OS X 10.2+:** /private/etc/hosts
- **Linux:** /etc/hosts

В ОС Linux также стоит проверить очерёдность разрешения доменного имени. Для этого в файле `/etc/nsswitch.conf` найдите группу `"hosts"`. Первым аргументом в данной группе должен быть `"files"`.


## Проверка разрешения доменного имени

Чтобы проверить, действительно ли ваш компьютер обращается IP-адресу, который вы указали в файле hosts, можно в командной строке выполнить команду:

	ping -c 1 domain.ru

В выводе команды будет указан IP-адрес, на который ваш компьютер посылает эхо-запросы. Если это IP-адрес сервера, на котором вы тестируете сайт, то смело можете вводить доменное имя в адресной строке браузера и проверять работоспособность сайта.