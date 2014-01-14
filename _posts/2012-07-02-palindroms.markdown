---
layout: post
status: publish
published: true
title: Числа-палиндромы
author: ––=Messiλh=––
author_login: Messiah
author_email: m.muzafarov@gmail.com
author_url: http://m.muzafarov.vk.com
wordpress_id: 160
wordpress_url: http://messiah.ks8.ru/wordpress/?p=160
date: 2012-07-02 11:49:45.000000000 +06:00
categories:
- C++
tags: []
comments: []
---
<pre class="brush: cpp; gutter: true"> &#47;&#47; ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
 &#47;&#47; ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
 &#47;&#47; ~~ Задача №14 ~~
 &#47;&#47; ~~ По заданному числу М строится последовательность, ~~
 &#47;&#47; ~~ в которой первый элемент равен М, а каждый ~~
 &#47;&#47; ~~ следующий - полусумма элемента и его перевертыша. ~~
 &#47;&#47; ~~ По каждой из некоторого количества пар чисел М, К ~~
 &#47;&#47; ~~ указать встретился ли палиндром среди не более чем~~
 &#47;&#47; ~~ К первых элементов последовательности, ~~
 &#47;&#47; ~~ порождаемой числом М. ~~
 &#47;&#47; ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
 &#47;&#47; ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
#include <iostream>
 #include <cmath>
 using namespace std;
 int m,k;
 &#47;&#47;Нахождение перевертыша.
 int revers (int n)
 {
 int a,b;
 a=n;
 for(b=0; a>0; a&#47;=10)
 {
 b=a%10 + b*10;
 }
 return b;
 }
 &#47;&#47;Нахождение следующего элемента последовательности.
 int next (int n)
 {return (n+revers(n))&#47;2; }
 &#47;&#47;Проверка на палиндром.
 bool palindrom (int m, int k)
 { int a,b;
 if (m==revers(m)) goto lab;
 b=m; a=0;
 do
 {
 b=next(b);
 if (b==revers(b)) goto lab;
 ++a;
 } while (a<=k);
 return false;
 lab: cout<<b<<endl;
 return true;
}
 int main()
 {
 cout<<"Vvedite n \n";
 cin>>m;
 cout<<revers(m)<<endl;
 cout<<"Kol-vo? ";
 cin>>k;
 if (palindrom(m,k)==true) cout<<"Palindrom byl \n"; else cout<<"Palindroma ne bylo \n";  }<&#47;pre>
