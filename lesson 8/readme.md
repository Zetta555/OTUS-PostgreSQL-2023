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

demo=#

```
</details>

<details><summary>• Запустить pgbench -c8 -P 6 -T 60 -U postgres postgres</summary>

```shell
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
