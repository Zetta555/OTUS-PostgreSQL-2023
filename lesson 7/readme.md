# **Домашнее задание**

### Работа с базами данных, пользователями и правами
### Цель:
  
> 
>    создание новой базы данных, схемы и таблицы
>    
>    создание роли для чтения данных из созданной схемы созданной базы данных
>
>    создание роли для чтения и записи из созданной схемы созданной базы данных

## **Описание/Пошаговая инструкция выполнения домашнего задания:**

<details><summary>• создайте новый кластер PostgresSQL 14</summary>

```shell
zetta55@ubuntu-vm3:~$ sudo apt install postgresql-14
...
...
fixing permissions on existing directory /var/lib/postgresql/14/main ... ok
creating subdirectories ... ok
selecting dynamic shared memory implementation ... posix
selecting default max_connections ... 100
selecting default shared_buffers ... 128MB
selecting default time zone ... Europe/Moscow
creating configuration files ... ok
running bootstrap script ... ok
performing post-bootstrap initialization ... ok
syncing data to disk ... ok
update-alternatives: using /usr/share/postgresql/14/man/man1/postmaster.1.gz to provide /usr/share/man/man1/postmaster.1.gz (postmaster.1.gz) in auto mode
Processing triggers for man-db (2.10.2-1) ...
Processing triggers for libc-bin (2.35-0ubuntu3.1) ...
zetta55@ubuntu-vm3:~$
```
Проверяю установку postgres-a
```shell
zetta55@ubuntu-vm3:~$ sudo systemctl status postgresql
● postgresql.service - PostgreSQL RDBMS
     Loaded: loaded (/lib/systemd/system/postgresql.service; enabled; vendor preset: enabled)
     Active: active (exited) since Mon 2023-05-01 15:26:26 MSK; 3min 21s ago
    Process: 35449 ExecStart=/bin/true (code=exited, status=0/SUCCESS)
   Main PID: 35449 (code=exited, status=0/SUCCESS)
        CPU: 1ms
мая 01 15:26:26 ubuntu-vm3 systemd[1]: Starting PostgreSQL RDBMS...
мая 01 15:26:26 ubuntu-vm3 systemd[1]: Finished PostgreSQL RDBMS.

zetta55@ubuntu-vm3:~$ sudo pg_config --version
PostgreSQL 14.7 (Ubuntu 14.7-0ubuntu0.22.04.1)

zetta55@ubuntu-vm3:~$ sudo -u postgres pg_lsclusters
Ver Cluster Port Status Owner    Data directory              Log file
14  main    5432 online postgres /var/lib/postgresql/14/main /var/log/postgresql/postgresql-14-main.log
zetta55@ubuntu-vm3:~$
```
ок
</details>

<details><summary>• зайдите в созданный кластер под пользователем postgres</summary>

```shell
zetta55@ubuntu-vm3:~$ sudo -u postgres psql
could not change directory to "/home/zetta55": Permission denied
psql (14.7 (Ubuntu 14.7-0ubuntu0.22.04.1))
Type "help" for help.

postgres=#
```
</details>

<details><summary>• создайте новую базу данных testdb</summary>

```shell
postgres=# CREATE DATABASE testdb;
CREATE DATABASE
postgres=#

```
</details>

<details><summary>• зайдите в созданную базу данных под пользователем postgres</summary>

```shell
zetta55@ubuntu-vm3:~$ sudo -u postgres psql testdb
[sudo] password for zetta55:
could not change directory to "/home/zetta55": Permission denied
psql (14.7 (Ubuntu 14.7-0ubuntu0.22.04.1))
Type "help" for help.

testdb=#

```
</details>

<details><summary>• создайте новую схему testnm</summary>

```shell
testdb=# CREATE SCHEMA testnm;
CREATE SCHEMA
testdb=#

```
</details>

<details><summary>• создайте новую таблицу t1 с одной колонкой c1 типа integer</summary>

```shell
testdb=# CREATE TABLE t1(c1 integer);
CREATE TABLE
testdb=#

```
</details>

