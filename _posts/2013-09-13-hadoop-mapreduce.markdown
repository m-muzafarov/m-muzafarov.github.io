---
layout: post
status: publish
published: true
title: Hadoop MapReduce
author: ––=Messiλh=––
author_login: Messiah
author_email: m.muzafarov@gmail.com
author_url: http://m.muzafarov.vk.com
excerpt: "Задачка из курса Распределённых Объектных технологий.\r\n\r\nСкрипты mapper{Uno,Double,Triple}.py
  соответственно выделяют из текста слова, пары и триплеты. И подсчитывают число вхождений
  каждого из них.\r\n\r\nДля универсальности, игнорируют знаки препинания (кроме конца
  предложения) и регистр.\r\n\r\nТребуют работающего <a href=\"http:&#47;&#47;hadoop.apache.org&#47;\">Hadoop<&#47;a>&nbsp;с
  включенным <a href=\"http:&#47;&#47;hadoop.apache.org&#47;docs&#47;stable&#47;streaming.html\">HadoopStreaming<&#47;a>\r\n\r\n"
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

Требуют работающего <a href="http:&#47;&#47;hadoop.apache.org&#47;">Hadoop<&#47;a>&nbsp;с включенным <a href="http:&#47;&#47;hadoop.apache.org&#47;docs&#47;stable&#47;streaming.html">HadoopStreaming<&#47;a>

<a id="more"></a><a id="more-250"></a><strong>Подготовка Запуск<&#47;strong>
<ul>
	<li>В файле main.sh необходимо указать верные пути до JRE и HADOOP_HOME, а также указать пользователя с правами на запись в HDFS<&#47;li>
	<li>В папку books положить нужные тексты<&#47;li>
	<li>Скопировать тексты в HDFS: .&#47;main.sh copy<&#47;li>
	<li>Для запуска сразу всех задач сделан конвейер .&#47;run.sh<&#47;li>
	<li>Для отдельных задач используем .&#47;main.sh run , где name = {Uno,Double,Triple}<&#47;li>
	<li>Для того, чтобы просмотреть содержимое каталога используем .&#47;main.sh ls<&#47;li>
	<li>Содержимое файла .&#47;main show<&#47;li>
	<li>Удалить (rm -r) -> .&#47;main.sh delete<&#47;li>
<&#47;ul>
<strong>Запуск всех задач подряд<&#47;strong>
<pre class="brush: bash; gutter: true">#!&#47;bin&#47;bash

.&#47;main.sh run Uno &amp;&amp; .&#47;main.sh run Double &amp;&amp; .&#47;main.sh run Triple || echo "Something shit happens."<&#47;pre>
<strong>main.sh<&#47;strong>
<pre class="brush: bash; gutter: true">#!&#47;bin&#47;bash
export HADOOP_HOME=&#47;usr&#47;local&#47;hadoop
export JAVA_HOME=&#47;usr&#47;lib&#47;jvm&#47;java-7-openjdk-i386&#47;jre&#47;
export HADOOP_USER=hduser

case "$1" in 
    run)
        sudo -u ${HADOOP_USER} ${HADOOP_HOME}&#47;bin&#47;hadoop jar ${HADOOP_HOME}&#47;contrib&#47;streaming&#47;hadoop-streaming.jar \
         -file mapper${2}.py -mapper mapper${2}.py -file reducer.py -reducer reducer.py \
         -input books&#47;* -output books-output-$2
        ;;
    show)
        sudo -u ${HADOOP_USER} ${HADOOP_HOME}&#47;bin&#47;hadoop fs -cat books-output-$2&#47;part-00000
        ;;
    delete)
        sudo -u ${HADOOP_USER} ${HADOOP_HOME}&#47;bin&#47;hadoop fs -rmr books-output-$2
        #sudo -u ${HADOOP_USER} ${HADOOP_HOME}&#47;bin&#47;hadoop fs -rmr books
        ;;
    ls)
        sudo -u ${HADOOP_USER} ${HADOOP_HOME}&#47;bin&#47;hadoop fs -ls $2
        ;;
    copy)
        sudo -u ${HADOOP_USER} ${HADOOP_HOME}&#47;bin&#47;hadoop dfs -copyFromLocal books books 
        ;;
    *)
        echo "Usage $0 {run|show|delete|copy|ls} " >&amp;2
        exit 3
        ;;
esac<&#47;pre>
<strong>mapperUno.py<&#47;strong>
<pre class="brush: python; gutter: true">#!&#47;usr&#47;bin&#47;env python3

import sys
import re

def read_input(file):
    for line in file:
        # split the line into words
        yield re.sub(r&#039;\W&#039;, &#039; &#039;, line.lower()).split()

def main(separator=&#039;\t&#039;):
    # input comes from STDIN (standard input)
    data = read_input(sys.stdin)
    for triplets in data:
        for triplet in triplets:
            print(&#039;{}{}{}&#039;.format(triplet, separator, 1))

if __name__ == "__main__":
    main()<&#47;pre>
<strong>mapperDouble.py<&#47;strong>
<pre class="brush: python; gutter: true">#!&#47;usr&#47;bin&#47;env python3

import sys
import re

def read_input(file):
    for line in file:
        # split the line into couples
        unpunct_line = re.sub(r&#039;\W&#039;, &#039; &#039;, line.lower())
        words = unpunct_line.split()
        yield ["{} {}".format(words[i - 1],
                              words[i]) for i in range(1, len(words))]

def main(separator=&#039;\t&#039;):
    # input comes from STDIN (standard input)
    data = read_input(sys.stdin)
    for triplets in data:
        for triplet in triplets:
            print(&#039;{}{}{}&#039;.format(triplet, separator, 1))

if __name__ == "__main__":
    main()<&#47;pre>
<strong>mapperTriple.py<&#47;strong>
<pre class="brush: python; gutter: true">#!&#47;usr&#47;bin&#47;env python3

import sys
import re

def read_input(file):
    for line in file:
        # split the line into triplets
        unpunct_line = re.sub(r&#039;\W&#039;, &#039; &#039;, line.lower())
        words = unpunct_line.split()
        yield ["{} {} {}".format(words[i - 2],
                                 words[i - 1],
                                 words[i]) for i in range(2, len(words))]

def main(separator=&#039;\t&#039;):
    # input comes from STDIN (standard input)
    data = read_input(sys.stdin)
    for triplets in data:
        for triplet in triplets:
            print(&#039;{}{}{}&#039;.format(triplet, separator, 1))

if __name__ == "__main__":
    main()<&#47;pre>
<strong>reducer.py<&#47;strong>
<pre class="brush: python; gutter: true">#!&#47;usr&#47;bin&#47;env python3

from itertools import groupby
from operator import itemgetter
import sys

def read_mapper_output(file, separator=&#039;\t&#039;):
    for line in file:
        yield line.rstrip().split(separator, 1)

def main(separator=&#039;\t&#039;):
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
    main()<&#47;pre>

<a href="https:&#47;&#47;github.com&#47;m-muzafarov&#47;ROT&#47;tree&#47;master&#47;Task3"><img src="http:&#47;&#47;messiah.ks8.ru&#47;wp-content&#47;uploads&#47;github-button.png" alt="github-button" width="170" height="72" class="alignleft size-full wp-image-258" &#47;><&#47;a>
