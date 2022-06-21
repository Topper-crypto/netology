# Домашнее задание к занятию "6.4. PostgreSQL"
### Задача 1
> Используя docker поднимите инстанс PostgreSQL (версию 13). Данные БД сохраните в volume.
> 
> Подключитесь к БД PostgreSQL используя ```psql```.
> 
> Воспользуйтесь командой ```\?``` для вывода подсказки по имеющимся в ```psql``` управляющим командам.
> 
> Найдите и приведите управляющие команды для:
> 
> * вывода списка БД
> * подключения к БД
> * вывода списка таблиц
> * вывода описания содержимого таблиц
> * выхода из psql

### Решение: 
```
[topper@fedora docker]$ sudo docker pull postgres:13
13: Pulling from library/postgres
...
Status: Downloaded newer image for postgres:13
docker.io/library/postgres:13

[topper@fedora docker]$ sudo docker volume create vol_postgres
vol_postgres

[topper@fedora docker]$ sudo docker run --rm --name pg-docker -e POSTGRES_PASSWORD=postgres -ti -p 5432:5432 -v vol_postgres:/var/lib/postgresql/data postgres:13
```

* вывода списка БД                    postgres=# \l
* подключения к БД                    postgres=# \c
* вывода списка таблиц                postgres-# \dt
* вывода описания содержимого таблиц  postgres-# \d[S+]
* выхода из psql                      postgres-# \q

### Задача 2
> Используя psql создайте БД ```test_database```.
> 
> Изучите бэкап БД.
> 
> Восстановите бэкап БД в ```test_database```.
> 
> Перейдите в управляющую консоль ```psql``` внутри контейнера.
> 
> Подключитесь к восстановленной БД и проведите операцию ANALYZE для сбора статистики по таблице.
> 
> Используя таблицу pg_stats, найдите столбец таблицы orders с наибольшим средним значением размера элементов в байтах.
> 
> Приведите в ответе команду, которую вы использовали для вычисления и полученный результат.
### Решение: 
```
postgres=# CREATE DATABASE test_database;
CREATE DATABASE
```
```
root@77d2d8cdbb3a:/var/lib/postgresql/data# psql -U postgres -f ./test_dump.sql test_database
SET
SET
SET
SET
SET
 set_config 
------------
 
(1 row)

SET
SET
SET
SET
SET
SET
CREATE TABLE
ALTER TABLE
CREATE SEQUENCE
ALTER TABLE
ALTER SEQUENCE
ALTER TABLE
COPY 8
 setval 
--------
      8
(1 row)

ALTER TABLE
```
```
root@77d2d8cdbb3a:/var/lib/postgresql/data# psql -h localhost -p 5432 -U postgres -W
Password: 
psql (13.7 (Debian 13.7-1.pgdg110+1))
Type "help" for help.
```
```
postgres=# \c test_database
Password: 
You are now connected to database "test_database" as user "postgres".
```
```
test_database=# \dt
         List of relations
 Schema |  Name  | Type  |  Owner   
--------+--------+-------+----------
 public | orders | table | postgres
(1 row)
```
```
test_database=# ANALYZE VERBOSE public.orders;
INFO:  analyzing "public.orders"
INFO:  "orders": scanned 1 of 1 pages, containing 8 live rows and 0 dead rows; 8 rows in sample, 8 estimated total rows
ANALYZE
```
```
test_database=# select avg_width from pg_stats where tablename='orders';
 avg_width 
-----------
         4
        16
         4
(3 rows)
```
### Задача 3
> Архитектор и администратор БД выяснили, что ваша таблица orders разрослась до невиданных размеров и поиск по ней занимает долгое время. Вам, как успешному выпускнику курсов DevOps в нетологии предложили провести разбиение таблицы на 2 (шардировать на orders_1 - price>499 и orders_2 - price<=499).
> 
> Предложите SQL-транзакцию для проведения данной операции.
> 
> Можно ли было изначально исключить "ручное" разбиение при проектировании таблицы orders?
### Решение: 
```
test_database=# alter table orders rename to orders_simple;
ALTER TABLE
test_database=# create table orders (id integer, title varchar(80), price integer) partition by range(price);
CREATE TABLE
test_database=# create table orders_less499 partition of orders for values from (0) to (499);
CREATE TABLE
test_database=# create table orders_more499 partition of orders for values from (499) to (999999999);
CREATE TABLE
test_database=# insert into orders (id, title, price) select * from orders_simple;
INSERT 0 8
```
```
test_database=# \dt
                   List of relations
 Schema |      Name      |       Type        |  Owner   
--------+----------------+-------------------+----------
 public | orders         | partitioned table | postgres
 public | orders_less499 | table             | postgres
 public | orders_more499 | table             | postgres
 public | orders_simple  | table             | postgres
(4 rows)
test_database=# select * from orders_more499;
 id |       title        | price 
----+--------------------+-------
  2 | My little database |   500
  6 | WAL never lies     |   900
  7 | Me and my bash-pet |   499
  8 | Dbiezdmin          |   501
(4 rows)

test_database=# select * from orders_less499;
 id |        title         | price 
----+----------------------+-------
  1 | War and peace        |   100
  3 | Adventure psql time  |   300
  4 | Server gravity falls |   300
  5 | Log gossips          |   123
(4 rows)
```
### Ответ:
Да можно если при изначальном проектировании таблиц сделать ее секционированной.

### Задача 4
> Используя утилиту ```pg_dump``` создайте бекап БД ```test_database```.
> 
> Как бы вы доработали бэкап-файл, чтобы добавить уникальность значения столбца ```title``` для таблиц ```test_database```?
### Решение: 
```
root@77d2d8cdbb3a:/var/lib/postgresql/data# pg_dump -U postgres test_database > ./test_database.dump
root@77d2d8cdbb3a:/var/lib/postgresql/data# ls -lha
total 8.0K
drwxr-xr-x. 1 postgres     1000   62 May 28 23:02 .
drwx------. 1 postgres postgres    2 May 28 22:11 ..
-rw-r--r--. 1 root     root     3.5K May 28 23:02 test_database.dump
-rw-r--r--. 1 postgres     1000 2.1K May 28 22:04 test_dump.sql
```
### Ответ:

Вариант 1.
```
title text UNIQUE,
```
- Ограничение UNIQUE определяет, что группа из одного или нескольких столбцов таблицы может содержать только уникальные значения.

Вариант 2.
Для уникальности можно добавить unique-индекс и UNIQUE constraints.

Создание индекса для таблицы orders поля title:

```
create unique index orders_title_uindex on orders (title);
```
Вариант 3.

Добавить параметр первичного ключа при определении поля title, заменив NOT NULL на PRIMARY KEY.