<details><summary>• вставьте строку со значением c1=1</summary>

```shell
testdb=# INSERT INTO t1 values(1);
INSERT 0 1
testdb=#

```
</details>

<details><summary>• создайте новую роль readonly</summary>

```shell
testdb=# CREATE ROLE readonly;
CREATE ROLE
testdb=#

```
</details>

<details><summary>• дайте новой роли право на подключение к базе данных testdb</summary>

```shell
```
</details>

<details><summary>• дайте новой роли право на использование схемы testnm</summary>

```shell
```
</details>

<details><summary>• дайте новой роли право на select для всех таблиц схемы testnm</summary>

```shell
```
</details>

<details><summary>• создайте пользователя testread с паролем test123</summary>

```shell
```
</details>

<details><summary>• дайте роль readonly пользователю testread</summary>

```shell
```
</details>

<details><summary>• зайдите под пользователем testread в базу данных testdb</summary>

```shell
```
</details>

<details><summary>• сделайте select * from t1;</summary>

```shell
```
</details>

<details><summary>• получилось? (могло если вы делали сами не по шпаргалке и не упустили один существенный момент про который позже)</summary>

```shell
```
</details>

<details><summary>• напишите что именно произошло в тексте домашнего задания</summary>

```shell
```
</details>

<details><summary>• у вас есть идеи почему? ведь права то дали?</summary>

```shell
```
</details>

<details><summary>• посмотрите на список таблиц</summary>

```shell
```
</details>

<details><summary>• подсказка в шпаргалке под пунктом 20</summary>

```shell
```
</details>

<details><summary>• а почему так получилось с таблицей (если делали сами и без шпаргалки то может у вас все нормально)</summary>

```shell
```
</details>

<details><summary>• вернитесь в базу данных testdb под пользователем postgres</summary>

```shell
```
</details>

<details><summary>• удалите таблицу t1</summary>

```shell
```
</details>

<details><summary>• создайте ее заново но уже с явным указанием имени схемы testnm</summary>

```shell
```
</details>

<details><summary>• вставьте строку со значением c1=1</summary>

```shell
```
</details>

<details><summary>• зайдите под пользователем testread в базу данных testdb</summary>

```shell
```
</details>

<details><summary>• сделайте select * from testnm.t1;</summary>

```shell
```
</details>

<details><summary>• получилось?</summary>

```shell
```
</details>

<details><summary>• есть идеи почему? если нет - смотрите шпаргалку</summary>

```shell
```
</details>

<details><summary>• как сделать так чтобы такое больше не повторялось? если нет идей - смотрите шпаргалку</summary>

```shell
```
</details>

<details><summary>• сделайте select * from testnm.t1;</summary>

```shell
```
</details>

<details><summary>• получилось?</summary>

```shell
```
</details>

<details><summary>• есть идеи почему? если нет - смотрите шпаргалку</summary>

```shell
```
</details>

<details><summary>• сделайте select * from testnm.t1;</summary>

```shell
```
</details>

<details><summary>• получилось?</summary>

```shell
```
</details>

<details><summary>• ура!</summary>

```shell
```
</details>

<details><summary>• теперь попробуйте выполнить команду create table t2(c1 integer); insert into t2 values (2);</summary>

```shell
```
</details>

<details><summary>• а как так? нам же никто прав на создание таблиц и insert в них под ролью readonly?</summary>

```shell
```
</details>

<details><summary>• есть идеи как убрать эти права? если нет - смотрите шпаргалку</summary>

```shell
```
</details>

<details><summary>• если вы справились сами то расскажите что сделали и почему, если смотрели шпаргалку - объясните что сделали и почему выполнив указанные в ней команды</summary>

```shell
```
</details>

<details><summary>• теперь попробуйте выполнить команду create table t3(c1 integer); insert into t2 values (2);</summary>

```shell
```
</details>

<details><summary>• расскажите что получилось и почему </summary>

```shell
```
</details>
