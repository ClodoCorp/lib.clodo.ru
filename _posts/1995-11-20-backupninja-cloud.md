---
layout: article
title: "Создание резервной копии на Cloud Storage"
description: "Backupninja"
category: Облачное хранилище 
---

## Создание резервной копии на Cloud Storage

* Содержание
{:toc}

Описание
----------

Для создания резервной копии на облачное хранилище, можно использовать утилиту Backupninja и плагины, позволяющие загружать данные в хранилище.

Backupninja-cloud представляет собой плагин для утилиты Backupninja.
При помощи него можно создавать резервные копии и загружать их на Clodo Cloud Storage

Установка
----------

Для того, чтобы это сделать, необходимо сперва установить следующее ПО:

* backupninja
: утилита для управления процессом создания резервных копий

* duplicity
: утилита для создание инкрементальных резервных копий

* python-cloudfiles
: библиотека для поддержки облачных хранилищ

* backupninja-cloud
: плагин для работы с облачным хранилищем

Для установки на Debian 6 можно воспользоваться скриптом для автоматической установки:


	wget http://cs.aylium.net/scripts/backupninja-cloud-debian.sh
	chmod +x backupninja-cloud-debian.sh
	./backupninja-cloud-debian.sh

Создание задания
----------


После успешной установки, можно приступить к настройке резервной копии файлов. Она производится при помощи ninjahelper:

    ninjahelper
	
![](/images/cloudstorage/backupninja/bn_cloud_001.png)


Создание нового задания для резервного копирования начинается с выбора типа резервного копирования. В данном случае - csdup.

![](/images/cloudstorage/backupninja/bn_cloud_002.png)


В главном меню создания задания необходимо пошагово указать все необходимые данные.
![](http://static.clodo.ru/lib-images/backupninja/bn_cloud_003.png)


###### __src__ (source) #######

Здесь необходимо указать директории, которые будут загружены в хранилище

![](/images/cloudstorage/backupninja/bn_cloud_004.png)

Exclude позволяет указать файлы, которые не будут включены в резервную копию

![](/images/cloudstorage/backupninja/bn_cloud_005.png)

###### __dest__ (destination) ######


На данном этапе указываются для загрузки резервной копии в хранилище:

![](/images/cloudstorage/backupninja/bn_cloud_006.png)


Параметры, __обязательные__ для заполнения для исправной работы системы резервного копирования:

storage
: уникальный идетификатор хранилища

apikey
: уникальный ключ хранилища

_Подсказка:_ (storage и apikey можно узнать из [панели управления](https://panel.clodo.ru/#storage/))

container
: название контейнера, который будет создан и в котором будет храниться резервная копия конкретного задания. Для каждого задания лучше всего создавать отдельный контейнер. __Обратите внимание__, что контейнер должен иметь только один уровень - корневой.

name
: название задания для резервного копирования. Будет совпадать с названием задания на создание резервной копии и ее откат. Необходимо для идентификации самого задания, чтобы не возникало путаницы.

Опциональные параметры, которые могут быть оставлены настроенными по-умолчанию:

keep
: количество дней в течение которых будет храниться резервная копия

incremental
: инкрементировать резервную копию или нет (принимает значения yes/no)

increments
: количество дней, по истечению которых резервная копия будет сделана с нуля


###### __gpg__ (gnu privacy guard) ######

Здесь Вам нужно указать пароль, при которым будет зашифрована резервная копия

###### __adv__ (advanced) ######

В данной секции оставьте все значения по-умолчанию. Просто нажмите Enter.

Если Вы выполнили все шаги, то напротив каждого пункта появится надпись (Done)

Нажмите "Finish" чтобы создать задание

![](/images/cloudstorage/backupninja/bn_cloud_007.png)

Теперь, в главном меню программы отобразится созданное задание:

![](/images/cloudstorage/backupninja/bn_cloud_008.png)

Также, в дирекотрии /root/.restore/ будет создан файл с названием taskname.cstask, где taskname - это слово, которые было указано в пункте name. В данном файле хранится информация для восстановления файлов из резервной копии. Это можно сделать как на текщем сервере, так и на удаленном ПК, с установленным и настроенным ПО (duplicity, python-cloudfiles). Желательно создать резервную копию даннных файлов, т.к. в них содержится вся информация для автоматического восстановления данных.


Запуск задания
----------

Выберите задание из списка и откройте его, нажав "Enter". Теперь можно протестировать его выполнение в пределах текущего сервера (уточнить параметры для duplicity).

![](/images/cloudstorage/backupninja/bn_cloud_009.png)

Обычно, с тестом проблем не возникает, и вы можете сраз перейти к проверке создания резервной копии в хранилище. Для этого нажмите "Run".

Пример успешно отработанного задания:

![](/images/cloudstorage/backupninja/bn_cloud_010.png)


Теперь, в панели управления хранилищем можно зайти в созданный контейнер и увидеть следующие файлы. Как уже говорилось ранее - данные хранятся в зашифрованном виде.

![](/images/cloudstorage/backupninja/bn_cloud_012.png)


Восстановление файлов
----------

В пакет backupninja-cloud также входит скрипт csrestore, предназначенный для восстановления данных.

Для того, чтобы восстановить данные, которые только что были загружены в облачное хранилище, нужно выполнить команду:

	csrestore /root/.restore/root.cstask
	
Скрипт считает такие данные, как логин, apikey, и контейнер, загрузит и распакует данные в директорию /var/restored/

Пример успешного выполнения скрипта:

![](/images/cloudstorage/backupninja/bn_cloud_011.png)

В /var/restored появится директория root со всем данными.

_Обратите внимание_: если вы уже восстанавливали одноименную директорию, то перед следующим восстановлением потребуется сперва удалить старую восстановленную версию:

	rm -rf /var/restored/root
	
В противном случае скрипт не сработает и сообщит о том, что одноименная директория уже существует.
