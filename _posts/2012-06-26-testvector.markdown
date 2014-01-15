---
layout: post
status: publish
published: true
title: TestVector
author: M_Messiah
author_login: Messiah
author_email: m.muzafarov@gmail.com
author_url: http://m.muzafarov.vk.com
wordpress_id: 72
wordpress_url: http://messiah.ks8.ru/wordpress/?p=72
date: 2012-06-26 21:47:33.000000000 +06:00
categories:
- Java/Tasks
tags: []
comments: []
---
{% highlight java %} import junit.framework.TestCase;
public class TestVector extends TestCase {
public void testScalar() {
Vector3D A = new Vector3D( 1, 2, 3 );
Vector3D B = new Vector3D(1, 15, 14 );
assertTrue(A.scalar(B) == 73);
}
public void testDimension() {
Vector3D A = new Vector3D( 1, 2, 3);
assertTrue(A.dimension() == 3);
}
}{% endhighlight %}
&nbsp;

Исходники доступны на <a href="https://github.com/m-muzafarov/java_course/blob/master/Vectors/TestVector.java" target="_blank">GitHub</a>
