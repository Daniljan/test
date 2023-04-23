-- idfix --
DO $$DECLARE
  r RECORD;
BEGIN
  FOR r IN (SELECT sequence_name FROM information_schema.sequences WHERE sequence_schema = 'public') LOOP
    EXECUTE 'ALTER SEQUENCE ' || r.sequence_name || ' RESTART WITH 1';
  END LOOP;
END$$;

-- CREATE TABLES --
create table color (
	id_color serial primary key,
	name varchar(255) 
);
COPY Color (name) from 'D:\daniljan\color.csv' DELIMITER ';' CSV ENCODING 'WIN1251' QUOTE '\';   

create table picture (
	id_picture serial primary key,
	type_picture varchar(255) 
);
COPY Picture (type_picture) from 'D:\daniljan\picture.csv' DELIMITER ';' CSV ENCODING 'WIN1251' QUOTE '\';   

create table typeF (
	id_TypeF serial primary key,
	name varchar(255) 
);
COPY typeF (name) from 'D:\daniljan\typeF.csv' DELIMITER ';' CSV ENCODING 'WIN1251' QUOTE '\';   

create table units (
	id_units serial primary key,
	list_u varchar(255) 
);
COPY units (list_u) from 'D:\daniljan\units.csv' DELIMITER ';' CSV ENCODING 'WIN1251' QUOTE '\'; 

create table textile(
	id_textile serial primary key,
	article varchar(255),
	name varchar(255),
	id_color integer,
	id_picture integer,
    compound varchar(255),
	length integer,
	width integer,
	square integer,
	id_units integer,
	price money,
	FOREIGN KEY (id_color) REFERENCES color(id_color),
	FOREIGN KEY (id_picture) REFERENCES picture(id_picture),
	FOREIGN KEY (id_units) REFERENCES units(id_units)
);
COPY textile (article, name, id_color, id_picture, compound, length, width, square, id_units, price) from 'D:\daniljan\textile.csv' DELIMITER ';' CSV ENCODING 'WIN1251' QUOTE '\';

create table furniture (
	id_furniture serial primary key,
	article varchar(255),
	name varchar(255),
	length integer,
	width integer,
	id_units integer,
	id_typeF integer,
	price money,
	weight integer,
	FOREIGN KEY (id_typeF) REFERENCES typeF(id_typeF),
	FOREIGN KEY (id_units) REFERENCES units(id_units)
);
COPY furniture (article, name, length, width,id_units, id_typeF, price, weight) from 'D:\daniljan\furniture.csv' DELIMITER ';' CSV ENCODING 'WIN1251' QUOTE '\';  

create table product (
	id_product serial primary key,
	article varchar(255),
	name varchar(255),
	length integer,
	width integer,
	square integer,
	id_units integer,
	price money,
	FOREIGN KEY (id_units) REFERENCES units(id_units)
);
COPY product (article, name, length, width, square, id_units, price) from 'D:\daniljan\product.csv' DELIMITER ';' CSV ENCODING 'WIN1251' QUOTE '\';   

CREATE TABLE provider (
id_provider SERIAL PRIMARY KEY,
name VARCHAR(100) NOT NULL,
phone VARCHAR(20) NOT NULL
);

CREATE TABLE storage (
id_storage SERIAL PRIMARY KEY,
address VARCHAR(100) NOT NULL,
phone VARCHAR(20) NOT NULL,
name VARCHAR(100) NOT NULL
);

CREATE TABLE supply_of_furniture (
id_supply_of_furniture SERIAL PRIMARY KEY,
count INTEGER NOT NULL,
weight REAL NOT NULL,
price REAL NOT NULL,
id_provider INTEGER NOT NULL,
FOREIGN KEY (id_provider) REFERENCES provider(id_provider)
);

CREATE TABLE textile_product (
  id_textile_product SERIAL PRIMARY KEY,
  id_textile INTEGER NOT NULL,
  id_product INTEGER NOT NULL,
  square INTEGER NOT NULL,
  width INTEGER NULL,
  length INTEGER NULL,
  count INTEGER NOT NULL,
  FOREIGN KEY (id_textile) REFERENCES textile(id_textile),
  FOREIGN KEY (id_product) REFERENCES product(id_product)
);


