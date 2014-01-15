---
layout: post
status: publish
published: true
title: Hadoop MapReduce
author: M_Messiah
author_login: Messiah
author_email: m.muzafarov@gmail.com
author_url: http://m.muzafarov.vk.com
excerpt: "Задачка из курса Распределённых Объектных технологий.\r\n\r\nСкрипты mapper{Uno,Double,Triple}.py
  соответственно выделяют из текста слова, пары и триплеты. И подсчитывают число вхождений
  каждого из них.\r\n\r\nДля универсальности, игнорируют знаки препинания (кроме конца
  предложения) и регистр.\r\n\r\nТребуют работающего <a href=\"http://hadoop.apache.org/\">Hadoop</a>&nbsp;с
  включенным <a href=\"http://hadoop.apache.org/docs/stable/streaming.html\">HadoopStreaming</a>\r\n\r\n"
wordpress_id: 250
wordpress_url: http://messiah.ks8.ru/?p=250
date: 2013-09-13 15:52:03.000000000 +06:00
categories:
- Python
tags: []
comments: []
---
Задачка из курса Распределённых Объектных технологий.

Скрипты mapper{Uno,Double,Triple}.py соответственно выделяют из текста слова, пары и триплеты. И подсчитывают число вхождений каждого из них.

Для универсальности, игнорируют знаки препинания (кроме конца предложения) и регистр.

Требуют работающего <a href="http://hadoop.apache.org/">Hadoop</a>&nbsp;с включенным <a href="http://hadoop.apache.org/docs/stable/streaming.html">HadoopStreaming</a>

**Подготовка Запуск**
<ul>
	<li>В файле main.sh необходимо указать верные пути до JRE и HADOOP_HOME, а также указать пользователя с правами на запись в HDFS</li>
	<li>В папку books положить нужные тексты</li>
	<li>Скопировать тексты в HDFS: ./main.sh copy</li>
	<li>Для запуска сразу всех задач сделан конвейер ./run.sh</li>
	<li>Для отдельных задач используем ./main.sh run , где name = {Uno,Double,Triple}</li>
	<li>Для того, чтобы просмотреть содержимое каталога используем ./main.sh ls</li>
	<li>Содержимое файла ./main show</li>
	<li>Удалить (rm -r) -> ./main.sh delete</li>
</ul>
**Запуск всех задач подряд**
{% highlight bash %}#!/bin/bash

./main.sh run Uno && ./main.sh run Double && ./main.sh run Triple || echo "Something shit happens."{% endhighlight %}
**main.sh**
{% highlight bash %}#!/bin/bash
export HADOOP_HOME=/usr/local/hadoop
export JAVA_HOME=/usr/lib/jvm/java-7-openjdk-i386/jre/
export HADOOP_USER=hduser

case "$1" in 
    run)
        sudo -u ${HADOOP_USER} ${HADOOP_HOME}/bin/hadoop jar ${HADOOP_HOME}/contrib/streaming/hadoop-streaming.jar \
         -file mapper${2}.py -mapper mapper${2}.py -file reducer.py -reducer reducer.py \
         -input books/* -output books-output-$2
        ;;
    show)
        sudo -u ${HADOOP_USER} ${HADOOP_HOME}/bin/hadoop fs -cat books-output-$2/part-00000
        ;;
    delete)
        sudo -u ${HADOOP_USER} ${HADOOP_HOME}/bin/hadoop fs -rmr books-output-$2
        #sudo -u ${HADOOP_USER} ${HADOOP_HOME}/bin/hadoop fs -rmr books
        ;;
    ls)
        sudo -u ${HADOOP_USER} ${HADOOP_HOME}/bin/hadoop fs -ls $2
        ;;
    copy)
        sudo -u ${HADOOP_USER} ${HADOOP_HOME}/bin/hadoop dfs -copyFromLocal books books 
        ;;
    *)
        echo "Usage $0 {run|show|delete|copy|ls} " >&2
        exit 3
        ;;
esac{% endhighlight %}
**mapperUno.py**
{% highlight python %}#!/usr/bin/env python3

import sys
import re

def read_input(file):
    for line in file:
        # split the line into words
        yield re.sub(r'\W', ' ', line.lower()).split()

def main(separator='\t'):
    # input comes from STDIN (standard input)
    data = read_input(sys.stdin)
    for triplets in data:
        for triplet in triplets:
            print('{}{}{}'.format(triplet, separator, 1))

if __name__ == "__main__":
    main(){% endhighlight %}
**mapperDouble.py**
{% highlight python %}#!/usr/bin/env python3

import sys
import re

def read_input(file):
    for line in file:
        # split the line into couples
        unpunct_line = re.sub(r'\W', ' ', line.lower())
        words = unpunct_line.split()
        yield ["{} {}".format(words[i - 1],
                              words[i]) for i in range(1, len(words))]

def main(separator='\t'):
    # input comes from STDIN (standard input)
    data = read_input(sys.stdin)
    for triplets in data:
        for triplet in triplets:
            print('{}{}{}'.format(triplet, separator, 1))

if __name__ == "__main__":
    main(){% endhighlight %}
**mapperTriple.py**
{% highlight python %}#!/usr/bin/env python3

import sys
import re

def read_input(file):
    for line in file:
        # split the line into triplets
        unpunct_line = re.sub(r'\W', ' ', line.lower())
        words = unpunct_line.split()
        yield ["{} {} {}".format(words[i - 2],
                                 words[i - 1],
                                 words[i]) for i in range(2, len(words))]

def main(separator='\t'):
    # input comes from STDIN (standard input)
    data = read_input(sys.stdin)
    for triplets in data:
        for triplet in triplets:
            print('{}{}{}'.format(triplet, separator, 1))

if __name__ == "__main__":
    main(){% endhighlight %}
**reducer.py**
{% highlight python %}#!/usr/bin/env python3

from itertools import groupby
from operator import itemgetter
import sys

def read_mapper_output(file, separator='\t'):
    for line in file:
        yield line.rstrip().split(separator, 1)

def main(separator='\t'):
    # input comes from STDIN (standard input)
    data = read_mapper_output(sys.stdin, separator=separator)
    for current_unit, group in groupby(data, itemgetter(0)):
        try:
            total_count = sum(int(count) for current_unit, count in group)
            print("{}{}{}".format(current_unit, separator, total_count))
        except ValueError:
            # count was not a number, so silently discard this item
            pass

if __name__ == "__main__":
    main(){% endhighlight %}

<a href="https://github.com/m-muzafarov/ROT/tree/master/Task3"><img src="http://messiah.ks8.ru/wp-content/uploads/github-button.png" alt="github-button" width="170" height="72" class="alignleft size-full wp-image-258" /></a>
