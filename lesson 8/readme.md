# **Домашнее задание**

### Настройка autovacuum с учетом особеностей производительности
### Цель:
  
> 
>    запустить нагрузочный тест pgbench
>    
>    настроить параметры autovacuum
>
>    проверить работу autovacuum

## **Описание/Пошаговая инструкция выполнения домашнего задания:**

<details><summary>• Создать инстанс ВМ с 2 ядрами и 4 Гб ОЗУ и SSD 10GB</summary>
  
  Использую ВМ из предыдущих заданий, с подходящей конфигурацией
```shell
zetta55@ubuntu-vm2:~$ nproc
2                               #кол-во ядер в системе
zetta55@ubuntu-vm2:~$
```
</details>

<details><summary>• Установить на него PostgreSQL 15 с дефолтными настройками</summary>

```shell
zetta55@ubuntu-vm2:~$ sudo -u postgres pg_lsclusters
[sudo] password for zetta55:
Ver Cluster Port Status Owner    Data directory   Log file
15  main    5432 online postgres /mnt/10G/15/main /var/log/postgresql/postgresql-15-main.log
zetta55@ubuntu-vm2:~$ free

```
</details>

<details><summary>• Создать БД для тестов: выполнить pgbench -i postgres</summary>

  Создал тестовую базу по мануалу https://www.postgrespro.ru/education/demodb
  
```shell
zetta55@ubuntu-vm2:/mnt/10G$ ls -la
total 909280
drwxr-xr-x 4 postgres postgres      4096 мая  3 17:35 .
drwxr-xr-x 3 root     root          4096 апр 25 13:32 ..
drwxr-xr-x 3 postgres postgres      4096 апр 24 15:55 15
-rw-rw-r-- 1 postgres postgres 931068524 мая  3 17:30 demo-big-20170815.sql
drwx------ 2 postgres postgres     16384 апр 24 17:14 lost+found
zetta55@ubuntu-vm2:/mnt/10G$ sudo -u postgres psql -f demo-big-20170815.sql -U postgres
SET
psql:demo-big-20170815.sql:17: ERROR:  database "demo" does not exist
CREATE DATABASE
You are now connected to database "demo" as user "postgres".

zetta55@ubuntu-vm2:/mnt/10G$ sudo -u postgres psql demo
psql (15.2 (Ubuntu 15.2-1.pgdg22.04+1))
Type "help" for help.

demo=# \dt
               List of relations
  Schema  |      Name       | Type  |  Owner
----------+-----------------+-------+----------
 bookings | aircrafts_data  | table | postgres
 bookings | airports_data   | table | postgres
 bookings | boarding_passes | table | postgres
 bookings | bookings        | table | postgres
 bookings | flights         | table | postgres
 bookings | seats           | table | postgres
 bookings | ticket_flights  | table | postgres
 bookings | tickets         | table | postgres
(8 rows)

demo=# \q

zetta55@ubuntu-vm2:/mnt/10G$ sudo -u postgres pgbench -i demo
dropping old tables...
creating tables...
generating data (client-side)...
100000 of 100000 tuples (100%) done (elapsed 0.04 s, remaining 0.00 s)
vacuuming...
creating primary keys...
done in 0.13 s (drop tables 0.01 s, create tables 0.00 s, client-side generate 0.06 s, vacuum 0.03 s, primary keys 0.03 s).
zetta55@ubuntu-vm2:/mnt/10G$ 
```
</details>

<details><summary>• Запустить pgbench -c8 -P 6 -T 60 -U postgres postgres</summary>

  в моём случае тестирую demo
```shell
zetta55@ubuntu-vm2:/mnt/10G$ sudo -u postgres pgbench -c8 -P 6 -T 60 -U postgres demo
pgbench (15.2 (Ubuntu 15.2-1.pgdg22.04+1))
starting vacuum...end.
progress: 6.0 s, 759.3 tps, lat 10.473 ms stddev 6.615, 0 failed
progress: 12.0 s, 765.2 tps, lat 10.434 ms stddev 6.957, 0 failed
progress: 18.0 s, 764.8 tps, lat 10.437 ms stddev 7.071, 0 failed
progress: 24.0 s, 758.0 tps, lat 10.532 ms stddev 7.095, 0 failed
progress: 30.0 s, 760.0 tps, lat 10.499 ms stddev 6.753, 0 failed
progress: 36.0 s, 764.3 tps, lat 10.448 ms stddev 6.717, 0 failed
progress: 42.0 s, 764.4 tps, lat 10.442 ms stddev 6.828, 0 failed
progress: 48.0 s, 769.2 tps, lat 10.375 ms stddev 6.856, 0 failed
progress: 54.0 s, 754.5 tps, lat 10.580 ms stddev 6.655, 0 failed
progress: 60.0 s, 762.8 tps, lat 10.461 ms stddev 6.840, 0 failed
transaction type: <builtin: TPC-B (sort of)>
scaling factor: 1
query mode: simple
number of clients: 8
number of threads: 1
maximum number of tries: 1
duration: 60 s
number of transactions actually processed: 45743
number of failed transactions: 0 (0.000%)
latency average = 10.470 ms
latency stddev = 6.842 ms
initial connection time = 12.589 ms
tps = 762.280387 (without initial connection time)
zetta55@ubuntu-vm2:/mnt/10G$

```
</details>