CREATE TABLE supply_of_textile (
  id_supply_of_textile SERIAL PRIMARY KEY,
  price money NOT NULL,
  id_provider INTEGER NOT NULL,
  count INTEGER NOT NULL,	
  FOREIGN KEY (id_provider) REFERENCES provider(id_provider)
);

CREATE TABLE textile_storage (
  id_part SERIAL PRIMARY KEY,
  name VARCHAR(255) NOT NULL,
  roll INTEGER NOT NULL,
  width INTEGER NOT NULL,
  length INTEGER NOT NULL,
  square INTEGER NOT NULL,
  id_textile INTEGER NOT NULL,
  id_supply_of_textile INTEGER NOT NULL,
  id_storage INTEGER NOT NULL,
  FOREIGN KEY (id_textile) REFERENCES textile(id_textile),
  FOREIGN KEY (id_supply_of_textile) REFERENCES  supply_of_textile(id_supply_of_textile),
  FOREIGN KEY (id_storage) REFERENCES storage(id_storage)
);

CREATE TABLE roles (
id_roles SERIAL PRIMARY KEY,
name VARCHAR(50) NOT NULL
);

CREATE TABLE order_status (
id_order_status SERIAL PRIMARY KEY,
name VARCHAR(100) NOT NULL
);

CREATE TABLE users(
id_users SERIAL PRIMARY KEY,
nickname VARCHAR(100) NOT NULL,
phone VARCHAR(20) NOT NULL,
login VARCHAR(50) NOT NULL,
password VARCHAR(50) NOT NULL,
id_roles INTEGER NOT NULL,
FOREIGN KEY (id_roles) REFERENCES roles (id_roles)
);

CREATE TABLE orders (
id_orders SERIAL PRIMARY KEY,
date DATE NOT NULL,
id_users INTEGER NOT NULL,
id_order_status INTEGER NOT NULL,
FOREIGN KEY (id_users) REFERENCES users (id_users),
FOREIGN KEY (id_order_status) REFERENCES order_status (id_order_status)
);

CREATE TABLE ordered_products (
id_ordered_products SERIAL PRIMARY KEY,
id_product INTEGER NOT NULL,
id_orders INTEGER NOT NULL,
count INTEGER NOT NULL,
FOREIGN KEY (id_product) REFERENCES product (id_product),
FOREIGN KEY (id_orders) REFERENCES orders (id_orders)
);

CREATE TABLE furniture_products (
id_furniture_products SERIAL PRIMARY KEY,
id_furniture INTEGER NOT NULL,
id_products INTEGER NOT NULL,
length REAL NULL,
width REAL NULL,
count INTEGER NOT NULL,
FOREIGN KEY (id_products) REFERENCES product(id_product),
FOREIGN KEY (id_furniture) REFERENCES furniture(id_furniture)
);

CREATE TABLE furniture_storage (
id_part SERIAL PRIMARY KEY,
name VARCHAR(100) NOT NULL,
count INTEGER NULL,
id_units INTEGER NOT NULL,
id_storage INTEGER NOT NULL,
id_furniture INTEGER NOT NULL,
id_supply_of_furniture INTEGER NOT NULL,
FOREIGN KEY (id_units) REFERENCES units(id_units),
FOREIGN KEY (id_storage) REFERENCES storage(id_storage),
FOREIGN KEY (id_furniture) REFERENCES furniture(id_furniture),
FOREIGN KEY (id_supply_of_furniture) REFERENCES supply_of_furniture(id_supply_of_furniture)
);

-- FILLING IN THE TABLES--
insert into provider(name,phone)
values
('ЭдикРукиНожницы',79870043055),--eto Dimyn--
('БистраДоставимФурни',79372827074) , --eto moy ))--
('ПуджакДжо',79872243055) ,
('ЛысыйГлобус',79873343055) ,
('РоссийскиеИзделия',79874443055) ,
('ОйойойоКакиеМатериалы',79875543055) ,
('ДададададаЭтоМы',79876643055) ,
('Ну мы это и что',79877743055) ,
('Мы лучшие',79878843055) ,
('Мы не лучшие, но Вам понравимся',79879943055);

insert into supply_of_furniture(count,weight,price,id_provider)
values
(10,100,200,1),
(15,20,1500,5),
(1,150,1000,4),
(5,1000,30,3),
(200,30,20,6),
(15,50,10,7),
(30,5,15,9),
(50,35,50,8),
(25,60,60,9),
(35,65,20700,8);

