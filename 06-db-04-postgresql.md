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

### Задача 4
> Используя утилиту ```pg_dump``` создайте бекап БД ```test_database```.
> 
> Как бы вы доработали бэкап-файл, чтобы добавить уникальность значения столбца ```title``` для таблиц ```test_database```?
### Решение: 