<details><summary>• Применить параметры настройки PostgreSQL из прикрепленного к материалам занятия файла</summary>

  Применяю настройки из файла.
```shell
zetta55@ubuntu-vm2:~$ sudo -u postgres psql demo
could not change directory to "/home/zetta55": Permission denied
psql (15.2 (Ubuntu 15.2-1.pgdg22.04+1))
Type "help" for help.

demo=# ALTER SYSTEM SET max_connections TO '40';
ALTER SYSTEM
demo=# ALTER SYSTEM SET shared_buffers TO '1GB';
ALTER SYSTEM
demo=# ALTER SYSTEM SET effective_cache_size TO '3GB';
ALTER SYSTEM
demo=# ALTER SYSTEM SET maintenance_work_mem TO '512MB';
ALTER SYSTEM
demo=# ALTER SYSTEM SET checkpoint_completion_target TO '0.9';
ALTER SYSTEM
demo=# ALTER SYSTEM SET wal_buffers TO '16MB';
ALTER SYSTEM
demo=# ALTER SYSTEM SET default_statistics_target TO '500';
ALTER SYSTEM
demo=# ALTER SYSTEM SET random_page_cost TO '4';
ALTER SYSTEM
demo=# ALTER SYSTEM SET effective_io_concurrency TO '2';
ALTER SYSTEM
demo=# ALTER SYSTEM SET work_mem TO '6553kB';
ALTER SYSTEM
demo=# ALTER SYSTEM SET min_wal_size TO '4GB';
ALTER SYSTEM
demo=# ALTER SYSTEM SET max_wal_size TO '16GB';
ALTER SYSTEM
demo=# \q
zetta55@ubuntu-vm2:~$ sudo pg_ctlcluster 15 main restart

zetta55@ubuntu-vm2:~$ sudo -u postgres psql -c "select pg_reload_conf();"
could not change directory to "/home/zetta55": Permission denied
 pg_reload_conf
----------------
 t
(1 row)

zetta55@ubuntu-vm2:~$

```
  Также достаточно было бы добавить все значения из файлика в конец конфига /etc/postgresql/15/main/postgresql.conf, так как устанавливается значение из последней считанной строки и рестартануть кластер или выполнить select pg_reload_conf(); - но этот запрос не все изменившиеся параметры может подкхватить.
</details>

<details><summary>• Протестировать заново</summary>

```shell
zetta55@ubuntu-vm2:~$ sudo -u postgres pgbench -c8 -P 6 -T 60 -U postgres demo
pgbench (15.2 (Ubuntu 15.2-1.pgdg22.04+1))
starting vacuum...end.
progress: 6.0 s, 765.1 tps, lat 10.404 ms stddev 5.830, 0 failed
progress: 12.0 s, 763.7 tps, lat 10.458 ms stddev 6.016, 0 failed
progress: 18.0 s, 758.7 tps, lat 10.517 ms stddev 5.940, 0 failed
progress: 24.0 s, 763.3 tps, lat 10.459 ms stddev 5.958, 0 failed
progress: 30.0 s, 766.5 tps, lat 10.417 ms stddev 5.699, 0 failed
progress: 36.0 s, 748.8 tps, lat 10.657 ms stddev 6.058, 0 failed
progress: 42.0 s, 762.7 tps, lat 10.463 ms stddev 5.854, 0 failed
progress: 48.0 s, 718.2 tps, lat 11.122 ms stddev 6.167, 0 failed
progress: 54.0 s, 741.0 tps, lat 10.780 ms stddev 6.039, 0 failed
progress: 60.0 s, 732.5 tps, lat 10.895 ms stddev 6.106, 0 failed
transaction type: <builtin: TPC-B (sort of)>
scaling factor: 1
query mode: simple
number of clients: 8
number of threads: 1
maximum number of tries: 1
duration: 60 s
number of transactions actually processed: 45131
number of failed transactions: 0 (0.000%)
latency average = 10.613 ms
latency stddev = 5.970 ms
initial connection time = 11.247 ms
tps = 752.165181 (without initial connection time)
zetta55@ubuntu-vm2:~$
```
</details>

<details><summary>• Что изменилось и почему?</summary>

  Практически без изменений, tps на прежнем уровне.
  
