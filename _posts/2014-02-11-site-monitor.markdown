---
layout: post
status: publish
published: true
title: Site monitor
author: M_Messiah
author_login: M_Messiah
author_email: m.muzafarov@gmail.com
author_url: http://brainstorage.me/M_Messiah
date: 2014-02-11 13:11:00 +06:00
categories: network
tags: []
comments: true
---

На днях позарез нужно было мониторить, обновилась ли страничка на сайте, поэтому на скорую руку набросал скрипт на bash с уведомлением в Центре Уведомлений (Mac OSX).

В принципе, тут и так все понятно - пост только чтобы код сохранился где-нибудь на будущее, но я все же прокомментирую:

раз в минуту запрашивается нужный адрес, где ищется строка, которая сейчас на сайте есть, а после обновления должна пропасть, и если найдена - высылается уведомление, по клику на котором - открывается искомый адрес.

<!--more-->

Код без изменений - для проверки, появился ли анонс на следующую игру DozoR.
{% highlight bash %}
#!/bin/bash

HIT=0
while true; do
    sleep 60
    curl -s http://classic.dzzzr.ru/e-burg/?section=anons | iconv -f cp1251 -t utf8 | grep "Нет запланированных игр" > /dev/null || ANONS=1
    if [ $ANONS -gt 0 ]; then
        terminal-notifier -message "Anons appears" -title "DozoR anons" -open http://classic.dzzzr.ru/e-burg/?section=anons
        exit 0
    fi
done;
{% endhighlight %}