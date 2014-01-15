---
layout: post
status: publish
published: true
title: Thrift client-server
author: M_Messiah
author_login: Messiah
author_email: m.muzafarov@gmail.com
author_url: http://m.muzafarov.vk.com
excerpt: "Client написан на C++, server - на Python. Thrift позволяет вызывать одному
  субъекты вызывать методы другого.\r\n\r\n"
date: 2013-09-13 16:30:50.000000000 +06:00
categories:
- C++
- Python
tags: []
comments: []
---
Client написан на C++, server - на Python. Thrift позволяет вызывать одному субъекты вызывать методы другого.


## thrift ##
Описывает две функции: ping и подсчёт количества тэгов.
{% highlight cpp %}#!/usr/local/bin/thrift

namespace cpp Parser
namespace py Parser

service Parser {
  void ping(),
  i32 count(1:string page, 2:string tag)
}{% endhighlight %}
## server ##
Python для парсинга страниц на количество тэгов. Принимает на вход текст страницы и искомый тэг. На выходе возвращает количество таких тэгов на странице.
{% highlight python %}#!/usr/bin/python
# -*- coding: utf-8 -*-
__author__ = 'Messiah'

import sys

sys.path.append('./gen-py')

from Parser import Parser
from Parser.ttypes import *
from re import findall, MULTILINE, DOTALL, IGNORECASE
from thrift.transport import TSocket
from thrift.transport import TTransport
from thrift.protocol import TBinaryProtocol
from thrift.server import TServer

class ParserHandler:
    def __init__(self):
        self.log = {}

    def ping(self):
        print "ping()"

    def count(self, page, tag):
        c = len(findall("<{0}.*?>.*?</{0}>".format(tag),
                        page, MULTILINE | DOTALL | IGNORECASE))
        c += len(findall("<{0}[^<>]*?/>".format(tag),
                         page, MULTILINE | DOTALL | IGNORECASE))
        return c

handler = ParserHandler()
processor = Parser.Processor(handler)
transport = TSocket.TServerSocket(port=9090)
tfactory = TTransport.TBufferedTransportFactory()
pfactory = TBinaryProtocol.TBinaryProtocolFactory()

server = TServer.TSimpleServer(processor, transport, tfactory, pfactory)

print "Starting python server..."
try:
    server.serve()
except KeyboardInterrupt:
    print "Kill signal received..."
print "done!"{% endhighlight %}
## client ##
С++. Принимает на вход URL и тэг. Скачивает страницу, отправляет текст серверу, возвращает количество искомых тегов.
{% highlight cpp %}#include "./gen-cpp/Parser.h"
#include <iostream>
#include <curl/curl.h>
#include <thrift/transport/TSocket.h>
#include <thrift/transport/TBufferTransports.h>
#include <thrift/protocol/TBinaryProtocol.h>

using namespace apache::thrift;
using namespace apache::thrift::protocol;
using namespace apache::thrift::transport;
using namespace std;
using namespace Parser;

int somecallback(char * data, int size, int nmemb, std::string* buffer){
       buffer->append(std::string(data));
       return (size* nmemb);
    }

int main(int argc, char **argv) {
    if (argc < 3) {
        cout << "Using: " << argv[0] << " <URL> <tag>" << endl;
        return 1;
    }
    string buffer;
    CURL * curl = curl_easy_init();
    curl_easy_setopt(curl, CURLOPT_URL, argv[1]);
    curl_easy_setopt(curl, CURLOPT_FOLLOWLOCATION, 1);
    curl_easy_setopt(curl, CURLOPT_WRITEFUNCTION, somecallback);
    curl_easy_setopt(curl, CURLOPT_WRITEDATA, &buffer);
    curl_easy_perform(curl);

    string tag = argv[2];

    boost::shared_ptr<TSocket> socket(new TSocket("localhost", 9090));
    boost::shared_ptr<TTransport> transport(new TBufferedTransport(socket));
    boost::shared_ptr<TProtocol> protocol(new TBinaryProtocol(transport));

    ParserClient client(protocol);
    transport->open();
    //client.ping();
    int c = client.count(buffer, tag);
    cout << tag << " = " << c << endl;
    transport->close();
    return 0;
}{% endhighlight %}
# Компиляция и зависимости #
Требует установленного thrift, libcurl, g++

Для С++ thrift успешно собирается по инструкции с сайта. Python у меня его не увидел (возможно недособрал) - решается установкой python-thrift:

    pip install thrift

Сборка происходит с помощью утилиты make.

**Makefile**
{% highlight bash %}
.PHONY: build thrift client clean

build: thrift client

thrift: parser.thrift
	thrift --gen cpp parser.thrift
	thrift --gen py  parser.thrift

client: thrift client.cpp
	CFLAGS="-O3" g++ -DHAVE_INTTYPES_H -DHAVE_NETINET_IN_H -Wall -lthrift -lcurl gen-cpp/Parser.cpp -o client client.cpp

clean:
	rm -rf ./gen-*/ client

{% endhighlight %}
На выходе будет доступно два исполняемых файла: server.py и client.elf

<a href="https://github.com/m-muzafarov/ROT/tree/master/Task8"><img class="alignleft size-full wp-image-258" alt="github-button" src="http://messiah.ks8.ru/wp-content/uploads/github-button.png" width="170" height="72" /></a>
