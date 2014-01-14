---
layout: post
status: publish
published: true
title: TestVector
author: ––=Messiλh=––
author_login: Messiah
author_email: m.muzafarov@gmail.com
author_url: http://m.muzafarov.vk.com
wordpress_id: 72
wordpress_url: http://messiah.ks8.ru/wordpress/?p=72
date: 2012-06-26 21:47:33.000000000 +06:00
categories:
- Java&#47;Tasks
tags: []
comments: []
---
<pre class="brush: java; gutter: true"> import junit.framework.TestCase;
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
}<&#47;pre>
&nbsp;

Исходники доступны на <a href="https:&#47;&#47;github.com&#47;m-muzafarov&#47;java_course&#47;blob&#47;master&#47;Vectors&#47;TestVector.java" target="_blank">GitHub<&#47;a>
