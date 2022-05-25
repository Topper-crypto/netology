# Домашнее задание к занятию "6.2. SQL"

### Задача 1
> Используя docker поднимите инстанс PostgreSQL (версию 12) c 2 volume, в который будут складываться данные БД и бэкапы.
>
> Приведите получившуюся команду или docker-compose манифест.
### Решение:
```
[topper@fedora ~]$ sudo docker pull postgres:12
[topper@fedora ~]$ sudo docker volume create vol1
vol1
[topper@fedora ~]$ sudo docker volume create vol2
vol2
[topper@fedora ~]$ sudo docker run --rm --name pg-docker -e POSTGRES_PASSWORD=postgres -ti -p 5432:5432 -v vol1:/var/lib/postgresql/data -v vol2:/var/lib/postgresql postgres:12
```
![](https://github.com/Topper-crypto/netology/blob/main/assets/psql.png)

### Задача 2
> В БД из задачи 1:
>
> * создайте пользователя test-admin-user и БД test_db
> * в БД test_db создайте таблицу orders и clients (спeцификация таблиц ниже)
> * предоставьте привилегии на все операции пользователю test-admin-user на таблицы БД test_db
> * создайте пользователя test-simple-user
> * предоставьте пользователю test-simple-user права на SELECT/INSERT/UPDATE/DELETE данных таблиц БД test_db
> 
> Таблица orders:> 
> * id (serial primary key)
> * наименование (string)
> * цена (integer)
> 
> Таблица clients:
> * id (serial primary key)
> * фамилия (string)
> * страна проживания (string, index)
> * заказ (foreign key orders)
> 
> Приведите:
> * итоговый список БД после выполнения пунктов выше,
> * описание таблиц (describe)
> * SQL-запрос для выдачи списка пользователей с правами над таблицами test_db
> * список пользователей с правами над таблицами test_db
### Решение: 
```
postgres=# CREATE DATABASE test_db;
CREATE DATABASE
```
```
postgres=# CREATE ROLE "test-admin-user" SUPERUSER NOCREATEDB NOCREATEROLE NOINHERIT LOGIN;
CREATE ROLE
```
```
test_db=# CREATE TABLE orders 
(
id integer, 
name text, 
price integer, 
PRIMARY KEY (id) 
);
CREATE TABLE
```
```
test_db=# CREATE TABLE clients 
(
        id integer PRIMARY KEY,
        lastname text,
        country text,
        booking integer,
        FOREIGN KEY (booking) REFERENCES orders (Id)
);
CREATE TABLE
```
```
postgres=# CREATE ROLE "test-simple-user" NOSUPERUSER NOCREATEDB NOCREATEROLE NOINHERIT LOGIN;
CREATE ROLE
```
```
test_db=# GRANT SELECT ON TABLE public.clients TO "test-simple-user";
GRANT
test_db=# GRANT INSERT ON TABLE public.clients TO "test-simple-user";
GRANT
test_db=# GRANT UPDATE ON TABLE public.clients TO "test-simple-user";
GRANT
test_db=# GRANT DELETE ON TABLE public.clients TO "test-simple-user";
GRANT
test_db=# GRANT SELECT ON TABLE public.orders TO "test-simple-user";
GRANT
test_db=# GRANT INSERT ON TABLE public.orders TO "test-simple-user";
GRANT
test_db=# GRANT UPDATE ON TABLE public.orders TO "test-simple-user";
GRANT
test_db=# GRANT DELETE ON TABLE public.orders TO "test-simple-user";
GRANT
```

![](https://github.com/Topper-crypto/netology/blob/main/assets/psql6.png)

![](https://github.com/Topper-crypto/netology/blob/main/assets/psql2.png)

![](https://github.com/Topper-crypto/netology/blob/main/assets/psql4.png)

![](https://github.com/Topper-crypto/netology/blob/main/assets/psql3.png)

### Задача 3
> Используя SQL синтаксис - наполните таблицы следующими тестовыми данными:
> 
> Таблица orders

| *Наименование*  | *Цена* | 
| ------------- | -------------| 
| Шоколад  | 10 | 
| Принтер  | 3000 | 
| Книга  | 500 | 
| Монитор  | 7000 | 
| Гитара  | 4000 | 

> Таблица clients

| *ФИО*  | *Страна проживания* | 
| ------------- | -------------| 
| Иванов Иван Иванович  | USA | 
| Петров Петр Петрович  | Canada | 
| Иоганн Себастьян Бах  | Japan | 
| Ронни Джеймс Дио  | Russia | 
| Ritchie Blackmore  | Russia | 
 
> Используя SQL синтаксис:
> 
> * вычислите количество записей для каждой таблицы
> * приведите в ответе:
>      * запросы
>      * результаты их выполнения.

### Решение:
```
test_db=# insert into orders VALUES (1, 'Шоколад', 10), (2, 'Принтер', 3000), (3, 'Книга', 500), (4, 'Монитор', 7000), (5, 'Гитара', 4000);
INSERT 0 5
```
```
test_db=# insert into clients VALUES (1, 'Иванов Иван Иванович', 'USA'), (2, 'Петров Петр Петрович', 'Canada'), (3, 'Иоганн Себастьян Бах', 'Japan'), (4, 'Ронни Джеймс Дио', 'Russia'), (5, 'Ritchie Blackmore', 'Russia');
INSERT 0 5
```

![](https://github.com/Topper-crypto/netology/blob/main/assets/psql5.png)

### Задача 4
> Часть пользователей из таблицы clients решили оформить заказы из таблицы orders.
> 
> Используя foreign keys свяжите записи из таблиц, согласно таблице:
> 

| *ФИО*  | *Заказ* | 
| ------------- | -------------| 
| Иванов Иван Иванович  | Книга | 
| Петров Петр Петрович  | Монитор | 
| Иоганн Себастьян Бах  | Гитара | 

> Приведите SQL-запросы для выполнения данных операций.
> 
> Приведите SQL-запрос для выдачи всех пользователей, которые совершили заказ, а также вывод данного запроса.
> 
> Подсказк - используйте директиву ```UPDATE```.

### Решение:
```
test_db=# UPDATE clients SET "booking" = (SELECT id FROM orders WHERE "name"='Книга') WHERE "lastname"='Иванов Иван Иванович';
UPDATE 1
test_db=# UPDATE clients SET "booking" = (SELECT id FROM orders WHERE "name"='Монитор') WHERE "lastname"='Петров Петр Петрович';
UPDATE 1
test_db=# UPDATE clients SET "booking" = (SELECT id FROM orders WHERE "name"='Гитара') WHERE "lastname"='Иоганн Себастьян Бах';
UPDATE 1
```
![](https://github.com/Topper-crypto/netology/blob/main/assets/psql7.png)


### Задача 5
> Получите полную информацию по выполнению запроса выдачи всех пользователей из задачи 4 (используя директиву EXPLAIN).
> 
> Приведите получившийся результат и объясните что значат полученные значения.

### Решение:

![](https://github.com/Topper-crypto/netology/blob/main/assets/psql8.png)

При запуске explain Postgres выводит примерный план выполнения запроса и для каждой операции предположит:
* сколько процессорного времени уйдёт на поиск первой записи и сбор всей выборки: cost=первая_запись..вся_выборка
* сколько примерно будет строк: rows
* какой будет средняя длина строки в байтах: width

Вариант1.

1. Построчно прочитана таблица orders
2. Создан кеш по полю id для таблицы orders
3. Прочитана таблица clients
4. Для каждой строки по полю "booking" будет проверено, соответствует ли она чему-то в кеше orders
    * если соответствия нет - строка будет пропущена
    * если соответствие есть, то на основе этой строки и всех подходящих строках кеша СУБД сформирует вывод

### Задача 6
> Создайте бэкап БД test_db и поместите его в volume, предназначенный для бэкапов (см. Задачу 1).
> 
> Остановите контейнер с PostgreSQL (но не удаляйте volumes).
> 
> Поднимите новый пустой контейнер с PostgreSQL.
> 
> Восстановите БД test_db в новом контейнере.
> 
> Приведите список операций, который вы применяли для бэкапа данных и восстановления.

### Решение:
