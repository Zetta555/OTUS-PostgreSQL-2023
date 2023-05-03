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

```shell
```
</details>

<details><summary>• Протестировать заново</summary>

```shell
```
</details>

<details><summary>• Что изменилось и почему?</summary>

```shell
```
</details>

<details><summary>• Создать таблицу с текстовым полем и заполнить случайными или сгенерированными данным в размере 1млн строк</summary>

```shell
```
</details>

<details><summary>• Посмотреть размер файла с таблицей</summary>

```shell
```
</details>

<details><summary>• 5 раз обновить все строчки и добавить к каждой строчке любой символ</summary>

```shell
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
