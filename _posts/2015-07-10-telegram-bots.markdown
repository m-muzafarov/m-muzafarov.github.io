---
layout: post
status: publish
published: true
title: Инструкция. Как создавать ботов в Telegram
excerpt_separator: ""
author: m-messiah
author_login: m-messiah
author_email: m.muzafarov@gmail.com
author_url: https://m-messiah.ru
date: 2015-07-10 10:20:00 +03:00
categories: python
tags: []
comments: true
---

24 июня разработчики Telegram [открыли](https://core.telegram.org/bots) платформу для создания ботов. Новость кого-то обошла стороной Хабр, однако многие уже начали разрабатывать викторины. При этом мало где указаны хоть какие-то примеры работающих ботов. Попробую это исправить.

Прежде всего, бот для Telegram - это по-прежнему приложение, запущенное на вашей стороне и осуществляющее запросы к [Telegram Bot API](https://core.telegram.org/bots/api). Причем API довольное простое - бот обращается на определенный URL с параметрами, а Telegram отвечает JSON объектом. 

Полную статью (с правками от 30 ноября) лучше читать на [habrahabr](http://habrahabr.ru/post/262247/)

