---
title: "Утилиты для мониторинга"
description: "Мониторинг Linux-системы"
layout: article
category: Используем Linux
---


* t
{:toc}

## free

Команда free отображает общий объем свободной, занятой физической памяти и пространства подкачки в системе, а также буферы, используемые ядром.


Строка Mem отображает использование физической памяти, строка Swap - использование пространства подкачки вашей системой.


Строка -/+ buffers/cache отображает объем физической памяти, выделенной в настоящий момент. Обычно данную команду используют с ключом -m, который задает вывод информации в мегабайтах, что облегчает ее восприятие.

![](/images/linux/monitoring/free.png)

## ps

Команда ps выводит список работающих процессов в вашей системе. Обычно она запускается согласно синтаксису BSD:

        ps ax

Для того, чтобы она отобразила имя владельца процесса, используется ps aux. Результат выполнения программы является статичным (он актуален на момент выполнения команды). Обычно, вывод команды достаточно длинный, чтобы уместиться на экране, поэтому будет удобнее пропускать ее через команду less:

        ps aux | less

Чтобы найти определенный процесс среди запущенных, используйте команду grep:

        ps aux | grep httpd

## top

Данная команда используется для мониторинга и управления процессами на сервере. Она отображает информацию о процессах,отображает список заданий, и Process ID (PID).

![](/images/linux/monitoring/top.png)


Вид отображаемой информации можно модифицировать. Приведем список клавиш для работы с запущенной утилитой top:


M (Shift+m) - отсортирует процессы по объему используемой ОЗУ


P - сортировка процессов по объему использования процессора


n - изменить число отображаемых процессов


u - сортировка процессов по имени пользователя


k - уничтожить процесс. Программа также выдаст список команд для уничтожения.Обычно используется сигнал kill (без соблюдения целостности данных).


space (пробел) - обновить информацию о процессах


s - установить время в секундах для обновления. По-умолчанию установлено значение 3. Не рекомендуется использовать значение 0, т.к. данное действие приведет к усиленному потреблению процессора.


h - вызвать справку


q - завершить работу программы

## htop

htop используется для тех же целей, что и top. Данная программа не включена в дистрибутивы по-умолчанию, поэтому для использования ее необходимо установить. Отличается от top внешним видом и возможностью работать в интерактивном режиме.

![](/images/linux/monitoring/htop.png)


Список клавиш для работы с htop:


M (Shift+m) - отсортирует процессы по объему используемой ОЗУ


P - сортировка процессов по объему использования процессора


u - выбрать пользователя, владельцем которых он является


F1 - справка по использованию программы


F2 - настройка программы


Остальные функциональные клавиши и их назначение вы можете видеть в нижней части интерфейса программы


q - завершить работу программы