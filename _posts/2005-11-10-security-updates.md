---
title: Установка обновлений безопасности
description: "Установка обновлений безопасности"
layout: article
category: Первые шаги
---


После первого входа на Ваш новый сервер Вам необходимо установить все последние обновления для пакетов, используемых в системе. Это поможет защитить Ваш сервер от несанкционированного доступа и обновит все установленные пакеты до последних версий, имеющихся в репозитории установленного дистрибутива.
   

Debian/Ubuntu:

	apt-get update
	apt-get upgrade --show-upgraded

CentOS/Fedora:

	yum update