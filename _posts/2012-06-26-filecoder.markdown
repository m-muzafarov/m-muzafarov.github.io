---
layout: post
status: publish
published: true
title: Конвертер кодировок
author: ––=Messiλh=––
author_login: Messiah
author_email: m.muzafarov@gmail.com
author_url: http://m.muzafarov.vk.com
excerpt: "Листинг\r\n\r\n"
wordpress_id: 77
wordpress_url: http://messiah.ks8.ru/wordpress/?p=77
date: 2012-06-26 21:49:59.000000000 +06:00
categories:
- Java&#47;Tasks
tags: []
comments: []
---
Листинг

<a id="more"></a><a id="more-77"></a>
<pre class="brush: java; gutter: true"> import java.io.BufferedOutputStream;
 import java.io.File;
 import java.io.FileInputStream;
 import java.io.FileOutputStream;
 import java.io.IOException;
 import java.io.InputStreamReader;
 import java.io.OutputStream;
 import java.io.OutputStreamWriter;
 import java.io.PrintWriter;
 import java.util.Scanner;
public class Coder {
 &#47;**
 * @param args
 *&#47;
 public static void main(String[] args) {
 if (args.length < 4) {
 System.out.println("ERROR: Запустите программу с аргументами: имя_входного_файла кодировка имя_выходного_файла кодировка\n");
 return;
 }
 File file_in = new File(args[0]);
 System.out.println("Input file is: " + args[0]);
 System.out.println("Input encoding is: " + args[1]);
 System.out.println("Output file is: " + args[2]);
 System.out.println("Output encoding is: " + args[3]);
 File file_out = new File(args[2]);
 try {
 Scanner scanner = new Scanner(new InputStreamReader(
 new FileInputStream(file_in), args[1]));
 OutputStream os = new FileOutputStream(file_out);
 try {
 PrintWriter writer = new PrintWriter(new OutputStreamWriter(
 new BufferedOutputStream(os), args[3]));
 try {
 while (scanner.hasNextLine())
 writer.println(scanner.nextLine());
 } finally {
 writer.close();
 }
 } finally {
 os.close();
 }
 } catch (IOException ex) {
 ex.printStackTrace();
 }
 }
 }<&#47;pre>
Исходники доступны на <a href="https:&#47;&#47;github.com&#47;m-muzafarov&#47;java_course&#47;blob&#47;master&#47;FileCoder.java" target="_blank">GitHub<&#47;a>
