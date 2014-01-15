---
layout: post
published: true
title: Wordpress to Jekyll
author: M_Messiah
author_login: Messiah
author_email: m.muzafarov@gmail.com
author_url: http://brainstorage.me/M_Messiah
date: 2014-01-15 15:43:00.000000000 +06:00
categories: Web
tags: []
comments: []
---

В связи с частыми проблемами хостера и какой-то глючной темой с блокированием многих функций Wordpress'а, сначала появилась идея найти другого хостера.

Глянул бесплатных - красивыми доменными именами не привлекли, а рыться в возможностях стало лень - как результат появилась идея переезда на GitHub-Pages.

По счастью, Jekyll уже имеет необходимого импортера с WP, но он подключается только к базе, а соответственно теряются все метрики, стили, оформления и прочее, чем так славился WP. 

"Чтож, не проблема", подумал я и начал ваять заново, благо с Markdown уже знаком.

<!--more-->

Для начала устанавливаем себе jekyll и создаем сайт.

{% highlight bash %}
gem install jekyll
gem install mysql # нужно только для моего способа
gem install jekyll-import
jekyll new myblog
cd myblog
{% endhighlight %}

После чего можно либо экспортировать WP в XML файл (Консоль управления - Инструменты - Экспорт),
либо напрямую подключиться к базе данных и взять данные оттуда.

У меня были какие-то проблемы с импортом из XML, поэтому я решил попробовать способ с базой.

{% highlight bash %}
ruby -rubygems -e 'require "jekyll-import";
    JekyllImport::Importers::WordPress.run({
     "dbname"   => "<dbname>",
     "user"     => "<username>",
     "password" => "<password>",
     "host"     => "<hostname>",
     "prefix"   => "wp_",
     "clean_entities" => true,
     "comments"       => true,
     "categories"     => true,
     "tags"           => true,
     "more_excerpt"   => true,
     "more_anchor"    => true,
     "status"         => ["publish"]
      })'
{% endhighlight %}

Благодаря установленным флагам, jekyll сохранит все коментарии, которые были у записей (к сожалению, у меня не было, поэтому не знаю, как это выглядит),
сохранит страницы категорий, если они были (в качестве тоже записей блога), а также применит категории и тэги из WP. Предпоследние флаги позволяют сохранить поведение "Read more...", а последний присвоит всем записям статус опубликованных, чтобы они сразу были видны. При таком импорте в Jekyll не попадают черновики и нужно добавить их самостоятельно, если имеются.

После чего можно проверить работспособность, с помощью {% highlight bash %}jekyll serve{% endhighlight %}

