---
title: "CentOS репозитории"
description: "Работа с репозиториями CentOS"
layout: article
category: Используем Linux
---

* t
{:toc}

Есть несколько репозиториев, предоставляемые CentOS и сторонними разработчиками, которые содержат программные пакеты, не включенные в список базовых или обновлений. Эти хранилища имеют различные уровни стабильности, поддержки и сотрудничества в рамках сообщества CentOS.

##Дополнительные репозитории, предоставляемые CentOS

CentOS Extras - этот репозиторий включает в себя набор ПО, которое обеспечивают дополнительную функциональность CentOS без нарушения совместимости при обновлении базовых компонентов. Группа разработчиков CentOS тестирует каждый пакет в этом репозитории, и все они работают с CentOS. Этот репозиторий включен в CentOS по умолчанию.

CentOS Plus - содержит пакеты, которые обновят базовый набор ПО. Этот репозиторий изменит общий вид CentOS таким образом, что она не будет соответствовать ОС, выпущенной компанией Red Hat. Команда разработчиков CentOS тестирует каждый пакет в этом репозитории, поэтому все они будут работать в CentOS. Компания Red Hat не тестирует данное ПО, и не предоставляет его в своих продуктах. Этот репозиторий поставляется вместе с CentOS, но по умолчанию не активирован. Включает в себя такие пакеты, как postfix (с поддержкой БД), пересобранные ядра с поддержкой дополнительных файловых систем, php5 и mysql5.

CS/GFS - репозиторий с пересобранными пакетами Cluster Suite и Global File System для CentOS-4, поддерживающий архитектуры x86 и x86_64. Команда разработчиков CentOS тестирует каждый пакет в этом репозитории, поэтому все они будут работать в CentOS. Данный репозиторий не поставляется по умолчанию с CentOS, но он может быть добавлен при помощи файла, доступного по ссылке:[http://mirror.centos.org/centos-4/4/csgfs/CentOS-csgfs.repo]()

CentOS Testing - служит временным хранилищем для пакетов ПО, которые попадут в список CentOS Plus и CentOS Extras. Ими можно заменять базовые компоненты CentOS, но работа не гарантируется. Они доступны для установки, и ожидают отзывов от тестирующих насчет функциональности и стабильности. Пакеты, доступные в этом репозитории, будут добавляться и исключаться, поэтому активировать этот репозиторий на рабочих серверах не рекомендуется. Файл конфигурации репозитория доступен по адресу:[http://dev.centos.org/centos/5/CentOS-Testing.repo]()

CentOS-Fasttrack - репозиторий, содержащий в себе исправление ошибок и дополнительные обновления, появляющиеся время от времени, ожидающие следующего релиза. Fasttrack доступен для CentOS 5 по данной ссылке:[http://mirror.centos.org/centos/5/fasttrack/CentOS-Fasttrack.repo]()

Contrib - репозиторий содержит пакеты, созданные пользователями. Данное ПО никак не пересекается с какими-либо пакетами, входящими с втандартную комплектацию операционной системы. Они не тестируются разработчиками и могут не соответствовать актуальным версиям ОС.

##Сторонние репозитории

RPMForge - репозиторий принадлежит одному из разработчиков CentOS. Данный репозиторий содержит в себе около 10000 пакетов, среди которых есть MPlayer, XMMS-mp3, и других распространенных пакетов, для работы с мультимедийными файлами. Более подробную информацию можно получить на его сайте:[http://dag.wieers.com/rpm/]()

KBS-Extras - еще один репозиторий от разработчика CentOS. Содержит в себе пакеты от Fedora Extras, пропатченные для работы в CentOS. Доступен по адресу [http://centos.karan.org/](). Он также получил репутацию стабильного и безопасного источника ПО.

EPEL (Extra Packages for Enterprise Linux) - содержит пересобранные пакеты из репозиториев для EL4 и EL5 для Fedora Linux. Установите соответствующий epel-release пакет для настройки EL5 или EL6. При использовании данного репозитория стоит обратить внимение на то, что он не может заменить пакеты, установленные из CentOS-Extras, активированного с системе по умолчанию. Во избежание подобного, рекомендуется использовать плагин Priority для Yum. Особенно, если вы используете ПО из иных сторонних репозиториев.

RPMfusion Repository - включает в себя пакеты, которые не предоствляются Red Hat или Fedora. Для EL5 находятся в тестовой стадии. Для более подробной информации смотрите [http://rpmfusion.org/]()

Les RPM de Remi repository - содержит бэкпорты Mysql и PHP, собранные из rpm-пкетов для Fedora. В ходе обновления могут быть заменены пакеты из основной ветки.

The Community Enterprise Linux Repository (EPEL) - community-репозиторий, сфокусированный на приложениях для мониторинга, работы сетью, звуком, и драйверами. Делится на 4 ветки.

* **elrepo** - стандартная ветка. Добавляется следующей командой:

        yum --enablerepo=elrepo


* **elrepo-testing** - содержит пакеты. который должны войти в страндартную ветку. По-умолчанию не активированы. Можно активировать, выполнив команду:

        yum --enablerepo=elrepo-testing

* **elrepo-kernel** - содержит последние стабильные версии ядер, сконфигурированных для EL5. По умолчанию не активированы. Именование ядер изменено на kernel-ml, чтобы не конфликтовать с ядрами CentOS/EL5, поэтому их можно устанавливать и обновлять одновременно со стандартными ядрами. Активировать репозиторий можно, выполнив команду:

        yum --enablerepo=elrepo-kernel

* **elrepo-extras** - содержит обновленные пакеты для CentOS (или любой другой совместимой ОС). По умолчанию не активирован, т.к. могут заменять пакеты самого дистрибутива. Активируется командой:

        yum --enablerepo=elrepo-extras