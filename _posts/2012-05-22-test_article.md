---
layout: article
title: "Тестовая статья"
description: "Синтаксис статей в базу знаний"
category: Тестовая категория
---

* t
{:toc}



Содержание статьи
-----------------

Содержание формируется автоматически из заголовков.



Заголовок второго уровня (раздел в статье)
------------------------------------------


Ссылка
------
[linkaaa](http://qqqq.ru)


Вставка изображения
-------------------
![Alt-текст](/images/create_vps/create_server.png "Заголовок изображения")



Заголовок с указанием якоря (для ссылки) {#header_one}
-------------------------------------------------------

Цитата
------
Если хотите писать с новой строки то нужна пустая зацитированная строка:

> ifconfig eth0 down
>
> ifconfig eth0 up
>
> route add default gw 192.168.1.1 eth0


Для выделения кода используйте вначале строки 4 пробела или 1 Tab:

	cp /etc/sysconfig/network-scripts/ifcfg-eth0 /etc/sysconfig/network-scripts/ifcfg-eth0:0
	/etc/init.d/networking restart
	sdsad  s s s 
	dfdfdfdfd


Нумерованный список
-------------------
1. one
2. two

Ненумерованный список
---------------------
- one
+ элемент

HTML-разметка
--------------
<p class="listing">auto eth0:0<br />
iface eth0:0 inet static<br />
address требуемый айпи-адрес<br />
netmask 255.255.0.0<br />
</p>