</details>

<details><summary>• Создать таблицу с текстовым полем и заполнить случайными или сгенерированными данным в размере 1млн строк</summary>

  Создаю таблицу с одним столбцом текстовых полей.
```shell
demo=# CREATE TABLE tmp_demo (col1 text);
CREATE TABLE
demo=#
```
  
  Смотрю размер таблицы после её создания 
```shell
demo=# \dt+ tmp_demo
                                        List of relations
  Schema  |   Name   | Type  |  Owner   | Persistence | Access method |    Size    | Description
----------+----------+-------+----------+-------------+---------------+------------+-------------
 bookings | tmp_demo | table | postgres | permanent   | heap          | 8192 bytes |
(1 row)

demo=#
```
  
  Смотрю кол-во строк в таблице после её создания
```shell
demo=# SELECT * FROM tmp_demo;
 col1
------
(0 rows)

demo=#
```
  
  Добавляю 1М строк с произвольным содержимым
```shell
demo=# INSERT INTO tmp_demo(col1) SELECT md5(random()::text) FROM generate_series(1,1000000);
INSERT 0 1000000
  
demo=# SELECT * FROM tmp_demo LIMIT 10;
               col1
----------------------------------
 0a738130309bf42971fbb5f31fcb401a
 e8d655f09c4213651ed902b7291959b6
 58fe6f29a07a4ee9f933db7ec12222b5
 0f7d2c5b6d365d7e55929c82eae91b73
 b05e241fc5427118cc7f7ac40654eff5
 f51b1e74da5f171944c565fc4b17058b
 b89b2125d43f779ebbee91b85067cc50
 8ff76293dcb33c3f343d342af4c42cab
 216076b5c19d3debe63664564ca93600
 8572eb89a8be056e4701d2b67a3533f6
(10 rows)

demo=# SELECT count(*) FROM tmp_demo;
  count
---------
 1000000
(1 row)

demo=#

```
</details>

<details><summary>• Посмотреть размер файла с таблицей</summary>

```shell
demo=# \dt+ tmp_demo
                                     List of relations
  Schema  |   Name   | Type  |  Owner   | Persistence | Access method | Size  | Description
----------+----------+-------+----------+-------------+---------------+-------+-------------
 bookings | tmp_demo | table | postgres | permanent   | heap          | 65 MB |
(1 row)

demo=#
```
</details>

<details><summary>• 5 раз обновить все строчки и добавить к каждой строчке любой символ</summary>
  
  Обновляю все строчки, добавляю к каждой строке +a, смотрю размер таблицы, кол-во строк, изменившееся содержимое. 5 раз.
