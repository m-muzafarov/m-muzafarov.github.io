---
layout: post
status: publish
published: true
title: База подарков
author: M_Messiah
author_login: Messiah
author_email: m.muzafarov@gmail.com
author_url: http://brainstorage.me/M_Messiah
excerpt: "Создание и заполнение базы SQL для индивидуального ДЗ."
date: 2012-06-26 23:55:38.000000000 +06:00
categories: SQL
tags: []
comments: []
---
Создание и заполнение базы
{% highlight sql %}USE [master]
IF EXISTS (SELECT name FROM sys.DATABASES WHERE name = N'presents')
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
		print 'Дата заказа не должна быть меньше даты отправки этой заявки'
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
		print 'Введите корректную дату заявления на работу'
	END
END
GO
CREATE TRIGGER snyat
ON dbo.tovar
FOR DELETE
AS
BEGIN
	DELETE FROM Act WHERE Act.ID_tovara = (SELECT ID_Tovara FROM deleted) AND Act.Status_act = 0
	print 'Удалены все невыполненные заказы, связанные этим товаром'
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
	UPDATE Client SET Sale_Client = @h FROM client, inserted AS i WHERE i.ID_Client = Client.ID_Client 
END
GO
INSERT INTO Category VALUES (1,'Светильники');
INSERT INTO Category VALUES (2,'Для_компьютеров');
INSERT INTO Category VALUES (3,'Приколы');
INSERT INTO Category VALUES (4,'Романтика');
INSERT INTO Category VALUES (5,'Другое');
INSERT INTO Tovar VALUES (1,'Солнце/луна',810,1);
INSERT INTO Tovar VALUES (2,'Череп-плазма',900,1);
INSERT INTO Tovar VALUES (3,'Плазма',900,1);
INSERT INTO Tovar VALUES (4,'Водопад',990,1);
INSERT INTO Tovar VALUES (5,'Неоновая роза',200,1);
INSERT INTO Tovar VALUES (6,'Пуля',900,1);
INSERT INTO Tovar VALUES (7,'USB-пепельница-мышка',900,2);
INSERT INTO Tovar VALUES (8,'Мышь жидкая красная',820,2);
INSERT INTO Tovar VALUES (9,'USB HUB - электронные пробел часы',750,2);
INSERT INTO Tovar VALUES (10,'USB мини пылесос',450,2);
INSERT INTO Tovar VALUES (11,'USB HUB-трансфортмер',450,2);
INSERT INTO Tovar VALUES (12,'Бегающий будильник',1220,3);
INSERT INTO Tovar VALUES (13,'Молоток шефа',560,3);
INSERT INTO Tovar VALUES (14,'Кот в мешке',450,3);
INSERT INTO Tovar VALUES (15,'Борзометр',690,3);
INSERT INTO Tovar VALUES (16,'Самогонный аппарат',2600,3);
INSERT INTO Tovar VALUES (17,'Сердечко-говорунчик',560,4);
INSERT INTO Tovar VALUES (18,'Набор наволочек "он и она"',1080,4);
INSERT INTO Tovar VALUES (19,'Ключи любви',360,4);
INSERT INTO Tovar VALUES (20,'Сердечко в банке',300,4);
INSERT INTO Tovar VALUES (21,'Флешка-сердечко',1250,4);
INSERT INTO Tovar VALUES (22,'Пивная каска "футбол"',790,5);
INSERT INTO Tovar VALUES (23,'Ультразвуковой увлажнитель воздуха',5650,5);
INSERT INTO Tovar VALUES (24,'Книга-шкатулка',1500,5);
INSERT INTO Tovar VALUES (25,'Тарелка на двоих',740,5);
INSERT INTO Tovar VALUES (26,'Кружка "похмельный глюк"',380,5);
INSERT INTO Manager VALUES (1,'сотрудник1','адрес1','2011/9/1',1);
INSERT INTO Manager VALUES (2,'сотрудник2','адрес2','2011/9/1',1);
INSERT INTO Manager VALUES (3,'сотрудник3','адрес3','2011/9/2',1);
INSERT INTO Manager VALUES (4,'сотрудник4','адрес4','2011/9/3',1);
INSERT INTO Manager VALUES (5,'сотрудник5','адрес5','2011/9/4',1);
INSERT INTO Manager VALUES (6,'сотрудник6','адрес6','2011/9/5',2);
INSERT INTO Manager VALUES (7,'сотрудник7','адрес7','2011/9/1',2);
INSERT INTO Manager VALUES (8,'сотрудник8','адрес8','2011/9/2',2);
INSERT INTO Manager VALUES (9,'сотрудник9','адрес9','2011/9/3',2);
INSERT INTO Manager VALUES (10,'сотрудник10','адрес10','2011/9/4',2);
INSERT INTO Manager VALUES (11,'сотрудник11','адрес11','2011/9/5',3);
INSERT INTO Manager VALUES (12,'сотрудник12','адрес12','2011/9/1',3);
INSERT INTO Manager VALUES (13,'сотрудник13','адрес13','2011/9/2',3);
INSERT INTO Manager VALUES (14,'сотрудник14','адрес14','2011/9/3',3);
INSERT INTO Manager VALUES (15,'сотрудник15','адрес15','2011/9/4',3);
INSERT INTO Manager VALUES (16,'сотрудник16','адрес16','2011/9/5',4);
INSERT INTO Manager VALUES (17,'сотрудник17','адрес17','2011/9/1',4);
INSERT INTO Manager VALUES (18,'сотрудник18','адрес18','2011/9/2',4);
INSERT INTO Manager VALUES (19,'сотрудник19','адрес19','2011/9/3',4);
INSERT INTO Manager VALUES (20,'сотрудник20','адрес20','2011/9/4',4);
INSERT INTO Manager VALUES (21,'сотрудник21','адрес21','2011/9/5',5);
INSERT INTO Manager VALUES (22,'сотрудник22','адрес22','2011/9/1',5);
INSERT INTO Manager VALUES (23,'сотрудник23','адрес23','2011/9/2',5);
INSERT INTO Manager VALUES (24,'сотрудник24','адрес24','2011/9/3',5);
INSERT INTO Manager VALUES (25,'сотрудник25','адрес25','2011/9/4',5);
INSERT INTO Client VALUES (1,'клиент1','адрес1',0,0);
INSERT INTO Client VALUES (2,'клиент2','адрес2',0,0);
INSERT INTO Client VALUES (3,'клиент3','адрес3',0,0);
INSERT INTO Client VALUES (4,'клиент4','адрес4',0,0);
INSERT INTO Client VALUES (5,'клиент5','адрес5',0,0);
INSERT INTO Client VALUES (6,'клиент6','адрес6',0,0);
INSERT INTO Client VALUES (7,'клиент7','адрес7',0,0);
INSERT INTO Client VALUES (8,'клиент8','адрес8',0,0);
INSERT INTO Client VALUES (9,'клиент9','адрес9',0,0);
INSERT INTO Client VALUES (10,'клиент10','адрес10',0,0);
INSERT INTO Client VALUES (11,'клиент11','адрес11',0,0);
INSERT INTO Client VALUES (12,'клиент12','адрес12',0,0);
INSERT INTO Client VALUES (13,'клиент13','адрес13',0,0);
INSERT INTO Client VALUES (14,'клиент14','адрес14',0,0);
INSERT INTO Client VALUES (15,'клиент15','адрес15',0,0);
INSERT INTO Act VALUES (1,'2011/09/1',0,1,1,1, 5);
INSERT INTO Act VALUES (2,'2011/09/2',1,2,2,3, 5);
INSERT INTO Act VALUES (3,'2011/09/3',0,3,3,5, 5);
INSERT INTO Act VALUES (4,'2011/09/4',1,4,4,7, 5);
INSERT INTO Act VALUES (5,'2011/09/5',0,5,5,9, 5);
INSERT INTO Act VALUES (6,'2011/09/6',1,6,6,3, 5);
INSERT INTO Act VALUES (7,'2011/09/7',0,7,7,15, 5);
INSERT INTO Act VALUES (8,'2011/09/8',1,8,8,7, 5);
INSERT INTO Act VALUES (9,'2011/09/9',0,9,9,9, 5);
INSERT INTO Act VALUES (10,'2011/09/10',0,10,10,2, 5);
INSERT INTO Act VALUES (11,'2011/09/11',0,11,11,16, 5);
INSERT INTO Act VALUES (12,'2011/09/12',1,12,12,14, 5);
INSERT INTO Act VALUES (13,'2011/09/13',0,13,13,22, 5);
INSERT INTO Act VALUES (14,'2011/09/14',1,14,14,25, 5);
INSERT INTO Act VALUES (15,'2011/09/15',0,15,15,20, 5);
INSERT INTO Act VALUES (16,'2012/09/15',0,15,15,20, 5);
INSERT INTO Act VALUES (17,'2012/09/15',0,15,15,20, 5);
INSERT INTO Act VALUES (18,'2012/09/15',0,15,15,20, 5);
INSERT INTO Act VALUES (19,'2012/09/15',0,15,15,20, 5);
INSERT INTO Act VALUES (20,'2012/09/15',0,15,15,20, 5);{% endhighlight %}