insert into supply_of_textile(price,id_provider,count)
values
(1000,1,300),
(1550,2,20),
(2000,2,15),
(3000,2,5),
(5000,3,15),
(25000,5,80),
(3000,6,90),
(2000,8,50),
(1000,9,90),
(11000,10,150);

insert into storage(address,phone,name)
values
('Stroiteley 25',79372827074,'S25'),
('Stroiteley 26',79371237074,'S26'),
('Mira 25',79373217074,'M25'),
('Mira 5',79371117074,'M5'),
('Lenina 5',79371447074,'L5'),
('Sovetskaya 69',79374447074,'S69'),
('Ganieva 1',79375557074,'G1'),
('Gorodskaya 5',79374357074,'G5'),
('Mira 89',79378097074,'M89'),
('Kira 100',79379997074,'K100');

insert into roles(name)
values 
('admin'),
('user');

insert into order_status(name)
values 
('ready'),
('delivered'),
('assembling');

insert into users (nickname,phone,login,password,id_roles)
values 
('kirill2009',79373328980,'adminchik2004','sladkaya_popka36',1),
('danil2005',79377728980,'adminchik2005','gorkaya_popka36',2),
('dimka2002',79375228980,'papochka','sosika2006',2),
('arturchik2006',79372221180,'arturchik','123321QWEasdrrr','1'),
('vladik_padik',79372228980,'vladik_superrrr','159754862789456123','2'),
('nikita_siel_kita',79372888980,'nikitosik','yakrutkit','1'),
('ainur_kachock',79372289980,'na_masse2001','10012003','2'),
('zxcursed',79372226680,'shadow_pid','yapravdauser','1'),
('naruto_fagggg',79371128980,'sharingan_itachi_dadada','nasamomdeleyaadmin','1'),
('Luffy',79372228912,'login666','serverpominecraft','2');

insert into orders(date,id_users,id_order_status)
values 
('17.03.2023',2,3),
('16.03.2023',1,1),
('12.03.2023',3,1),
('1.03.2023',10,1),
('10.03.2023',9,2),
('17.02.2023',8,3),
('17.01.2023',2,2),
('16.02.2023',8,3),
('20.02.2023',7,1),
('27.03.2023',2,3);

insert into ordered_products(id_product,id_orders,count)
values
(1,1,30),
(2,2,50),
(3,3,60),
(5,4,20),
(1,5,10),
(2,6,2),
(2,7,3),
(3,8,5),
(4,9,50),
(5,10,10);

insert into furniture_products(id_furniture,id_products,length,width,count)
values
(1,1,100,50,2),
(2,2,20,50,1),
(3,3,70,50,5),
(4,4,100,40,4),
(5,5,100,10,3),
(6,6,100,90,6),
(7,7,600,230,7),
(8,8,500,50,9),
(9,9,800,230,12),
(10,10,700,550,7);

insert into furniture_storage(name,count,id_units,id_storage,id_furniture,id_supply_of_furniture)
values
('Пуговица',50,1,1,1,1),
('Булавка',100,1,2,2,2),
('Наклейка',30,3,3,3,3),
('Пуговица-Б3',1,4,4,4,4),
('Наклейка-Э23',3,2,5,5,5),
('Булавка-М25',2,4,6,6,6),
('Пуговица-АМ21',1,3,7,7,7),
('Наклейка-Л25',3,4,8,8,8),
('Булавка-МА21',2,5,9,9,9),
('Пуговица-Б21',1,6,10,10,10);



insert into textile_product(id_textile,id_product,square,width,length,count)
values
(1,1,10,50,100,3),
(2,2,30,110,150,10),
(3,3,20,100,170,5),
(4,4,50,120,200,21),
(5,5,70,150,300,12),
(6,6,8,6,10,4),
(7,7,16,80,100,14),
(8,8,14,40,70,7),
(9,9,12,80,130,16),
(10,10,20,70,110,15);

insert into textile_storage(name,roll,width,length,square,id_textile,id_supply_of_textile,id_storage)
values
('К-3',3,20,60,30,1,1,1),
('C-5',5,30,40,17,2,2,2),
('О-8',8,10,15,20,3,3,3),
('З-10',4,10,180,70,4,4,4),
('К-5',5,10,5,56,5,5,5),
('Ч-20',5,23,16,44,6,6,6),
('К-7',7,30,17,36,7,7,7),
('Ф-4',4,26,20,68,8,8,8),
('З-5',1,18,13,72,9,9,9),
('М-9',9,16,16,39,10,10,10);

