---
layout: post
status: publish
published: true
title: Hadoop MapReduce
author: m-messiah
author_login: m-messiah
author_email: m.muzafarov@gmail.com
author_url: https://github.com/m-messiah
date: 2013-09-13 15:52:03.000000000 +06:00
categories: ROT
tags: []
comments: true
---

Задачка из курса Распределённых Объектных технологий.

Скрипты mapper{Uno,Double,Triple}.py соответственно выделяют из текста слова, пары и триплеты. И подсчитывают число вхождений каждого из них.

Для универсальности, игнорируют знаки препинания (кроме конца предложения) и регистр.

Требуют работающего [Hadoop](http://hadoop.apache.org/) с включенным [HadoopStreaming](http://hadoop.apache.org/docs/stable/streaming.html)

<!--more-->

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

```bash
#!/bin/bash

./main.sh run Uno && ./main.sh run Double && ./main.sh run Triple || echo "Something shit happens."
```

**main.sh**

```bash
#!/bin/bash
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
esac
```

**mapperUno.py**

```python
#!/usr/bin/env python3

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
    main()
```

**mapperDouble.py**

```python
#!/usr/bin/env python3

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
    main()
```

**mapperTriple.py**

```python
#!/usr/bin/env python3

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
    main()
```

**reducer.py**

```python
#!/usr/bin/env python3

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
    main()
```


[GitHub](https://github.com/m-muzafarov/ROT/tree/master/Task3)
