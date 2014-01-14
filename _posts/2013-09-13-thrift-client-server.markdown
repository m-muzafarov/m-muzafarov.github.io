---
layout: post
status: publish
published: true
title: Thrift client-server
author: ––=Messiλh=––
author_login: Messiah
author_email: m.muzafarov@gmail.com
author_url: http://m.muzafarov.vk.com
excerpt: "Client написан на C++, server - на Python. Thrift позволяет вызывать одному
  субъекты вызывать методы другого.\r\n\r\n"
wordpress_id: 256
wordpress_url: http://messiah.ks8.ru/?p=256
date: 2013-09-13 16:30:50.000000000 +06:00
categories:
- C++
- Python
tags: []
comments: []
---
Client написан на C++, server - на Python. Thrift позволяет вызывать одному субъекты вызывать методы другого.

<a id="more"></a><a id="more-256"></a>
<h2>thrift<&#47;h2>
Описывает две функции: ping и подсчёт количества тэгов.
<pre class="brush: cpp; gutter: false">#!&#47;usr&#47;local&#47;bin&#47;thrift

namespace cpp Parser
namespace py Parser

service Parser {
  void ping(),
  i32 count(1:string page, 2:string tag)
}<&#47;pre>
<h2>server<&#47;h2>
Python для парсинга страниц на количество тэгов. Принимает на вход текст страницы и искомый тэг. На выходе возвращает количество таких тэгов на странице.
<pre class="brush: python; gutter: true">#!&#47;usr&#47;bin&#47;python
# -*- coding: utf-8 -*-
__author__ = &#039;Messiah&#039;

import sys

sys.path.append(&#039;.&#47;gen-py&#039;)

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
        c = len(findall("<{0}.*?>.*?<&#47;{0}>".format(tag),
                        page, MULTILINE | DOTALL | IGNORECASE))
        c += len(findall("<{0}[^<>]*?&#47;>".format(tag),
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
print "done!"<&#47;pre>
<h2>client<&#47;h2>
С++. Принимает на вход URL и тэг. Скачивает страницу, отправляет текст серверу, возвращает количество искомых тегов.
<pre class="brush: cpp; gutter: true">#include ".&#47;gen-cpp&#47;Parser.h"
#include <iostream>
#include <curl&#47;curl.h>
#include <thrift&#47;transport&#47;TSocket.h>
#include <thrift&#47;transport&#47;TBufferTransports.h>
#include <thrift&#47;protocol&#47;TBinaryProtocol.h>

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
    curl_easy_setopt(curl, CURLOPT_WRITEDATA, &amp;buffer);
    curl_easy_perform(curl);

    string tag = argv[2];

    boost::shared_ptr<TSocket> socket(new TSocket("localhost", 9090));
    boost::shared_ptr<TTransport> transport(new TBufferedTransport(socket));
    boost::shared_ptr<TProtocol> protocol(new TBinaryProtocol(transport));

    ParserClient client(protocol);
    transport->open();
    &#47;&#47;client.ping();
    int c = client.count(buffer, tag);
    cout << tag << " = " << c << endl;
    transport->close();
    return 0;
}<&#47;pre>
<h1>Компиляция и зависимости<&#47;h1>
Требует установленного thrift, libcurl, g++

Для С++ thrift успешно собирается по инструкции с сайта. Python у меня его не увидел (возможно недособрал) - решается установкой python-thrift:
<pre><code>pip install thrift
<&#47;code><&#47;pre>
Сборка происходит с помощью утилиты make.

<strong>Makefile<&#47;strong>
<pre class="brush: bash; gutter: true">.PHONY: build thrift client clean

build: thrift client

thrift: parser.thrift
	thrift --gen cpp parser.thrift
	thrift --gen py  parser.thrift

client: thrift client.cpp
	CFLAGS="-O3" g++ -DHAVE_INTTYPES_H -DHAVE_NETINET_IN_H -Wall -lthrift -lcurl gen-cpp&#47;Parser.cpp -o client client.cpp

clean:
	rm -rf .&#47;gen-*&#47; client<&#47;pre>
На выходе будет доступно два исполняемых файла: server.py и client.elf

<a href="https:&#47;&#47;github.com&#47;m-muzafarov&#47;ROT&#47;tree&#47;master&#47;Task8"><img class="alignleft size-full wp-image-258" alt="github-button" src="http:&#47;&#47;messiah.ks8.ru&#47;wp-content&#47;uploads&#47;github-button.png" width="170" height="72" &#47;><&#47;a>
