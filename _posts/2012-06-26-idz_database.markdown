---
layout: post
status: publish
published: true
title: База подарков
author: ––=Messiλh=––
author_login: Messiah
author_email: m.muzafarov@gmail.com
author_url: http://m.muzafarov.vk.com
excerpt: "Создание и заполнение базы\r\n\r\n"
wordpress_id: 121
wordpress_url: http://messiah.ks8.ru/wordpress/?p=121
date: 2012-06-26 23:55:38.000000000 +06:00
categories:
- SQL
tags: []
comments: []
---
Создание и заполнение базы

<a id="more"></a><a id="more-121"></a>
<pre class="brush: sql; gutter: true">USE [master]

IF EXISTS (SELECT name FROM sys.DATABASES WHERE name = N&#039;presents&#039;)

DROP DATABASE [presents]

GO

CREATE DATABASE [presents]

GO

USE [presents]

GO

CREATE TABLE [dbo].Category( ID_category [INT] NOT NULL,

 Name_category [VARCHAR](50) NOT NULL,

CONSTRAINT PK_category PRIMARY KEY(ID_category))

CREATE TABLE [dbo].Tovar( ID_Tovara [INT] NOT NULL,

 Name_Tovara [VARCHAR](50) NOT NULL,

 Price_tovar [DECIMAL] (10,2) NOT NULL,

 Category_tovara [INT] NOT NULL REFERENCES Category (ID_Category),

CONSTRAINT PK_Tovar PRIMARY KEY (ID_Tovara)

)

CREATE TABLE [dbo].Manager( ID_manager [INT] NOT NULL,

 Name_manager [VARCHAR](50) NOT NULL,

 Address_manager [VARCHAR] (50) NOT NULL,

 Data_manager [datetime] NOT NULL,

 ID_category[INT] NOT NULL REFERENCES Category(ID_Category),

CONSTRAINT PK_Manager PRIMARY KEY (ID_manager)

)

CREATE TABLE Client( ID_Client [INT] NOT NULL,

 Name_client [VARCHAR](50) NOT NULL,

 Adress_Client [VARCHAR] (60) NOT NULL,

 Summa_Client [BIGINT] NOT NULL,

 Sale_Client [tinyint] NOT NULL,

CONSTRAINT PK_Client PRIMARY KEY (ID_Client)

)

CREATE TABLE Act( ID_Act [INT] NOT NULL,

 Data_act [datetime] NOT NULL,

 status_act [bit] NOT NULL,

 ID_tovara [INT] NOT NULL REFERENCES Tovar (ID_tovara),

 ID_client[INT] NOT NULL REFERENCES Client (ID_client),

 ID_manager [INT] NOT NULL REFERENCES Manager (ID_manager),

 Rate_Act [tinyint] NOT NULL,

CONSTRAINT PK_Act PRIMARY KEY (ID_Act)

)

GO

CREATE TRIGGER DATA

ON Act

FOR INSERT

AS 

BEGIN

SET nocount ON

IF 

((SELECT Data_act FROM inserted) < (getdate()))

BEGIN

ROLLBACK

print &#039;Дата заказа не должна быть меньше даты отправки этой заявки&#039;

END

END

GO

CREATE TRIGGER data_1

ON Manager

FOR INSERT

AS 

BEGIN

SET nocount ON

IF 

((SELECT Data_manager FROM inserted) < (getdate()))

BEGIN

ROLLBACK

print &#039;Введите корректную дату заявления на работу&#039;

END

END

GO

CREATE TRIGGER snyat

ON dbo.tovar

FOR DELETE

AS

BEGIN

DELETE FROM Act WHERE Act.ID_tovara = (SELECT ID_Tovara FROM deleted) AND Act.Status_act = 0
print &#039;Удалены все невыполненные заказы, связанные этим товаром&#039;

END

GO

CREATE TRIGGER sales

ON act

FOR INSERT

AS

BEGIN

DECLARE @s INT

DECLARE @a DECIMAL (10,2)

SET @s= (SELECT Tovar.Price_tovar FROM tovar,inserted WHERE inserted.ID_tovara=Tovar.ID_Tovara)

UPDATE Client

SET Summa_Client = Summa_client + @s

FROM client, inserted AS i 

 WHERE i.ID_Client = Client.ID_Client 

SELECT @a = Client.Summa_Client

FROM client, inserted AS i 

 WHERE i.ID_Client = Client.ID_Client 

--Прописать скидки

DECLARE @h tinyint

SET @h= CASE 

WHEN (@a>10000) THEN 15

WHEN (@a>5000) THEN 10

WHEN (@a>2000) THEN 7

WHEN (@a>1000) THEN 5

ELSE 0

END

UPDATE Client

SET Sale_Client = @h

FROM client, inserted AS i 

 WHERE i.ID_Client = Client.ID_Client 

END

GO

INSERT INTO Category VALUES (1,&#039;Светильники&#039;);

INSERT INTO Category VALUES (2,&#039;Для_компьютеров&#039;);

INSERT INTO Category VALUES (3,&#039;Приколы&#039;);

INSERT INTO Category VALUES (4,&#039;Романтика&#039;);

INSERT INTO Category VALUES (5,&#039;Другое&#039;);

INSERT INTO Tovar VALUES (1,&#039;Солнце&#47;луна&#039;,810,1);

INSERT INTO Tovar VALUES (2,&#039;Череп-плазма&#039;,900,1);

INSERT INTO Tovar VALUES (3,&#039;Плазма&#039;,900,1);

INSERT INTO Tovar VALUES (4,&#039;Водопад&#039;,990,1);

INSERT INTO Tovar VALUES (5,&#039;Неоновая роза&#039;,200,1);

INSERT INTO Tovar VALUES (6,&#039;Пуля&#039;,900,1);

INSERT INTO Tovar VALUES (7,&#039;USB-пепельница-мышка&#039;,900,2);

INSERT INTO Tovar VALUES (8,&#039;Мышь жидкая красная&#039;,820,2);

INSERT INTO Tovar VALUES (9,&#039;USB HUB - электронные пробел часы&#039;,750,2);

INSERT INTO Tovar VALUES (10,&#039;USB мини пылесос&#039;,450,2);

INSERT INTO Tovar VALUES (11,&#039;USB HUB-трансфортмер&#039;,450,2);

INSERT INTO Tovar VALUES (12,&#039;Бегающий будильник&#039;,1220,3);

INSERT INTO Tovar VALUES (13,&#039;Молоток шефа&#039;,560,3);

INSERT INTO Tovar VALUES (14,&#039;Кот в мешке&#039;,450,3);

INSERT INTO Tovar VALUES (15,&#039;Борзометр&#039;,690,3);

INSERT INTO Tovar VALUES (16,&#039;Самогонный аппарат&#039;,2600,3);

INSERT INTO Tovar VALUES (17,&#039;Сердечко-говорунчик&#039;,560,4);

INSERT INTO Tovar VALUES (18,&#039;Набор наволочек "он и она"&#039;,1080,4);

INSERT INTO Tovar VALUES (19,&#039;Ключи любви&#039;,360,4);

INSERT INTO Tovar VALUES (20,&#039;Сердечко в банке&#039;,300,4);

INSERT INTO Tovar VALUES (21,&#039;Флешка-сердечко&#039;,1250,4);

INSERT INTO Tovar VALUES (22,&#039;Пивная каска "футбол"&#039;,790,5);

INSERT INTO Tovar VALUES (23,&#039;Ультразвуковой увлажнитель воздуха&#039;,5650,5);

INSERT INTO Tovar VALUES (24,&#039;Книга-шкатулка&#039;,1500,5);

INSERT INTO Tovar VALUES (25,&#039;Тарелка на двоих&#039;,740,5);

INSERT INTO Tovar VALUES (26,&#039;Кружка "похмельный глюк"&#039;,380,5);

INSERT INTO Manager VALUES (1,&#039;сотрудник1&#039;,&#039;адрес1&#039;,&#039;2011&#47;9&#47;1&#039;,1);

INSERT INTO Manager VALUES (2,&#039;сотрудник2&#039;,&#039;адрес2&#039;,&#039;2011&#47;9&#47;1&#039;,1);

INSERT INTO Manager VALUES (3,&#039;сотрудник3&#039;,&#039;адрес3&#039;,&#039;2011&#47;9&#47;2&#039;,1);

INSERT INTO Manager VALUES (4,&#039;сотрудник4&#039;,&#039;адрес4&#039;,&#039;2011&#47;9&#47;3&#039;,1);

INSERT INTO Manager VALUES (5,&#039;сотрудник5&#039;,&#039;адрес5&#039;,&#039;2011&#47;9&#47;4&#039;,1);

INSERT INTO Manager VALUES (6,&#039;сотрудник6&#039;,&#039;адрес6&#039;,&#039;2011&#47;9&#47;5&#039;,2);

INSERT INTO Manager VALUES (7,&#039;сотрудник7&#039;,&#039;адрес7&#039;,&#039;2011&#47;9&#47;1&#039;,2);

INSERT INTO Manager VALUES (8,&#039;сотрудник8&#039;,&#039;адрес8&#039;,&#039;2011&#47;9&#47;2&#039;,2);

INSERT INTO Manager VALUES (9,&#039;сотрудник9&#039;,&#039;адрес9&#039;,&#039;2011&#47;9&#47;3&#039;,2);

INSERT INTO Manager VALUES (10,&#039;сотрудник10&#039;,&#039;адрес10&#039;,&#039;2011&#47;9&#47;4&#039;,2);

INSERT INTO Manager VALUES (11,&#039;сотрудник11&#039;,&#039;адрес11&#039;,&#039;2011&#47;9&#47;5&#039;,3);

INSERT INTO Manager VALUES (12,&#039;сотрудник12&#039;,&#039;адрес12&#039;,&#039;2011&#47;9&#47;1&#039;,3);

INSERT INTO Manager VALUES (13,&#039;сотрудник13&#039;,&#039;адрес13&#039;,&#039;2011&#47;9&#47;2&#039;,3);

INSERT INTO Manager VALUES (14,&#039;сотрудник14&#039;,&#039;адрес14&#039;,&#039;2011&#47;9&#47;3&#039;,3);

INSERT INTO Manager VALUES (15,&#039;сотрудник15&#039;,&#039;адрес15&#039;,&#039;2011&#47;9&#47;4&#039;,3);

INSERT INTO Manager VALUES (16,&#039;сотрудник16&#039;,&#039;адрес16&#039;,&#039;2011&#47;9&#47;5&#039;,4);

INSERT INTO Manager VALUES (17,&#039;сотрудник17&#039;,&#039;адрес17&#039;,&#039;2011&#47;9&#47;1&#039;,4);

INSERT INTO Manager VALUES (18,&#039;сотрудник18&#039;,&#039;адрес18&#039;,&#039;2011&#47;9&#47;2&#039;,4);

INSERT INTO Manager VALUES (19,&#039;сотрудник19&#039;,&#039;адрес19&#039;,&#039;2011&#47;9&#47;3&#039;,4);

INSERT INTO Manager VALUES (20,&#039;сотрудник20&#039;,&#039;адрес20&#039;,&#039;2011&#47;9&#47;4&#039;,4);

INSERT INTO Manager VALUES (21,&#039;сотрудник21&#039;,&#039;адрес21&#039;,&#039;2011&#47;9&#47;5&#039;,5);

INSERT INTO Manager VALUES (22,&#039;сотрудник22&#039;,&#039;адрес22&#039;,&#039;2011&#47;9&#47;1&#039;,5);

INSERT INTO Manager VALUES (23,&#039;сотрудник23&#039;,&#039;адрес23&#039;,&#039;2011&#47;9&#47;2&#039;,5);

INSERT INTO Manager VALUES (24,&#039;сотрудник24&#039;,&#039;адрес24&#039;,&#039;2011&#47;9&#47;3&#039;,5);

INSERT INTO Manager VALUES (25,&#039;сотрудник25&#039;,&#039;адрес25&#039;,&#039;2011&#47;9&#47;4&#039;,5);

INSERT INTO Client VALUES (1,&#039;клиент1&#039;,&#039;адрес1&#039;,0,0);

INSERT INTO Client VALUES (2,&#039;клиент2&#039;,&#039;адрес2&#039;,0,0);

INSERT INTO Client VALUES (3,&#039;клиент3&#039;,&#039;адрес3&#039;,0,0);

INSERT INTO Client VALUES (4,&#039;клиент4&#039;,&#039;адрес4&#039;,0,0);

INSERT INTO Client VALUES (5,&#039;клиент5&#039;,&#039;адрес5&#039;,0,0);

INSERT INTO Client VALUES (6,&#039;клиент6&#039;,&#039;адрес6&#039;,0,0);

INSERT INTO Client VALUES (7,&#039;клиент7&#039;,&#039;адрес7&#039;,0,0);

INSERT INTO Client VALUES (8,&#039;клиент8&#039;,&#039;адрес8&#039;,0,0);

INSERT INTO Client VALUES (9,&#039;клиент9&#039;,&#039;адрес9&#039;,0,0);

INSERT INTO Client VALUES (10,&#039;клиент10&#039;,&#039;адрес10&#039;,0,0);

INSERT INTO Client VALUES (11,&#039;клиент11&#039;,&#039;адрес11&#039;,0,0);

INSERT INTO Client VALUES (12,&#039;клиент12&#039;,&#039;адрес12&#039;,0,0);

INSERT INTO Client VALUES (13,&#039;клиент13&#039;,&#039;адрес13&#039;,0,0);

INSERT INTO Client VALUES (14,&#039;клиент14&#039;,&#039;адрес14&#039;,0,0);

INSERT INTO Client VALUES (15,&#039;клиент15&#039;,&#039;адрес15&#039;,0,0);

INSERT INTO Act VALUES (1,&#039;2011&#47;09&#47;1&#039;,0,1,1,1, 5);

INSERT INTO Act VALUES (2,&#039;2011&#47;09&#47;2&#039;,1,2,2,3, 5);

INSERT INTO Act VALUES (3,&#039;2011&#47;09&#47;3&#039;,0,3,3,5, 5);

INSERT INTO Act VALUES (4,&#039;2011&#47;09&#47;4&#039;,1,4,4,7, 5);

INSERT INTO Act VALUES (5,&#039;2011&#47;09&#47;5&#039;,0,5,5,9, 5);

INSERT INTO Act VALUES (6,&#039;2011&#47;09&#47;6&#039;,1,6,6,3, 5);

INSERT INTO Act VALUES (7,&#039;2011&#47;09&#47;7&#039;,0,7,7,15, 5);

INSERT INTO Act VALUES (8,&#039;2011&#47;09&#47;8&#039;,1,8,8,7, 5);

INSERT INTO Act VALUES (9,&#039;2011&#47;09&#47;9&#039;,0,9,9,9, 5);

INSERT INTO Act VALUES (10,&#039;2011&#47;09&#47;10&#039;,0,10,10,2, 5);

INSERT INTO Act VALUES (11,&#039;2011&#47;09&#47;11&#039;,0,11,11,16, 5);

INSERT INTO Act VALUES (12,&#039;2011&#47;09&#47;12&#039;,1,12,12,14, 5);

INSERT INTO Act VALUES (13,&#039;2011&#47;09&#47;13&#039;,0,13,13,22, 5);

INSERT INTO Act VALUES (14,&#039;2011&#47;09&#47;14&#039;,1,14,14,25, 5);

INSERT INTO Act VALUES (15,&#039;2011&#47;09&#47;15&#039;,0,15,15,20, 5);

INSERT INTO Act VALUES (16,&#039;2012&#47;09&#47;15&#039;,0,15,15,20, 5);

INSERT INTO Act VALUES (17,&#039;2012&#47;09&#47;15&#039;,0,15,15,20, 5);

INSERT INTO Act VALUES (18,&#039;2012&#47;09&#47;15&#039;,0,15,15,20, 5);

INSERT INTO Act VALUES (19,&#039;2012&#47;09&#47;15&#039;,0,15,15,20, 5);

INSERT INTO Act VALUES (20,&#039;2012&#47;09&#47;15&#039;,0,15,15,20, 5);<&#47;pre>