-- TRIGGERS --
-- consumption furniture
CREATE FUNCTION furniture_consumption()
RETURNS TRIGGER AS $$
BEGIN
	UPDATE furniture_storage
	SET count = count - new.count
	WHERE id_furniture = new.id_furniture;
	RETURN NEW;
END;
$$ LANGUAGE plpgsql;
CREATE TRIGGER furniture_consumption
AFTER INSERT ON furniture_products
FOR EACH ROW
EXECUTE FUNCTION furniture_consumption();

values
('Пуговица',40,1,1,1,1);
select * from furniture_storage


--profit furniture
CREATE FUNCTION furniture_profit()
RETURNS TRIGGER AS $$
BEGIN
	UPDATE furniture_storage
	SET count = count + new.count
	WHERE id_furniture = new.id_furniture;
	RETURN NEW;
END;
$$ LANGUAGE plpgsql;
CREATE TRIGGER furniture_profit
AFTER INSERT ON furniture_products
FOR EACH ROW
EXECUTE FUNCTION furniture_profit();

-- balance furniture
CREATE FUNCTION furniture_balance()
RETURNS TRIGGER AS $$
BEGIN
	UPDATE furniture_storage
	SET count = count - (furniture_consumption + furniture_plus)
	WHERE id_furniture = new.id_furniture;
	RETURN NEW;
END;
$$ LANGUAGE plpgsql;
CREATE TRIGGER furniture_balance
AFTER INSERT ON furniture_products
FOR EACH ROW
EXECUTE FUNCTION furniture_balance();
insert into furniture_storage(name,id_storage,id_supply_of_furniture,id_units,id_furniture,count) values
('Hожка',1,1,1,1,10);

-- consumption textile
CREATE FUNCTION textile_consumption()
RETURNS TRIGGER AS $$
BEGIN
	UPDATE textile_storage
	SET roll = roll - new.roll
	WHERE id_textile = new.id_textile;
	RETURN NEW;
END;
$$ LANGUAGE plpgsql;
CREATE TRIGGER textile_consumption
AFTER INSERT ON textile_product
FOR EACH ROW
EXECUTE FUNCTION textile_consumption();


--profit textile
CREATE FUNCTION textile_profit()
RETURNS TRIGGER AS $$
BEGIN
	UPDATE textile_storage
	SET roll = roll + new.roll
	WHERE id_textile = new.id_textile;
	RETURN NEW;
END;
$$ LANGUAGE plpgsql;
CREATE TRIGGER textile_profit
AFTER INSERT ON textile_product
FOR EACH ROW
EXECUTE FUNCTION textile_profit();

--  balance textile
CREATE FUNCTION textile_balance()
RETURNS TRIGGER AS $$
BEGIN
	UPDATE textile_storage
	SET roll = textile_profit-textile_consumption
	WHERE id_textile = new.id_textile;
	RETURN NEW;
END;
$$ LANGUAGE plpgsql;
CREATE TRIGGER textile_balance
AFTER INSERT ON textile_product
FOR EACH ROW
EXECUTE FUNCTION textile_balance();
--  status)
CREATE FUNCTION status()
RETURNS TRIGGER AS $$
BEGIN
	UPDATE orders
	SET id_order_status = 1
	WHERE date = new.date;
	RETURN NEW;
END;
$$ LANGUAGE plpgsql;
CREATE TRIGGER status
AFTER INSERT ON orders
FOR EACH ROW
EXECUTE FUNCTION status();

-- OTHER -- 
ALTER TABLE product ADD COLUMN size text;

UPDATE product SET size = 'Very small'
where square between 1 and 1000;

UPDATE product SET size = 'Small'
where square between 1000 and 5000;

UPDATE product SET size = 'Medium'
where square between 5001 and 15000;


UPDATE product SET size = 'Tinat size'
where square between 15001 and 231231231;



Update textile_storage
set square = width*length;
SELECT name, SUM(roll*square) AS кв_метры
FROM textile_storage
where (roll*square) > 100
GROUP BY name;A