```shell
demo=# UPDATE tmp_demo SET col1 = CONCAT(col1, '+a');  -- 1-ый шаг.
UPDATE 1000000
demo=# SELECT count(*) FROM tmp_demo;
  count
---------
 1000000
(1 row)

demo=# \dt+ tmp_demo
                                      List of relations
  Schema  |   Name   | Type  |  Owner   | Persistence | Access method |  Size  | Description
----------+----------+-------+----------+-------------+---------------+--------+-------------
 bookings | tmp_demo | table | postgres | permanent   | heap          | 130 MB |
(1 row)

demo=# SELECT * FROM tmp_demo LIMIT 10;
                 col1
--------------------------------------
 c967cf8a9dae164797f043c277832bab+a
 4eb7bd3e5dc2112c9d8c2d411f198b21+a
 1456eac84cdb2d2f76a87394ff2787a3+a
 d3755c14e076f8aec709cf84d6d557f7+a
 c557eff077a0d1370be183abbfa9eae6+a
 bbbd565230287e711c4fbd7c86d3cfdd+a
 32334df5a18da6098ec00885fa24bafa+a
 ba5e28049b3bd5920ece566c51069cdf+a
 e727ce258d25df74a4e3eca94e12a59a+a
 77097c664d9dada45a3ef4934f475738+a
(10 rows)

demo=# UPDATE tmp_demo SET col1 = CONCAT(col1, '+a');  -- 2-ой шаг.
UPDATE 1000000
demo=# SELECT count(*) FROM tmp_demo;
  count
---------
 1000000
(1 row)

demo=# \dt+ tmp_demo
                                      List of relations
  Schema  |   Name   | Type  |  Owner   | Persistence | Access method |  Size  | Description
----------+----------+-------+----------+-------------+---------------+--------+-------------
 bookings | tmp_demo | table | postgres | permanent   | heap          | 130 MB |
(1 row)

demo=# SELECT * FROM tmp_demo LIMIT 5;
                 col1
--------------------------------------
 c967cf8a9dae164797f043c277832bab+a+a
 4eb7bd3e5dc2112c9d8c2d411f198b21+a+a
 1456eac84cdb2d2f76a87394ff2787a3+a+a
 d3755c14e076f8aec709cf84d6d557f7+a+a
 c557eff077a0d1370be183abbfa9eae6+a+a
(5 rows)

demo=# UPDATE tmp_demo SET col1 = CONCAT(col1, '+a');  -- 3-ий шаг.
UPDATE 1000000
demo=# \dt+ tmp_demo
                                      List of relations
  Schema  |   Name   | Type  |  Owner   | Persistence | Access method |  Size  | Description
----------+----------+-------+----------+-------------+---------------+--------+-------------
 bookings | tmp_demo | table | postgres | permanent   | heap          | 130 MB |
(1 row)

demo=# SELECT count(*) FROM tmp_demo;
  count
---------
 1000000
(1 row)

demo=# SELECT * FROM tmp_demo LIMIT 5;
                  col1
----------------------------------------
 c967cf8a9dae164797f043c277832bab+a+a+a
 4eb7bd3e5dc2112c9d8c2d411f198b21+a+a+a
 1456eac84cdb2d2f76a87394ff2787a3+a+a+a
 d3755c14e076f8aec709cf84d6d557f7+a+a+a
 c557eff077a0d1370be183abbfa9eae6+a+a+a
(5 rows)

demo=# UPDATE tmp_demo SET col1 = CONCAT(col1, '+a');  -- 4-ый шаг.
UPDATE 1000000
demo=# \dt+ tmp_demo
                                      List of relations
  Schema  |   Name   | Type  |  Owner   | Persistence | Access method |  Size  | Description
----------+----------+-------+----------+-------------+---------------+--------+-------------
 bookings | tmp_demo | table | postgres | permanent   | heap          | 138 MB |
(1 row)

demo=# SELECT count(*) FROM tmp_demo;
  count
---------
 1000000
(1 row)

demo=# SELECT * FROM tmp_demo LIMIT 5;
                   col1
------------------------------------------
 2604898253790f706189a0a919d7688e+a+a+a+a
 b11347dd72a504dfcc9af4aed41b0a74+a+a+a+a
 4e080801e004b7bc7c1376acfd1c1b48+a+a+a+a
 2aef04dc9bbf1adc5b2489fdfa0a0ec8+a+a+a+a
 7256e2ef7f7c66ab75f59ca1733cd7bc+a+a+a+a
(5 rows)

demo=# UPDATE tmp_demo SET col1 = CONCAT(col1, '+a');  -- 5-ый шаг.
UPDATE 1000000
demo=# SELECT count(*) FROM tmp_demo;
  count
---------
 1000000
(1 row)

demo=# \dt+ tmp_demo
                                      List of relations
  Schema  |   Name   | Type  |  Owner   | Persistence | Access method |  Size  | Description
----------+----------+-------+----------+-------------+---------------+--------+-------------
 bookings | tmp_demo | table | postgres | permanent   | heap          | 211 MB |
(1 row)

demo=# SELECT * FROM tmp_demo LIMIT 5;
                    col1
--------------------------------------------
 c967cf8a9dae164797f043c277832bab+a+a+a+a+a
 4eb7bd3e5dc2112c9d8c2d411f198b21+a+a+a+a+a
 1456eac84cdb2d2f76a87394ff2787a3+a+a+a+a+a
 d3755c14e076f8aec709cf84d6d557f7+a+a+a+a+a
 c557eff077a0d1370be183abbfa9eae6+a+a+a+a+a
(5 rows)

demo=#
```
</details>

<details><summary>• Посмотреть количество мертвых строчек в таблице и когда последний раз приходил автовакуум</summary>

```shell
```
</details>

<details><summary>• Подождать некоторое время, проверяя, пришел ли автовакуум</summary>

```shell
```
</details>

<details><summary>• 5 раз обновить все строчки и добавить к каждой строчке любой символ</summary>

```shell
```
</details>

<details><summary>• Посмотреть размер файла с таблицей</summary>

```shell
```
</details>

<details><summary>• Отключить Автовакуум на конкретной таблице</summary>

```shell
```
</details>

<details><summary>• 10 раз обновить все строчки и добавить к каждой строчке любой символ</summary>

```shell
```
</details>

<details><summary>• Посмотреть размер файла с таблицей</summary>

```shell
```
</details>

<details><summary>• Объясните полученный результат</summary>

```shell
```
</details>

<details><summary>• Не забудьте включить автовакуум)</summary>

```shell
```
</details>

<details><summary>• Задание со *:</summary>

```shell
```
</details>

<details><summary>• Написать анонимную процедуру, в которой в цикле 10 раз обновятся все строчки в искомой таблице. Не забыть вывести номер шага цикла.</summary>

```shell
```
</details>
